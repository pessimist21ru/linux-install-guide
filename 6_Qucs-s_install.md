# `Qucs-s` - моделирование электронных схем.

1. Для установки `Qucs-s` есть несколько способов: через deb-пакет
с [сайта разработчика](https://ra3xdh.github.io/), через внешний репозиторий
OpenSUSE Build Service (проблемный ключ SHA1), или — лучший вариант —
через `Flathub`, где разработчик публикует официальные сборки.

2. Есть версия в репозитории Debian, но там проблема с ключом шифрования. Кроме
того есть свежая версия на `Flathub`, что вообще здорово, так как при установке
через `flatpak` мы получаем изолированное окружение со своими симуляторами схем:

```bash
flatpak install flathub io.github.ra3xdh.qucs_s
```

3. Переносим ярлык для запуска в меню приложений в корректное расположение:

```bash
# Ищем ярлык Qucs-s
find /var/lib/flatpak/exports/share/applications -name "*qucs*" 2>/dev/null
# или
ls ~/.local/share/flatpak/exports/share/applications/*qucs* 2>/dev/null

# и создаём локальную для пользователя копию
cp /var/lib/flatpak/exports/share/applications/io.github.ra3xdh.qucs_s.desktop \
~/.local/share/applications/io.github.ra3xdh.qucs_s.desktop

```

Почему это переживёт обновления Flatpak?

*  Системный .desktop файл в /var/lib/flatpak/exports/... будет обновляться
  вместе с пакетом

*  Но твой локальный файл в ~/.local/share/applications/ имеет приоритет выше
  системного

*  Flatpak никогда не трогает пользовательские файлы

Единственное: если разработчик поменяет Exec= строку, то ярлык может сломаться.
Но это редкость.


4. Редактируем ярлык в текстовом редакторе:

```bash
nvim ~/.local/share/applications/io.github.ra3xdh.qucs_s.desktop
# или nano, или kate
```

5. Находим строку с `Categories=` и меняем на:

```bash
Categories=Science;Math;Electronics;
```


6. Или просто создать готовый ярлык одной командой (вместо ручного редактирования):

 ```bash
echo "[Desktop Entry]
Encoding=UTF-8
Name=Qucs-S
Comment=An Universal GUI for Circuit Simulators
EExec=flatpak run io.github.ra3xdh.qucs_s %U
Icon=io.github.ra3xdh.qucs_s
Terminal=false
Type=Application
Categories=Science;Math;Electronics
StartupNotify=false
X-Flatpak-RenamedFrom=qucs-s.desktop;
X-Flatpak=io.github.ra3xdh.qucs_s
" | tee ~/.local/share/applications/io.github.ra3xdh.qucs_s.desktop

update-desktop-database ~/.local/share/applications/
 ```