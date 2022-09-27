---
id: alchemy
title: Создание смарт-контракта с помощью Alchemy
sidebar_label: Using Alchemy
description:  Руководство по развертыванию смарт-контрактов с помощью Alchemy.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---

## Обзор {#overview}

Данное руководство предназначено для разработчиков, которые либо недавно занимаются разработкой на блокчейне Ethereum, либо хотят понять основы развертывания смарт-контрактов и взаимодействия с ними. В нем описывается процесс создания и развертывания смарт-контракта в тестовой сети Polygon Mumbai с помощью виртуального кошелька ([Metamask](https://metamask.io)), [Solidity](https://docs.soliditylang.org/en/v0.8.0/), [Hardhat](https://hardhat.org) и [Alchemy](https://alchemy.com/?a=polygon-docs).

:::note

Если у вас возникнут вопросы или проблемы, обратитесь к <ins>официальному серверу Alchemy в Discord</ins>.

:::

## Что вы узнаете {#what-you-will-learn}

Чтобы создать смарт-контракт в этом руководстве, вы узнаете, как с помощью платформы Alchemy решить следующие задачи:
- Создать приложение смарт-контракта.
- Проверить остаток в кошельке.
- Проверить вызовы контракта в обозревателе.

## Что вы сделаете {#what-you-will-do}

Следуя данному руководству, вы сможете:
1. Приступить к созданию приложения на Alchemy
2. Создать адрес кошелька с Metamask
3. Добавить остаток в кошелек
4. Использовать Hardhat и Ethers.js для компиляции и развертывания проекта
5. Проверить статус контракта на платформе Alchemy.

## Создать и развернуть ваш смарт-контракт с помощью Hardhat {#create-and-deploy-your-smart-contract-using-hardhat}

### Шаг 1 — Подключитесь к сети Polygon {#step-1-connect-to-the-polygon-network}

Существует несколько способов отправки заявок в цепочку Polygon PoS. Вместо запуска собственного нода вы будете использовать бесплатный аккаунт на платформе разработчиков Alchemy и взаимодействовать с Alchemy Polygon PoS API для связи с цепочкой Polygon PoS. Платформа включает инструментальные средства разработчика для мониторинга заявок и анализа данных, показывающих внутренний механизм развертывания смарт-контракта. Если у вас еще нет аккаунта Alchemy, начните с бесплатной регистрации [здесь](https://alchemy.com/?a=polygon-docs).

![img](/img/alchemy/alchemy-dashboard.png)

:::note

После создания аккаунта у вас есть возможность сразу же создать свое первое приложение до перехода в дашборд.

:::

### Шаг 2 — Создайте свое приложение (и ключ API) {#step-2-create-your-app-and-api-key}

После успешного создания аккаунта Alchemy вам понадобится сгенерировать ключ API путем создания приложения. Этим обеспечивается подтверждение подлинности заявок, отправляемых в тестовую сеть Polygon Mumbai.
> Если вы не знакомы с тестовыми сетями, ознакомьтесь с [этим руководством по тестовым сетям](https://docs.alchemyapi.io/guides/choosing-a-network).

Чтобы сгенерировать новый ключ API, перейдите на вкладку «Apps» (Приложения) на панели навигации дашборда Alchemy и выберите вложенную вкладку «Create App» (Создать приложение).

![img](/img/alchemy/create-app.png)

Назовите свое новое приложение «Hello World», дайте короткое описание, выберите «Polygon» в качестве цепочки и выберите «Polygon Mumbai» в качестве своей сети.

Наконец, нажмите «Create app» (Создать приложение). Ваше новое приложение должно появиться в таблице ниже.

### Шаг 3 — Создайте адрес кошелька {#step-3-create-a-wallet-address}

Поскольку Polygon PoS является решением второго уровня для масштабирования Ethereum, нам необходимо получить кошелек Ethereum и добавить пользовательский URL Polygon для отправки и получения транзакций в тестовой сети Polygon Mumbai. В этом руководстве мы будем использовать MetaMask, совместимый с браузером цифровой кошелек, используемый для управления адресом вашего кошелька. Если вы хотите лучше разобраться в том, как работают транзакции на Ethereum, ознакомьтесь с [данным руководством по транзакциям](https://ethereum.org/en/developers/docs/transactions/), подготовленным Ethereum Foundation.

Чтобы получить URL пользовательского Polygon RPC от Alchemy, перейдите в свое приложение «Hello World» в дашборде Alchemy и нажмите «View Key» (Посмотреть ключ) в верхнем правом углу. Затем скопируйте свой ключ Alchemy HTTP API.

![img](/img/alchemy/view-key.png)

Вы можете бесплатно загрузить и создать аккаунт Metamask [здесь](https://metamask.io/download.html). После создания аккаунта выполните эти шаги для настройки сети Polygon на своем кошельке.

1. Выберите «Settings» (Настройки) из раскрывающегося меню в верхнем правом углу своего кошелька Metamask.
2. Выберите «Networks» (Сети) из меню с левой стороны.
3. Подключите свой кошелек к тестовой сети Mumbai, используя следующие параметры.

#### Имя сети: Тестовая сеть Polygon Mumbai {#network-name-polygon-mumbai-testnet}

#### URL нового RPC: https://polygon-mumbai.g.alchemy.com/v2/your-api-key {#new-rpc-url-https-polygon-mumbai-g-alchemy-com-v2-your-api-key}

#### Идентификатор цепочки: 80001 {#chainid-80001}

#### Символ: MATIC {#symbol-matic}

#### URL обозревателя блоков: https://mumbai.polygonscan.com/ {#block-explorer-url-https-mumbai-polygonscan-com}


### Шаг 4 — Добавьте тестовый MATIC Polygon Mumbai из Faucet {#step-4-add-polygon-mumbai-test-matic-from-a-faucet}

Чтобы развернуть смарт-контракт в тестовой сети, вам необходимо получить несколько токенов тестовой сети. Чтобы получить токены тестовой сети, посетите [Polygon Mumbai Faucet](https://faucet.polygon.technology/), выберите «Mumbai», выберите «MATIC Token» и введите адрес вашего кошелька Polygon, затем нажмите «Submit» (Отправить). Получение токенов тестовой сети может занять некоторое время из-за сетевого трафика.

![img](/img/alchemy/faucet.png)

Вскоре после этого вы увидите токены тестовой сети в своем аккаунте MetaMask.

### Шаг 5 — Проверьте остаток {#step-5-check-your-balance}

Чтобы убедиться в наличии остатка, давайте отправим заявку [eth\_getBalance](https://docs.alchemy.com/reference/eth-getbalance-polygon), воспользовавшись [компоновщиком Alchemy](https://composer.alchemyapi.io/). Выберите «Polygon» в качестве цепочки, «Polygon Mumbai» в качестве сети, «eth_getBalance» в качестве метода, и введите свой адрес. В результате будет возвращено количество токенов MATIC в нашем кошельке. Посмотрите [это видео](https://youtu.be/r6sjRxBZJuU), в котором приводятся инструкции по использованию компоновщика.

![img](/img/alchemy/get-balance.png)

После того, как вы введете адрес своего аккаунта Metamask и нажмете «Send Request» (Отправить заявку), вы должны увидеть отклик, который будет выглядеть следующим образом:

```
{ "jsonrpc": "2.0", "id": 0, "result": "0xde0b6b3a7640000" }
```

:::note

Этот результат представлен в Wei, а не в ETH. Wei используется в качестве наименьшей единицы Ether. Конвертация из Wei в Ether: 1 Ether = 10^18 Wei. Таким образом, при преобразовании «0xde0b6b3a7640000» в десятичное число мы получаем 1\*10^18, что равно 1 ETH. Это можно сопоставить с 1 MATIC, если исходить из номинала.

:::

### Шаг 6 — Инициализируйте проект {#step-6-initialize-your-project}

Во-первых, необходимо будет создать папку для нашего проекта. Перейдите в [командную строку](https://www.computerhope.com/jargon/c/commandi.htm) и введите:

```
mkdir hello-world
cd hello-world
```

Теперь, находясь в папке проекта, используем `npm init`, чтобы инициализировать проект. Если у вас еще не установлен npm, следуйте [этим инструкциям](https://docs.alchemyapi.io/alchemy/guides/alchemy-for-macs#1-install-nodejs-and-npm) (нам также понадобится Node.js, так что загрузите его тоже).

```bash
npm init # (or npm init --yes)
```

Неважно, как вы ответите на вопросы в ходе установки; ниже для справки приводятся наши ответы:

```
package name: (hello-world)
version: (1.0.0)
description: hello world smart contract
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)

About to write to /Users/.../.../.../hello-world/package.json:

{   
   "name": "hello-world",
   "version": "1.0.0",
   "description": "hello world smart contract",
   "main": "index.js",
   "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
   },
   "author": "",
   "license": "ISC"
}
```

Утвердите package.json. Теперь можно начинать!

### Шаг 7 — Загрузите [Hardhat](https://hardhat.org/getting-started/#overview)

Hardhat — это среда разработки для компилирования, развертывания, тестирования и отладки программного обеспечения Ethereum. Она помогает разрабатывать смарт-контракты и децентрализованные приложения локально перед их развертыванием в активной цепочке.

Внутри нашего проекта `hello-world` выполните команду:

```
npm install --save-dev hardhat
```

Ознакомьтесь с этой страницей, где представлены более подробные [инструкции по установке](https://hardhat.org/getting-started/#overview).

### Шаг 8 — Создайте проект Hardhat {#step-8-create-hardhat-project}

Внутри нашей папки проекта `hello-world` выполните команду:

```
npx hardhat
```

После этого вы должны увидеть приветственное сообщение и варианты выбора желаемых действий. Выберите «create an empty hardhat.config.js»:

```
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.0.11 👷‍

What do you want to do? …
Create a sample project
❯ Create an empty hardhat.config.js
Quit
```

В результате будет сгенерирован файл `hardhat.config.js`, в котором будут заданы все настройки для нашего проекта (на шаге 13).

### Шаг 9 — Добавьте папки проекта {#step-9-add-project-folders}

Для оптимальной организации проекта создадим две новых папки. Перейдите в корневой каталог вашего проекта `hello-world` в командной строке и введите:

```
mkdir contracts
mkdir scripts
```

* `contracts/` — в этой папке мы будем хранить файл с кодом смарт-контракта hello world
* `scripts/` — в этой папке мы будем хранить скрипты для развертывания нашего контракта и взаимодействия с ним

### Шаг 10 — Напишите контракт {#step-10-write-the-contract}

Откройте проект hello-world в предпочтительном редакторе, например в [VSCode](https://code.visualstudio.com). Смарт-контракты пишутся на языке Solidity, и именно его мы будем использовать для написания нашего смарт-контракта HelloWorld.sol.

1. Перейдите в папку «contracts» и создайте новый файл с именем `HelloWorld.sol`
2. Ниже представлен образец смарт-контракта Hello World от [Ethereum Foundation](https://ethereum.org/en/), который мы будем использовать в этом руководстве. Скопируйте и вставьте приведенные ниже строки кода в ваш файл `HelloWorld.sol` и обязательно прочитайте комментарии, чтобы понять задачи, решаемые этим контрактом:

```
// SPDX-License-Identifier: None

// Specifies the version of Solidity, using semantic versioning.
// Learn more: https://solidity.readthedocs.io/en/v0.5.10/layout-of-source-files.html#pragma
pragma solidity >=0.8.9;

// Defines a contract named `HelloWorld`.
// A contract is a collection of functions and data (its state). Once deployed, a contract resides at a specific address on the Ethereum blockchain. Learn more: https://solidity.readthedocs.io/en/v0.5.10/structure-of-a-contract.html
contract HelloWorld {

   //Emitted when update function is called
   //Smart contract events are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen.
   event UpdatedMessages(string oldStr, string newStr);

   // Declares a state variable `message` of type `string`.
   // State variables are variables whose values are permanently stored in contract storage. The keyword `public` makes variables accessible from outside a contract and creates a function that other contracts or clients can call to access the value.
   string public message;

   // Similar to many class-based object-oriented languages, a constructor is a special function that is only executed upon contract creation.
   // Constructors are used to initialize the contract's data. Learn more:https://solidity.readthedocs.io/en/v0.5.10/contracts.html#constructors
   constructor(string memory initMessage) {

      // Accepts a string argument `initMessage` and sets the value into the contract's `message` storage variable).
      message = initMessage;
   }

   // A public function that accepts a string argument and updates the `message` storage variable.
   function update(string memory newMessage) public {
      string memory oldMsg = message;
      message = newMessage;
      emit UpdatedMessages(oldMsg, newMessage);
   }
}
```

Это сверхпростой смарт-контракт, который сохраняет сообщение после создания и может быть обновлен путем вызова функции `update`.

### Шаг 11 — Подключите Metamask и Alchemy в свой проект {#step-11-connect-metamask-alchemy-to-your-project}

Мы создали кошелек Metamask, аккаунт Alchemy и написали смарт-контракт. Теперь пришло время подключить три этих компонента.

Каждая транзакция, отправляемая из вашего виртуального кошелька, требует наличия подписи с использованием уникального приватного ключа. Чтобы предоставить нашей программе данное разрешение, мы можем безопасно сохранить приватный ключ (и ключ Alchemy API) в файле среды.

Во-первых, установите пакет dotenv в каталог проекта:

```
npm install dotenv --save
```

Затем создайте файл `.env` в корневом каталоге проекта и добавьте в него свой приватный ключ Metamask и HTTP Alchemy API URL.

Ваш файл среды должен иметь имя `.env`, иначе он не будет распознан как файл среды.

Не следует давать ему имя `process.env` или `.env-custom`, или какое-либо иное имя.

:::warning

Если вы используете систему контроля версий типа git для управления проектом, НЕ отслеживайте файл .env. Добавьте .env в свой файл .gitignore, чтобы предотвратить публикацию секретных данных.

:::

* Следуйте [этим инструкциям](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key), чтобы экспортировать свой приватный ключ
* Чтобы получить ключ Alchemy HTTP API (RPC URL), перейдите в приложение «Hello World» на дашборде вашего аккаунта и нажмите «View Key» (Посмотреть ключ) в верхнем правом углу.

Ваш файл `.env` должен выглядеть следующим образом:

```
API_URL = "https://polygon-mumbai.g.alchemy.com/v2/your-api-key"
PRIVATE_KEY = "your-metamask-private-key"
```

Чтобы в действительности подключить эти переменные в наш код, мы включим ссылку на них в наш файл `hardhat.config.js` на шаге 13.

### Шаг 12 — Установите Ethers.js {#step-12-install-ethers-js}

Ethers.js — это библиотека, которая упрощает взаимодействие и отправку заявок в Ethereum за счет упаковки [стандартных методов JSON-RPC](https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc) с более удобными методами.

Hardhat облегчает интеграцию [плагинов](https://hardhat.org/plugins/) с целью обеспечения дополнительных инструментальных средств и расширения функциональных возможностей. Мы воспользуемся [плагином Ethers](https://hardhat.org/plugins/nomiclabs-hardhat-ethers.html) для развертывания контракта. [Ethers.js](https://github.com/ethers-io/ethers.js/) предусматривает полезные методы развертывания контракта.

В каталоге вашего проекта введите:

```bash
npm install --save-dev @nomiclabs/hardhat-ethers "ethers@^5.0.0"
```

Нам также потребуются эфиры в `hardhat.config.js` на следующем шаге.

### Шаг 13 — Обновите hardhat.config.js {#step-13-update-hardhat-config-js}

Мы уже добавили несколько зависимостей и плагинов, и теперь нам нужно обновить `hardhat.config.js`, чтобы все они распознавались в нашем проекте.

Обновите скрипт `hardhat.config.js`, чтобы он приобрел следующий вид:

```javascript
/**
* @type import('hardhat/config').HardhatUserConfig
*/

require('dotenv').config();
require("@nomiclabs/hardhat-ethers");

const { API_URL, PRIVATE_KEY } = process.env;

module.exports = {
   solidity: "0.8.9",
   defaultNetwork: "polygon_mumbai",
   networks: {
      hardhat: {},
      polygon_mumbai: {
         url: API_URL,
         accounts: [`0x${PRIVATE_KEY}`]
      }
   },
}
```

### Шаг 14 — Скомпилируйте контракт {#step-14-compile-our-contract}

Чтобы убедиться в работоспособности кода, давайте скомпилируем наш контракт. Задача `compile` является одной из встроенных задач hardhat.

Из командной строки запустите команду:

```bash
npx hardhat compile
```

Вы можете получить предупреждение `SPDX license identifier not provided in source file`, однако приложение может по-прежнему работать нормально. В противном случае вы всегда можете отправить сообщение в [Discord Alchemy](https://discord.gg/u72VCg3).

### Шаг 15 — Напишите скрипт развертывания {#step-15-write-our-deploy-script}

Теперь, когда контракт написан, а файл конфигурации готов, пришло время написать скрипт развертывания контракта.

Перейдите в папку `scripts/` и создайте новый файл с именем `deploy.js`, добавив в него следующие строки:

```javascript
async function main() {
   const HelloWorld = await ethers.getContractFactory("HelloWorld");

   // Start deployment, returning a promise that resolves to a contract object
   const hello_world = await HelloWorld.deploy("Hello World!");   
   console.log("Contract deployed to address:", hello_world.address);
}

main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
  });
```

Мы заимствовали пояснения команды разработчиков Hardhat в отношении того, что делает каждая строка этого кода, из их [руководства по контрактам](https://hardhat.org/tutorial/testing-contracts.html#writing-tests).

```javascript
const HelloWorld = await ethers.getContractFactory("HelloWorld");
```

`ContractFactory` в ethers.js — это абстракция, используемая для развертывания новых смарт-контрактов, поэтому `HelloWorld` здесь является [фабрикой классов](https://en.wikipedia.org/wiki/Factory\_\(object-oriented\_programming\)) для экземпляров нашего контракта hello world. При использовании `ContractFactory` и `Contract` в плагине `hardhat-ethers` экземпляры подключаются по умолчанию к первому подписавшему лицу (владельцу).

```javascript
const hello_world = await HelloWorld.deploy();
```

При вызове функции `deploy()` на `ContractFactory` начинается развертывание, при этом возвращается `Promise`, которое разрешается в объект `Contract`. Это объект, в котором имеется метод для каждой из функций нашего смарт-контракта.

### Шаг 16 — Разверните контракт {#step-16-deploy-our-contract}

Перейдите в командную строку и запустите команду:

```bash
npx hardhat run scripts/deploy.js --network polygon_mumbai
```

После этого вы должны увидеть что-то вроде этого:

```bash
Contract deployed to address: 0x3d94af870ED272Cd5370e4135F9B2Bd0e311d65D
```

Если мы перейдем в [обозреватель Polygon Mumbai](https://mumbai.polygonscan.com/) и выполним поиск адреса нашего контракта, мы должны увидеть, что он был успешно развернут.

Адрес `From` должен соответствовать адресу вашего аккаунта Metamask, а в адресе To будет указано «Contract Creation» (Создание контракта). Однако, если мы щелкнем кнопкой мыши транзакцию, мы увидим адрес нашего контракта в поле `To`:

![img](/img/alchemy/polygon-scan.png)

### Шаг 17 — Проверьте контракт {#step-17-verify-the-contract}

Alchemy предоставляет [обозреватель](https://dashboard.alchemyapi.io/explorer), где вы можете найти информацию о методах, развернутых вместе со смарт-контрактом, таких как время отклика, статус HTTP, коды ошибок и т. п. Это хорошая среда для проверки контракта и успешного прохождения транзакций.

![img](/img/alchemy/calls.png)

**Поздравляем! Вы только что развернули смарт-контракт в цепочке Polygon.**
