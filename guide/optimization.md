# Оптимизация Linux
---
# Ядро

Скомпилируем оптимизированное ядро, используя скрипт **[linux-tkg](https://github.com/Frogging-Family/linux-tkg)** для установки патча и компиляции ядра. Также можно воспользоваться ядром от **[CachyOS](https://github.com/CachyOS/linux-cachyos)**.

---
# Параметры GRUB
Измените параметры следующим образом:

1. Откройте файл:
   
   - `/etc/default/grub`

2) Найдите строку:
   
   - `GRUB_CMDLINE_LINUX_DEFAULT`

4) Вставляем туда:

   - `quiet splash lpj=XXXXXXX mitigations=off nowatchdog page_alloc.shuffle=1 pci=pcie_bus_perf intel_idle.max_cstate=1 (для процессоров Intel) libahci.ignore_sss=1 (для HDD)`

Объяснение параметров:
- **lpj**: Уникальный параметр для каждой системы. 

   - Значение автоматически определяется при загрузке, но лучше задать вручную. 

- Определите своё значение для **lpj** с помощью команды:

   - `sudo dmesg | grep -o "lpj=\([0-9]*\)"`

- **mitigations=off** 
  - Отключает все заплатки безопасности ядра.
- **nowatchdog**
  - Отключает сторожевые таймеры, что позволяет избежать заиканий в онлайн-играх.
- **page_alloc.shuffle=1**
  - Рандомизирует свободные списки распределителя страниц, улучшая производительность при работе с памятью на быстрых накопителях (*NVMe, Optane*).
- **intel_idle.max_cstate=1**
  - Ограничивает энергосберегающие функции, не позволяя процессору переходить в глубокий сон. Это увеличит энергопотребление на ноутбуках, но может убрать зависания при нагрузке.

   ⚠️ **Только для процессоров Intel** ⚠️
- **pci=pcie_bus_perf** 
  - Увеличивает значение *Max Payload Size* (*MPS*) для родительской шины *PCI Express*, что обеспечивает лучшую пропускную способность.
- **libahci.ignore_sss=1** 
  - Отключает ступенчатое включение жёстких дисков, ускоряя работу *HDD*.
---
# Оптимизация initramfs
**initramfs** — это начальное загрузочное окружение, содержащее модули и утилиты для загрузки системы. В **Arch Linux** для его генерации используется **mkinitcpio**, который по умолчанию сжимает его с помощью алгоритма **zstd**. Для ускорения загрузки лучше использовать алгоритм с самой быстрой скоростью расжатия — **lz4**.

Следуйте этим шагам:

1. Откройте файл конфигурации **mkinitcpio**: 

   - `/etc/mkinitcpio.conf`

2. Найдите и раскомментируйте эти строки: 

   - `COMPRESSION`  
   - `COMPRESSION_OPTIONS`

3. Впишите в строке:
      - `COMPRESSION` параметр `lz4` 

4.   Впишите в строке: 
      - `COMPRESSION_OPTIONS` параметр `-9`
---
# Демоны

- **Ananicy-cpp**: [Демон](terms.md#Демон) для автоматической установки приоритета процессу. Настраивается через параметры **GRUB**, компиляцию ядра и выбор планировщиков задач (*bore и EEVDF*).

- **System-oomd**: Демон, предотвращающий *OOM* (*Out-of-Memory*) ситуации. Убивает самый ресурсоёмкий процесс *cgroup* до того, как система зависнет.

- **Gamemode**: Демон позволяющий играм запрашивать набор оптимизации для системы и игрового процесса.
---
# Настройки Steam
В параметрах запуска впишите: 

    gamemoderun %command%
---
# ZRAM / ZSWAP
# НУЖДАЕТСЯ В ЗАПОЛНЕНИИ
---
# Политики масштабирования частоты
Политики электропитания позволяют влиять на работу процессора, включая изменение частот и управление потреблением энергии. Эти политики помогают оптимизировать производительность системы в зависимости от текущих задач, например, увеличивая частоту процессора для требовательных приложений или снижая её для экономии энергии при простое.

Список политик:
- **performance**: Максимальная производительность, частота процессора всегда высокая.
- **powersave**: Устанавливает минимальную частоту, чтобы снизить энергопотребление.
- **userspace**: Позволяет пользователю вручную устанавливать частоту процессора.
- **ondemand**: Автоматически увеличивает частоту процессора при повышении нагрузки и уменьшает при снижении.
- **conservative**: Похож на ondemand, но увеличивает частоту медленнее и уменьшает быстрее, чтобы лучше экономить энергию.
- **schedutil**: Использует информацию о планировщике задач для оптимизации частоты процессора, обеспечивая сбалансированное управление производительностью и энергопотреблением.
# Драйверы масштабирования
Процессоры *AMD* и *Intel* могут устанавливать частоты на основе данных от *CPPC* (*Collaborative Processor Performance Control*). Для этого используются драйвера *amd-pstate* и *intel-pstate*.

Режимы работы драйверов **pstate**:
- **active**: По умолчанию. В этом режиме драйвер управляет частотой процессора, активно изменяя её в зависимости от нагрузки.
- **passive**: В этом режиме процессор использует драйвер для настройки частоты, но не может сам устанавливать частоты. Частота определяется только на основе текущей загрузки и ограничений, заданных пользователем. Это может привести к менее оптимальной работе при изменениях нагрузки.
- **guided**: Позволяет устанавливать минимальные и максимальные лимиты частоты процессора. Этот режим более гибкий и эффективный, так как дает возможность пользователю задавать рамки, в которых процессор может изменять свою частоту, что может улучшить производительность и снизить энергопотребление в зависимости от условий.

Чтобы менять политики, выполните следующие шаги:

1. Откройте конфигурацию **GRUB**

2. В строке `GRUB_CMDLINE_LINUX_DEFAULT` добавьте следующие параметры:

Для процессоров *AMD*:

`acpi_cpufreq amd_pstate=guided`

Для процессоров *Intel*:

`acpi_cpufreq intel_pstate=guided`

---
# Работа с программой cpupower

Вы можете использовать следующие команды для настройки политики масштабирования и лимитов частот если выбрали режим **guided**:

- Задать политику масштабирования:
   - Чтобы задать политику масштабирования, Вы можете использовать следующую команду:

      `sudo cpupower frequency-set -g <policy>`
- Задать минимальную и максимальную частоту:
   - Чтобы установить минимальную частоту процессора, Вы можете использовать следующую команду:

      `sudo cpupower frequency-set --min <Ghz>`
   - Чтобы установить максимальную частоту процессора, Вы можете использовать следующую команду:

      `sudo cpupower frequency-set --max <Ghz>`
- Задать частоту для конкретного ядра: 
   - Чтобы установить частоту для конкретного ядра, Вы можете использовать следующую команду:

      `sudo cpupower frequency-set -c <core> -g <policy>`
- Просмотреть текущие настройки: 
   - Чтобы увидеть текущие настройки и частоты процессора, Вы можете использовать следующую команду: 

      `cpupower frequency-info`
---
# Службы индексации
В окружениях рабочего стола, таких как *GNOME* и *KDE Plasma*, работают службы индексации, которые в фоновом режиме ищут новые файлы и каталоги, чтобы внести их в базу для ускорения встроенного поиска.
# Отключение служб индексации
На *GNOME*:
```
systemctl --user mask tracker-extract-3 tracker-miner-fs-3 \
tracker-miner-fs-control-3 tracker-miner-rss-3 tracker-writeback-3 \
tracker-xdg-portal-3
rm -rf ~/.cache/tracker ~/.local/share/tracker
```
На *KDE Plasma*:
```
balooctl6 suspend
balooctl6 disable
balooctl6 purge
```
---