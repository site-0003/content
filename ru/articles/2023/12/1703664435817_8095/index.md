---
# -------------------------------------------------------------------------------------------------------------------- #
# General settings.
# -------------------------------------------------------------------------------------------------------------------- #

title: 'Настройка DoH на MikroTik'
description: ''
images:
  - 'https://images.unsplash.com/photo-1639322537504-6427a16b0a28'
categories:
  - 'network'
  - 'security'
tags:
  - 'mikrotik'
  - 'doh'
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

date: '2023-12-27T11:07:16+03:00'
publishDate: '2023-12-27T11:07:16+03:00'
expiryDate: ''
lastMod: '2023-12-27T11:07:16+03:00'

# -------------------------------------------------------------------------------------------------------------------- #
# Meta settings.
# -------------------------------------------------------------------------------------------------------------------- #

type: 'articles'
hash: 'b6c7c5cd1b0dbfcdedd2f28f87a5064c68b9c659'
uuid: 'b6c7c5cd-1b0d-5fcd-8dd2-f28f87a5064c'
slug: 'b6c7c5cd-1b0d-5fcd-8dd2-f28f87a5064c'

draft: 0
---

Небольшая заметка по настройке DNS over HTTPS (DoH) на MikroTik.

<!--more-->

## Установка набора сертификатов

Для работы DoH необходимо наличие сертификатов в базе MikroTik. Пакет сертификатов я беру с сайта `curl.se`:

```
/tool fetch url="https://curl.se/ca/cacert.pem" dst-path="ros.cacert.pem"; /certificate import file-name="ros.cacert.pem" passphrase="" name="ROS"
```

## Настройка DoH

После установки сертификатов, приступаем к настройке DoH:

```
/ip dns set allow-remote-requests=yes servers=1.1.1.1,1.0.0.1 use-doh-server=https://1.1.1.1/dns-query verify-doh-cert=yes
```

В этой команде присутствуют следующие параметры:

- DNS 01: `1.1.1.1`.
- DNS 02: `1.0.0.1`.
- Сервер DoH: `https://1.1.1.1/dns-query`.