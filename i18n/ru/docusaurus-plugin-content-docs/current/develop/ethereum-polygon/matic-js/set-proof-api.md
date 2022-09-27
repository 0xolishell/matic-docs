---
id: set-proof-api
title: Настройка ProofApi
keywords:
    - setProofApi
    - polygon
    - sdk
description: Конфигурируйте API доказательства.
---

Вы увидите определенные API с **более быстрым** суффиксом, что обеспечивает ускорение процесса. Это достигается за счет использования API генерации доказательства в серверной системе, размещенного любым лицом.

Polygon разместил api генерации доказательства, который может использовать любое лицо. API имеет следующий url: [https://apis.matic.network/](https://apis.matic.network/)

`setProofApi` можно использовать для задания url для api доказательства.

```
import { setProofApi } from '@maticnetwork/maticjs'

setProofApi("https://apis.matic.network/");
```

👉 Рекомендуем разместить API доказательства своими силами, что обеспечит повышение производительности. При работе с API по умолчанию, предоставленным Polygon, могут возникнуть проблемы с производительностью, поскольку он используется множеством людей.

Ссылка на репозиторий API доказательства: [https://github.com/maticnetwork/proof-generation-api](https://github.com/maticnetwork/proof-generation-api)

Развернув api, вы можете задать url для api в matic.js с помощью `setProofApi`.

Например, если вы развернули api доказательства и базовым url является `https://abc.com/`, вам необходимо задать базовый url в `setProofApi`

```
import { setProofApi } from '@maticnetwork/maticjs'

setProofApi("https://abc.com/");
```


Рекомендуем использовать более быстрые API, потому некоторые API, в частности те, в которых генерируется доказательство, совершают много вызовов RPC, при этом в случае публичных RPC процесс может быть очень медленным.
>
