---
title: 'Чёрный экран с курсором при старте GDM'
description: ''
images:
  - 'https://images.unsplash.com/photo-1542906453-02e875f65428'
categories:
  - 'linux'
tags:
  - 'debian'
  - 'gnome'
  - 'gdm'
  - 'systemd'
authors:
  - 'KitsuneSolar'
sources:
  - 'https://www.reddit.com/r/gnome/comments/ml0sky/solution_to_a_black_screen_on_boot_problem_after/'
  - 'https://www.reddit.com/r/archlinux/comments/j1ji0o/gdm_login_screen_doesnt_show_until_switching_tty/'
license: 'CC-BY-SA-4.0'
complexity: '0'
toc: 1
comments: 1

date: '2021-09-30T11:10:19+03:00'
hash: '581804b80613b2a8a5c4fdae90e7bc8ad26ee435'
uuid: '581804b8-0613-52a8-a5c4-fdae90e7bc8a'
slug: '581804b8-0613-52a8-a5c4-fdae90e7bc8a'

draft: 0
---

На днях установил **Debian 11** на несколько ПК. Всё было отлично, кроме как на одном из ПК почему то **GDM** не стал загружаться. Чёрный экран с мигающем текстовым курсором...

<!--more-->

На других ПК всё было нормально. Отличие проблемного ПК от остальных заключалось в том, что на проблемном ПК система была установлена на **SSD**. Может быть дело в этом. В интернете я нашёл похожую проблему. По сути, проблема заключается в том, что сам GDM запускается чуть раньше, чем начинается инициализация графической подсистемы. И на кой чёрт его несёт вперёд...

В общем, предлагают несколько решений.

## Настройка сервиса GDM

Данный вариант решения проблемы предлагает отредактировать стартовые параметры сервиса GDM, чтобы тот запускался с небольшой задержкой и не лез вперёд остальной графической подсистемы.

В терминале нужно ввести от root'а команду `systemctl edit gdm.service`, откроется редактор сервиса GDM. В нем необходимо добавить строку `ExecStartPre=/bin/sleep 2`, которая создаёт задержку перед запуском самого GDM. Примерно вот так:

```ini
### Anything between here and the comment below will become the new contents of the file

[Service]
ExecStartPre=/bin/sleep 2

### Lines below this comment will be discarded
```

Таким образом GDM начинает запускаться после того, как инициализируется графическая подсистема.

Ещё одно исправление [предложили](https://gitlab.gnome.org/GNOME/gdm/-/issues/662#note_993169) в баг-трекере GNOME/GDM. Вместо торможения загрузки GDM, необходимо добавить следующие строки:

```ini
[Unit]
Wants=systemd-udev-trigger.service systemd-udev-settle.service
After=systemd-udev-trigger.service systemd-udev-settle.service
```

Но это не совсем корректное исправление (хотя исправление, изложенное выше, тоже трудно назвать правильным), так как после применения этих строк, появляется ошибка:

```terminal {os="linux"}
Dec 27 22:23:48 oleksandr-xps15 udevadm[260]: systemd-udev-settle.service is deprecated. Please fix gdm.service not to pull it in.
```

## Указание порядка инициализации графической подсистемы

Этот вариант решения проблемы подходит для **Arch Linux**. Необходимо в файле `/etc/mkinitcpio.conf` добавить в массив `MODULES` свою графическую подсистему, чтобы та запускалась раньше, чем GDM. Например:

```text
MODULES=(amdgpu)
```

Можно указать другие графические подсистемы, всё зависит от видеокарты, установленной в ПК:

- `amdgpu` - для современных видеокарт AMD.
- `radeon` - для устаревших видеокарт AMD.
- `nouveau` - для Nvidia Nouveau.
- `i915` - для Intel.
- `mgag200` - для видеокарт Matrox.

Если в системе используется Intel в качестве графики и в модулях прописан `i915`, то могут появится ошибки ACPI. Для исправления этих ошибок необходимо добавить `intel_agp` перед `i915`. Чтобы проверить корректность загрузки модуля, можно ввести команду `lsmod | grep intel_agp`.