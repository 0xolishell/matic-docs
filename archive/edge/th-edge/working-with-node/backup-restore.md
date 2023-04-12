---
id: backup-restore
title: การสำรอง/กู้คืนข้อมูลอินสแตนซ์ของโหนด
description: "วิธีการสำรองและกู้คืนข้อมูลอินแสตนส์ของโหนด Polygon Edge"
keywords:
  - docs
  - polygon
  - edge
  - instance
  - restore
  - directory
  - node
---

## ภาพรวม {#overview}

คู่มือนี้อธิบายรายละเอียดเกี่ยวกับการสำรองข้อมูลและกู้คืนข้อมูลอินสแตนซ์ของโหนด Polygon Edge
โดยมีการระบุโฟลเดอร์พื้นฐานและเนื้อหา ตลอดจนการอธิบายว่า ไฟล์ไหนเป็นส่วนสำคัญในการดำเนินการสำรองและกู้คืนข้อมูลที่ประสบความสำเร็จ

## โฟลเดอร์พื้นฐาน {#base-folders}

Polygon Edge ใช้ LevelDB เป็นกลไกในการจัดเก็บ
เมื่อคุณเริ่มโหนด Polygon Edge จะมีการสร้างโฟลเดอร์ย่อยดังต่อไปนี้ในไดเรกทอรีการใช้งานที่กำหนดไว้:
* **blockchain** - จัดเก็บข้อมูลบล็อกเชน
* **trie** - จัดเก็บ Merkle trie (ข้อมูลสถานะทั้งระบบ)
* **keystore** - จัดเก็บคีย์ส่วนตัวให้กับไคลเอ็นต์รวมถึงคีย์ส่วนตัว libp2p และคีย์ส่วนตัวการซีล/ตัวตรวจสอบความถูกต้อง
* **consensus** - จัดเก็บข้อมูลฉันทามติที่ไคลเอ็นต์อาจต้องใช้ในการดำเนินงานตอนนี้ โฟลเดอร์นี้จะจัดเก็บ*คีย์ของตัวตรวจสอบความถูกต้องส่วนตัว*ของโหนด

การเก็บรักษาโฟลเดอร์เหล่านี้เป็นสิ่งสำคัญในการทำให้อินสแตนซ์ Polygon Edge ทำงานได้อย่างงราบรื่น

## สร้างการสำรองข้อมูลจากโหนดที่ใช้งานอยู่และกู้คืนข้อมูลสำหรับโหนดใหม่ {#create-backup-from-a-running-node-and-restore-for-new-node}

ส่วนนี้จะแนะนำคุณตลอดการสร้างข้อมูลเก็บถาวรของบล็อกเชนในโหนดที่ใช้งานอยู่และกู้คืนข้อมูลนั้นในอินสแตนซ์อื่น

### การสำรองข้อมูล {#backup}

คำสั่ง `backup` จะดึงข้อมูลบล็อกจากโหนดที่ใช้งานอยู่โดยใช้ gRPC และสร้างไฟล์เก็บถาวรหากไม่มีการให้ `--from` และ `--to` ไว้ในคำสั่ง คำสั่งนี้จะดึงข้อมูลบล็อกตั้งแต่บล็อก genesis จนถึงบล็อกล่าสุด

```bash
$ polygon-edge backup --grpc-address 127.0.0.1:9632 --out backup.dat [--from 0x0] [--to 0x100]
```

### การกู้คืน {#restore}

เซิร์ฟเวอร์นำเข้าบล็อกจากพื้นที่เก็บถาวรตั้งแต่เริ่มต้น เมื่อมีการเริ่มด้วยค่าสถานะ `--restore`โปรดตรวจสอบว่ามีคีย์สำหรับโหนดใหม่ดูข้อมูลเพิ่มเติมเกี่ยวกับการนำเข้าหรือสร้างคีย์ได้ที่[ส่วนตัวจัดการข้อมูลลับ](/docs/edge/configuration/secret-managers/set-up-aws-ssm)

```bash
$ polygon-edge server --restore archive.dat
```

## การสำรอง/กู้คืนข้อมูลทั้งหมด {#back-up-restore-whole-data}

ส่วนนี้แนะนำคุณตลอดการสำรองข้อมูล รวมถึงคีย์และข้อมูลสถานะ และกู้คืนข้อมูลนั้นในอินสแตนซ์ใหม่

### ขั้นตอนที่ 1: หยุดไคลเอ็นต์ที่ใช้งานอยู่ {#step-1-stop-the-running-client}

เนื่องจาก Polygon Edge ใช้ **LevelDB** เพื่อจัดเก็บข้อมูล ต้องหยุดการทำงานของโหนดในระหว่างการสำรองข้อมูล
เนื่องจาก **LevelDB** ไม่อนุญาตให้เข้าถึงไฟล์ฐานข้อมูลของตนพร้อมกัน

นอกจากนี้ Polygon Edge ยังทำการ Flush ข้อมูลเมื่อมีการหยุดทำงาน

ขั้นตอนแรกจะเกี่ยวข้องกับการหยุดการทำงานของไคลเอ็นต์ที่ใช้งานอยู่ (ผ่านการใช้ตัวจัดการบริการหรือผ่านการใช้กลไกอื่นใดที่ส่งสัญญาณ SIGINT แก่การประมวลผล)
เพื่อให้สามารถทริกเกอร์อีเวนต์ 2 อย่าง ในขณะที่กำลังค่อยๆ ปิดระบบ:
* การเรียกใช้การ Flush ข้อมูลกับดิสก์
* การปล่อยไฟล์ DB ที่ LevelDB ล็อกไว้

### ขั้นตอนที่ 2: สำรองข้อมูลไดเรกทอรี {#step-2-backup-the-directory}

ตอนนี้ไคลเอ็นต์ไม่ใช้งานอยู่แล้ว เราจึงสามารถสำรองไดเรกทอรีข้อมูลในเครื่องอื่น
โปรดจำไว้ว่า ไฟล์ที่มีนามสกุล `.key` จะประกอบด้วยข้อมูลเกี่ยวกับคีย์ส่วนตัว ซึ่งสามารถนำไปใช้เพื่อแสดงตัวเป็นโหนดปัจจุบันได้
และห้ามมิให้แชร์ไฟล์นั้นกับบุคคลที่สาม/บุคคลที่ไม่ได้รู้จัก

:::info

โปรดสำรองและกู้คืนไฟล์ `genesis` ที่สร้างมาโดยตนเอง เพื่อให้โหนดที่กู้คืนมานั้นสามารถใช้งานได้อย่างเต็มที่

:::

## การกู้คืน {#restore-1}

### ขั้นตอนที่ 1: หยุดไคลเอ็นต์ที่ใช้งานอยู่ {#step-1-stop-the-running-client-1}

หากมีอินสแตนซ์ใดของ Polygon Edge กำลังทำงานอยู่ ให้หยุดใช้งานเพื่อให้ดำเนินการตามขั้นตอนที่ 2 ประสบความสำเร็จ

### ขั้นตอนที่ 2: คัดลอกไดเรกทอรีข้อมูลที่สำรองไว้ไปยังโฟลเดอร์ที่ต้องการ {#step-2-copy-the-backed-up-data-directory-to-the-desired-folder}

เมื่อไคลเอ็นต์ไม่ได้ใช้งานอยู่ จะสามารถคัดลอกไดเรกทอรีข้อมูลซึ่งมีการสำรองไว้ก่อนหน้านี้ไปยังโฟลเดอร์ที่ต้องการได้
นอกจากนี้ ให้กู้คืนไฟล์ `genesis` ที่คัดลอกไว้ก่อนหน้า

### ขั้นตอนที่ 3: เรียกใช้ ไคลเอ็นต์ Polygon Edge ในขณะระบุไดเรกทอรีข้อมูลที่ถูกต้อง {#step-3-run-the-polygon-edge-client-while-specifying-the-correct-data-directory}

เพื่อให้ Polygon Edge สามารถใช้ไดเรกทอรีข้อมูลที่กู้คืนมาได้ ในขณะเรียกใช้งาน ผู้ใช้ต้องระบุพาธไปยัง
ไดเรกทอรีข้อมูลโปรดดูข้อมูลเกี่ยวกับค่าสถานะ `data-dir` ในส่วน[คำสั่ง CLI](/docs/edge/get-started/cli-commands)