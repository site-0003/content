---
# -------------------------------------------------------------------------------------------------------------------- #
# General settings.
# -------------------------------------------------------------------------------------------------------------------- #

title: 'Сброс пароля CISCO'
description: ''
images:
  - 'https://images.unsplash.com/photo-1528845922818-cc5462be9a63'
categories:
  - 'network'
tags:
  - 'cisco'
  - 'password'
  - 'reset'
authors:
  - 'KaiKimera'
sources:
  - ''
license: 'CC-BY-SA-4.0'
complexity: '0'
toc: 1
comments: 1

# -------------------------------------------------------------------------------------------------------------------- #
# Date settings.
# -------------------------------------------------------------------------------------------------------------------- #

date: '2024-03-26T15:07:17+03:00'
publishDate: '2024-03-26T15:07:17+03:00'
expiryDate: ''
lastMod: '2024-03-26T15:07:17+03:00'

# -------------------------------------------------------------------------------------------------------------------- #
# Meta settings.
# -------------------------------------------------------------------------------------------------------------------- #

type: 'articles'
hash: 'fc67eb748f7c3de36d1e2d823208eae243424bbe'
uuid: 'fc67eb74-8f7c-5de3-bd1e-2d823208eae2'
slug: 'fc67eb74-8f7c-5de3-bd1e-2d823208eae2'

draft: 0
---

Инструкция по сбросу пароля администратора в готовой конфигурации на устройствах {{< tag "CISCO" >}}.

<!--more-->

- Подключиться к COM-порту {{< tag "CISCO" >}}.
- Загрузиться в **ROMMON** (при старте {{< tag "CISCO" >}} нажать клавишу {{< key "Break" >}}).
- Переключить регистр с `0x2102` на `0x2142` и перезагрузить:

```
> confreg 0x2142
> reset
```

- Сбросить пароль:

```
> en
# copy startup-config running-config
# config t
(config)# en secret PA$$W0RD
(config)# username USER privilege 15 secret PA$$W0RD
```

- Переключить регистр с `0x2142` на `0x2102`:

```
(config)# config-register 0x2102
(config)# exit
# copy running-config startup-config
# reload
```

- Готово!