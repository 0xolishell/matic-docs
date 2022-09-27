---
id: custom-tokens
title: Как добавить пользовательские токены в Arkane?
sidebar_label: Custom Tokens
description: Поддержка пользовательских токенов ERC20/ERC721/ERC1155 в Polygon.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---
> Пользовательские поглощаемые и непоглощаемые токены

### Поглощаемые {#fungible}
Разработчик может легко добавить поддержку своего пользовательского токена ERC20, создав небольшой запрос pull, содержащий детали токена для [репозитория Arkane Git](https://github.com/ArkaneNetwork/content-management/tree/master/tokens). Вот образец информации, которую необходимо предоставить:
```
{"name":"SAND","symbol":"SAND","address":"0x3845badade8e6dff049820680d1f14bd3903a5d0","decimals":18,"type":"ERC20"}
```
Также вы можете связаться с ними через чат в приложении и попросить добавить ваш токен.

### Не поглощаемый {#non-fungible}
Arkane разработала свой сервис так, что он автоматически 🤩 собирает пользовательские NFT, если они соответствуют стандарту ERC721 и ERC1155. На сегодня это единственный кошелек, способный показывать все NFT, которые присутствуют в блокчейнах Polygon.

![Hulk ERC1155 NFT в Polygon](img/09.png)
