---
title: 'Отключение установки дополнительных пакетов в Debian APT'
description: ''
images:
  - 'https://images.unsplash.com/photo-1583078379333-e34d6569c406'
cover:
  crop: 'center'
categories:
  - 'linux'
tags:
  - 'debian'
  - 'apt'
authors:
  - 'KitsuneSolar'
license: 'CC-BY-SA-4.0'
complexity: '0'
toc: 1
comments: 1

date: '2021-09-07T16:07:46+03:00'
hash: '43b40b92c29ac04b47fa5eac368dcf95b3900cbe'
uuid: '43b40b92-c29a-504b-87fa-5eac368dcf95'
slug: '43b40b92-c29a-504b-87fa-5eac368dcf95'

draft: 0
---

Стандартное действие APT при установке пакета, это установить дополнительные рекомендуемые пакеты. Но, к примеру, мне рекомендуемые пакеты не нужны. Они занимают место и, если я захочу поставить что-то дополнительно, я поставлю это сам.

<!--more-->

Ниже приведены две команды, которые позволяют отключить установку рекомендуемых (Recommends) и дополнительных (Suggests)пакетов.

```sh
echo 'APT::Install-Recommends "false";' > /etc/apt/apt.conf.d/00InstallRecommends
```

```sh
echo 'APT::Install-Suggests "false";' > /etc/apt/apt.conf.d/00InstallSuggests
```

Стоит заметить, что при установке Debian на завершающем этапе можно отключить рекомендуемые пакеты (в preseed этот параметр именуется как `d-i base-installer/install-recommends boolean false`). При этом, после установки, Debian сам создаст файл `00InstallRecommends` в `/etc/apt/apt.conf.d` с соответствующим содержанием.