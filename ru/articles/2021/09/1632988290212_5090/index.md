---
title: 'Отключение режима Wayland в GDM'
description: ''
images:
  - 'https://images.unsplash.com/photo-1590140409417-8fc288725f77'
categories:
  - 'linux'
tags:
  - 'wayland'
  - 'gdm'
authors:
  - 'KitsuneSolar'
license: 'CC-BY-SA-4.0'
complexity: '0'
toc: 1
comments: 1

date: '2021-09-30T10:51:30+03:00'
hash: '0d11ecc02714ac5c8b48804f504195906bcfb36b'
uuid: '0d11ecc0-2714-5c5c-9b48-804f50419590'
slug: '0d11ecc0-2714-5c5c-9b48-804f50419590'

draft: 0
---

Использовать **Wayland** мне, пока что, нет необходимости. Поэтому, я решил его отключить в **GDM**.

<!--more-->

Для того, чтобы отключить выбор режима Wayland в GDM, необходимо в файле `/etc/gdm3/daemon.conf` раскомментировать строку:

```ini
# WaylandEnable=false
```

Должно получится так:

```ini
WaylandEnable=false
```

Таким образом даём понять GDM, что мы не желаем использовать Wayland.