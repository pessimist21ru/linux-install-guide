Вот список команд для установки некоторых эмуляторов старых систем в Debian,
если они доступны в репозиториях:

## FUSE (Free Unix Spectrum Emulator)
FUSE — эмулятор ZX Spectrum. В Debian он доступен в основном репозитории.
[```1```](https://www.addictivetips.com/ubuntu-linux-tips/play-sinclair-zx-spectrum-games-on-linux/)

Чтобы установить FUSE, выполните:
```bash
sudo apt-get install fuse-emulator-common
```

Для работы самого эмулятора нужно установить одну из графических оболочек:
```bash
sudo apt-get install fuse-emulator-gtk  # версия с интерфейсом GTK
# или
sudo apt-get install fuse-emulator-sdl  # версия с интерфейсом SDL
```

Также может потребоваться пакет с ROM-файлами:
```bash
sudo apt-get install spectrum-roms
```

## FBZX
FBZX — ещё один эмулятор ZX Spectrum, работающий в X Window System или в режиме
FrameBuffer. [```6```](https://packages.debian.org/sid/fbzx)

Команда для установки:
```bash
sudo apt-get install fbzx
```

## FCEUX
FCEUX — эмулятор NES/Dendy. В Debian доступен в репозитории unstable (sid).
[```18```](https://packages.debian.org/unstable/games/fceux)

Чтобы установить, выполните:
```bash
sudo apt-get install fceux
```

## Nestopia
Nestopia — эмулятор NES/Famicom. Также доступен в репозитории unstable (sid).
[```21```](https://packages.debian.org/sid/nestopia)

Команда для установки:
```bash
sudo apt-get install nestopia
```

## Amiberry
Amiberry — эмулятор Amiga. В стандартных репозиториях Debian не представлен,
но можно установить из DEB-пакета или через Flatpak.
[```31```](https://blitterstudio.com/amiberry/)

Если у вас есть DEB-пакет (например, `amiberry-XYZ.deb`), выполните:
```bash
sudo apt install ./amiberry-XYZ.deb
```

Для установки через Flatpak сначала нужно убедиться, что Flatpak установлен,
а затем выполнить:
```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
flatpak install flathub com.blitterstudio.Amiberry
```

## FS-UAE
FS-UAE — ещё один эмулятор Amiga. Для его установки в Debian требуется
добавление стороннего репозитория.
[```36```](https://www.addictivetips.com/ubuntu-linux-tips/play-amiga-games-on-linux/)

Добавьте репозиторий:
```bash
su -
echo "deb https://download.opensuse.org/repositories/home:/FrodeSolheim:/stable/Debian_9.0/ /" > /etc/apt/sources.list.d/FrodeSolheim-stable.list
```

Скачайте и установите ключ релиза:
```bash
wget https://download.opensuse.org/repositories/home:FrodeSolheim:stable/Debian_9.0/Release.key
apt-key add - < Release.key
```

Обновите список пакетов и установите FS-UAE:
```bash
apt-get update
apt-get install fs-uae fs-uae-launcher fs-uae-arcade
```

**Примечание:** поддержка FS-UAE в Debian ограничена, например, в некоторых
версиях (например, Debian 10) могут возникнуть сложности с установкой.
[```36```](https://www.addictivetips.com/ubuntu-linux-tips/play-amiga-games-on-linux/)

Перед установкой любого пакета убедитесь, что список пакетов обновлён:
```bash
sudo apt-get update
```

Если эмулятор не найден в репозиториях, возможно, потребуется компиляция из
исходного кода или использование других методов установки (например, через
Flatpak, Snap или сторонние репозитории).