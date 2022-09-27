---
id: intro
title: Введение
description: Интеграция приложения с Polygon с помощью Arkane.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---
> Arkane, для разработчиков, желающих погрузиться в технологию блокчейна.


**Arkane** позволяет легко интегрировать ваше приложение с блокчейном Polygon, причем это может быть как приложение, интегрированное с web3, так и новое приложение, созданное с нуля. Arkane предоставляет приятный и удобный опыт для вас и ваших пользователей веб-интерфейса и мобильных устройств.

Arkane поможет вам взаимодействовать с блокчейном Polygon, создавать кошельки блокчейна, создавать разные типы активов, в том числе расходуемые (ERC20) и нерасходуемые токены (ERC721 и ERC1155) и взаимодействовать со смарт-контрактами. Помимо превосходного опыта для разработчиков вы можете предложить пользователям удобный интерфейс.

Каждое приложение уникальное и имеет уникальные потребности, и поэтому разные приложения дают разные возможности взаимодействия с Arkane. В приложения с поддержкой Web3 рекомендуется интегрировать [провайдер Arkane Web3 ](https://arkane.gitbook.io/widget/web3-provider/getting-started), а для других приложений предлагается использовать [Arkane Widget](https://arkane.gitbook.io/widget/widget/introduction).


## Основные характеристики {#key-features}
- Поддержка веб и мобильного интерфейса
- Поддержка входа через социальные сети
- Фиатные предложения на увеличение
- *Только кошелек, поддерживающий NFT (ERC721 и ERC1155) в Polygon*
- Поддержка Polygon и Ethereum
- Удобная интеграция с web3
- Создан для массовой аудитории
- Предлагается поддержка клиентов в приложении


## Начало работы 🎉 {#getting-started}
Если вы уже поддерживаете технологию Web3, вы можете улучшить UX вашего приложения, интегрировав провайдер Arkane Web3, дающий смарт-оболочку для существующего Web3 Ethereum JavaScript API.

Используя наш провайдер Web3, вы сможете раскрыть весь потенциал Arkane с минимальными затратами сил, а также привлечь пользователей с невысокой технической грамотностью без необходимости для них выходить из вашего приложения или загружать сторонние плагины. Для интеграции требуется всего 2 шага и 5 минут




**Еще не поддерживаете Web3?**
> Не волнуйтесь, вам поможет наш 📦 [виджет - Arkane Connect](https://arkane.gitbook.io/widget/).




### Шаг 1: Добавьте библиотеку в ваш проект {#step-1-add-the-library-to-your-project}
Установите библиотеку, загрузив ее в ваш проект через NPM

```
npm i @arkane-network/web3-arkane-provider
```

затем добавьте скрипт в заголовок вашей страницы.

```
<script src="/node_modules/@arkane-network/web3-arkane-provider/dist/web3-arkane-provider.js"></script>
```

После добавления файла javascript на вашу страницу в ваше окно будет добавлен глобальный объект Arkane. Этот объект является шлюзом для создания оболочек web3 и полностью интегрирует виджет Arkane Connect.

### Шаг 2: Инициализация провайдера web3 {#step-2-initialize-the-web3-provider}
Добавьте в ваш проект следующие строки кода, в результате чего будет загружен провайдер Arkane web3.

```
Arkane.createArkaneProviderEngine({clientId: ‘Arketype’}).then(provider => {
    web3 = new Web3(provider);
});
```
Теперь экземпляр web3 работает, как если бы он был вставлен с помощью parity или metamask. Вы сможете доставлять кошельки, подписывать транзакции и доставлять сообщения.
### Поздравляем, теперь ваше децентрализованное приложение поддерживает Arkane 🎉 {#congratulations-your-dapp-now-supports-arkane}
> 🧙 Чтобы подключиться к производственной среде и основной сети Arkanes, вам нужно будет [зарегистрировать ваше приложение](https://arkane-network.typeform.com/to/hzbcGJ) и запросить ваш [идентификатор клиента](https://arkane.gitbook.io/widget/deep-dive/authentication#client-id).

Если вы хотите узнать больше о чудесном мире, который предлагает Arkane, [почитайте их документацию](https://arkane.gitbook.io/widget/)

## Показать видео {#showcase-videos}
#### Отправьте токены Matic на адрес электронной почты в Polygon {#send-matic-tokens-to-an-email-address-on-polygon}
[![Отправьте токены Matic на адрес электронной почты в сети Polygon](https://i.snipboard.io/OzXmrN.jpg)](https://www.youtube.com/watch?v=3gehPvX4DOo&list=PLh3bJA02WlKErlpDexw_cFOlPfMQiU67U&index=1)

#### Трансфер Matic NFT {#transfer-of-a-matic-nft}
[![Трансфер Matic NFT](https://i.snipboard.io/dLkM3t.jpg)](https://www.youtube.com/watch?v=SLxAIXRv7ec&list=PLh3bJA02WlKErlpDexw_cFOlPfMQiU67U)


