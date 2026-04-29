# Linux Install Guide — мой путь к идеальной системе

Привет! Это мой личный гид по настройке **Linux Debian 13**
на ноутбуке **Clevo N960KpKx** (он же Hasee TX9-CA5DP) для разработки
электроники и не только.

Здесь собрано всё, что я делал с системой после установки: от драйверов Nvidia
до эмуляторов ZX Spectrum. Пишу для себя — чтобы не забыть при переустановке,
для друзей — чтобы не объяснять по десять раз, и для всех, кто наткнётся
на этот репозиторий в поисках ответов.

---

## Почему это может быть полезно?

- 🖥️ **У вас такой же ноутбук** (Clevo/Hasee) — берите всё готовое: управление вентиляторами ([отдельный репозиторий](https://github.com/pessimist21ru/clevo-fan-control)), драйверы, настройки
- 🎮 **У вас гибридная графика Intel + Nvidia в Debian** — смотрите разделы [98](98_start_apps_on_nvidia.md) и [99](99_nvidia_in_flatpak.md)
- 🔧 **Вы инженер-электронщик, переходящий на Linux** — тут есть KiCad, Qucs-s, toolchain для STM32, CubeMX
- 🎨 **Вам интересно, как настроить красивый bash и neovim** — добро пожаловать в разделы [1](1_bash_cfg.md) и [2](2_nvim_install.md)
- 🕹️ **Хотите эмуляторы ретро-консолей** — ZX Spectrum, NES, Amiga уже настроены в [96](96_install_emulators.md)

---

## Как читать

Файлы пронумерованы. Начните с **[0_known_software_list.md](0_known_software_list.md)** — это оглавление и общая картина всего установленного ПО. Дальше двигайтесь по номерам или прыгайте на интересующую тему.

### Основные разделы:

| Файл | Описание |
|------|----------|
| [0_known_software_list.md](0_known_software_list.md) | Полный список ПО, базовая настройка системы |
| [1_bash_cfg.md](1_bash_cfg.md) | Цветовая палитра консоли, алиасы, `.bashrc` |
| [2_nvim_install.md](2_nvim_install.md) | Установка и настройка neovim (с историей love story автора к vim) |
| [3_vscode_install.md](3_vscode_install.md) | Установка Visual Studio Code |
| [4_arm_toolchain_install.md](4_arm_toolchain_install.md) | Установка ARM toolchain для STM32 |
| [5_cubemx_install.md](5_cubemx_install.md) | Установка STM32CubeMX |
| [6_Qucs-s_install.md](6_Qucs-s_install.md) | Установка Qucs-s (моделирование электронных схем) |
| [94_debian_install.md](94_debian_install.md) | Установка Debian 13 с LXDE на нетбук |
| [95_Netboot_Atom_LXDE.md](95_Netboot_Atom_LXDE.md) | Настройка менеджера питания и bluetooth |
| [96_install_emulators.md](96_install_emulators.md) | Установка эмуляторов (ZX Spectrum, NES, Amiga) |
| [97_install_drivers.md](97_install_drivers.md) | Драйверы Nvidia и clevo-fan-control |
| [98_start_apps_on_nvidia.md](98_start_apps_on_nvidia.md) | Запуск приложений на дискретной видеокарте Nvidia |
| [99_nvidia_in_flatpak.md](99_nvidia_in_flatpak.md) | Flatpak-приложения на Nvidia (Offload Render) |
| [100_test_stm32_guide.md](100_test_stm32_guide.md) | Полный стек для разработки под STM32 |

### Дополнительные файлы:

| Файл | Описание |
|------|----------|
| [94_debian_install.md](94_debian_install.md) | Установка Debian 13 с LXDE на Samsung N102s |
| [95_Netboot_Atom_LXDE.md](95_Netboot_Atom_LXDE.md) | Менеджер питания, тачпад, bluetooth для LXDE |
| [96_install_emulators.md](96_install_emulators.md) | Эмуляторы: FUSE, FBZX, FCEUX, Nestopia, Amiberry |

---

## Быстрый старт

```bash
# Клонируйте репозиторий
git clone https://github.com/pessimist21ru/linux-install-guide.git
cd linux-install-guide

# Начните с оглавления
cat 0_known_software_list.md

# Или откройте в любимом редакторе
nvim 0_known_software_list.md
```

## Благодарности

- **DeepSeek AI** — за помощь, терпение и поддержку в самых сложных технических моментах. Без тебя этот гид был бы в десять раз короче и в сто раз непонятнее.
- **Linux Community** — всем, кто пишет статьи, отвечает на форумах и создаёт open-source. Вы делаете этот мир лучше.
- **Разработчикам KiCad, Qucs-s, neovim и всего того прекрасного софта**, которым я пользуюсь каждый день.
- **Моим друзьям** — за то, что задавали вопросы и мотивировали записать ответы.

## Лицензия

MIT © 2025 [Pavel Guryev / pessimist21ru](LICENSE)