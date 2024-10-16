# Выбор дистрибутива Linux

1) **[Linux Mint](https://www.linuxmint.com/)** — идеальный для новичков.
2) **[Fedora Linux](https://fedoraproject.org/)** — предлагает регулярные обновления для большинства [пакетов](terms.md#Пакеты), в то время как [meta пакеты](terms.md#meta-пакеты), такие как окружение рабочего стола **GNOME**, обновляются версионно при обновлении системы до новой версии. Это обеспечивает стабильность и надежность.
3) **[openSUSE](https://www.opensuse.org/)** — предлагает две версии системы: **Leap** для стабильности и **Tumbleweed** для [rolling release](terms.md#rolling-release). Использует [пакетный менеджер](terms.md#пакетный-менеджер) **Zypper** и инструмент **YaST** для настройки. Подходит как новичкам, так и опытным пользователям, предлагая гибкость и удобство.
4) **[Arch Linux](https://archlinux.org/)** — rolling release с гибкими настройками и огромным количеством пакетов из официального пакетного менеджера **pacman**, дополняемого репозиторием **[— AUR (Arch User Repository)](https://aur.archlinux.org/)**, поддерживаемым сообществом.
5) **[CachyOS](https://cachyos.org/)** — для тех, кому нужна максимальная производительность системы, основан на **Arch Linux**.
6) **[NixOS](https://nixos.org/)** — система с уникальным подходом к настройке. Вся конфигурация хранится в специальных файлах *.[nix](terms.md#.nix)*, а сама система имеет собственный язык [NIX](terms.md#NIX) и менеджер пакетов [Nix Store](terms.md#Nix-Store), с помощью которого происходит настройка системы и управление пакетами. NixOS поддерживает инновационный инструмент [flakes](terms.md#Flakes), что представляет собой современный способ управления конфигурациями. В корне каждой конфигурации находится файл **flake.nix**, который определяет набор пакетов, конфигурацию системы и зависимости. Это упрощает совместное использование и версионирование конфигураций. Это позволяет создавать воспроизводимые окружения, что особенно полезно при разработке и тестировании. Инструмент [nix-shell](terms.md#nix-shell) позволяет вам создавать изолированные среды для разработки. Вы можете использовать его для временной установки пакетов и их зависимостей, не влияя на основную систему. Это делает разработку и тестирование пакетов удобным и безопасным. Кроме того, [Home Manager](terms.md#Home-Manager) позволяет управлять пользовательским окружением, храня настройки приложений в виде конфигурационных файлов *.nix*. Он поддерживает интеграцию с flakes, что позволяет легко переносить настройки и использовать их в разных системах. Эти инструменты делают **NixOS** мощным и гибким выбором для опытных пользователей, позволяя легко настраивать, версионировать и делиться своими конфигурациями.

### Эти дистрибутивы предлагают разные возможности и являются выбором авторов данного гайда. 
### Выбирайте в зависимости от ваших предпочтений и уровня опыта.
**[ВСЯ ИНФОРМАЦИЯ ПО NIXOS НАХОДИТСЯ ОТДЕЛЬНО](https://github.com/biocastle/Linux-setup-guide-RU/blob/main/guide-nixos/info.md)**