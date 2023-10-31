---
title: 'Monero Unit'
description: ''
images:
  - 'https://images.unsplash.com/photo-1552057426-9f23e61fa7b1'
categories:
  - 'crypto'
tags:
  - 'monero'
  - 'systemd'
authors:
  - 'KitsuneSolar'
license: 'CC-BY-SA-4.0'
complexity: '0'
toc: 1
comments: 1

date: '2021-03-05T14:03:20+03:00'
hash: '0a8155474f1812a0aa99c463421872cae7feee23'
uuid: '0a815547-4f18-52a0-9a99-c463421872ca'
slug: '0a815547-4f18-52a0-9a99-c463421872ca'

draft: 0
---

Бум майнинга. Скупают видеокарты, ноутбуки. Будут майнить на тостерах и стиральных машинах. Решил попробовать **Monero**. Задача была запустить майнер в фоне, желательно в отдельной сессии. И чтобы запускался он каждый раз при старте машины.

<!--more-->

Написал небольшой [systemd unit](https://github.com/KitsuneSolar/xmrig-systemd) для майнера [xmrig](https://github.com/search?q=xmrig).

{{< ghRepo "KitsuneSolar/xmrig-systemd" >}}

Запускается от имени `root` в отдельной сессии **screen**, но можно запускать от любого пользователя: в командах запуска и остановки необходимо вместо `root` прописывать имя другого пользователя.

## Установка

1. Установить `screen`.
2. Поместить в директорию `/etc/systemd/system` файл `xmrig@.service`.
3. В файле `xmrig@.service` задать в параметре `ExecStart` путь до `xmrig.run.sh`.

## Использование

- Запуск:

```terminal {os="linux", mode="root"}
systemctl enable --now xmrig@root
```

- Остановка:

```terminal {os="linux", mode="root"}
systemctl disable --now xmrig@root
```