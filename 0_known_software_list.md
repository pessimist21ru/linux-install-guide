# Список программ использованных в работе.


  Для установки программ и внесения настроек в система необходимо получить права
суперпользователя компьютера. В Linux Debian сделать это можно двумя способами:

- зайти в сеанс суперпользователя (в консоли, либо в графическом режиме).
  Для входа через консоль существует команда:
  
```bash
  su - # устаревший метод, в rpm ветке основной. Нужен пароль root.
  sudo -i # основной для debian ветки. Нужен пароль пользователя.
```

- выполнить команду с привилегиями суперпользователя. В графическом режиме,
в системе уже существуют специальные ярлыки программ, при открытии которых
появится окно с предложением ввести пароль. Тут надо внимательно посмотреть чей
пароль необходимо ввести (у текущего пользователя, либо пользователя `root`).
  Также есть способ использования модификатора команды, который указывается в
терминале перед самой командой:

``` bash
sudo [command]
```

  Если был использован модификатор `sudo` система запросит ввод пароля. В этом 
случае необходимо вводить пароль текущего пользователя. В зависимости от
дистрибутива символы при вводе пароля могут вообще никак не отображаться на 
экране.
  Однако для использования модификатора `sudo` в некоторых дистрибутивах linux
сперва его нужно включить.

**Включение sudo:**

```bash
su - # если при установке был указан пароль `root`
usermod -aG sudo $USER # если пароль `root` не был указан
                             # выполнение команды не требуется
```

  После выполнения команды система запросит пароль пользователя `root` и
включит пользователя `[user_name]` в список с доступом к выполнению команд
через `sudo`.
  Если при установке системы пароль суперпользователя `root` не был указан
то можно его установить командой:

```bash
sudo passwd root
```

  Но включать пароль `root` в Linux Debian считается избыточным и для
работы в сеансе суперпользователя вместо `su -` (как это принято в rpm ветке)
было принято более альтернативное решение:

```bash
sudo -i
```

  Применение этой команды позволит войти в сеанс суперпользователя `root`
при отсутствии его пароля. Вместо пароля суперпользователя в этом случае
будет запрошен пароль обычного пользователя, как при входе в систему.


# Системные службы и драйвера:


## Установка драйвера NVidia и заголовочных файлов ядра

  Получаем всю информацию об обновлениях и обновляем систему. Важно для
корректной установки драйвера NVidia:

```bash
sudo apt update
sudo apt full-upgrade
```

  Устанавливаем заголовочные файлы ядра и текстовый редактор neovim. В текстовом
редакторе будем редактировать список источников приложение системы. Можно также
использовать `vi`, `nano` или другие. Мне удобно использовать `neovim`, поэтому
устанавливаем его.
  Заголовочные файлы ядра будут нужны после установки драйвера, так как система
заново соберёт ядро:
  
```bash
sudo apt install linux-headers-$(uname -r) neovim
```

  Редактируем список источников приложений и добавляем в него не бесплатные,
в которых как раз присутствуют драйвера NVidia, с помощью `neovim`:

```bash
sudo nvim /etc/apt/sources.list
```

  Список должен выглядеть следующим образом (добавляем ключи в основных
репозиториях для разрешения установки contib, non-free, non-free-firmware):

```ini
deb http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
deb-src http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
```

  Снова получаем информацию о доступных обновлениях системы:

```bash
sudo apt update
```

  Устанавливаем утилиту NVidia, которая проверяет текущий видеоадаптер и
рекомендует правильную версию драйвера для установки:

```bash
sudo apt install nvidia-detect
nvidia-detect
```

  Устанавливаем драйвер (тут команда может отличаться, мне рекомендовано
`nvidia-driver`). Если `nvidia-detect` указала драйвер отличный от
`nvidia-driver` пишем в команду его:

```bash
# Собственно установка драйвера и набора утилит
sudo apt install nvidia-driver firmware-misc-nonfree nvtop mesa-utils

# Добавление пользователя в группы видеоподсистемы
sudo usermod -a -G video,render $USER

# Проверка установки драйвера
nvidia-smi

# Проверка установки групп пользователя
groups $USER
# Должны быть группы: video, render
```

  Далее должна быть установлена утилита для переключения основного видеоадаптера
с встроенного в процессор видео-ядра intel на дискретный NVidia в качестве
основного для системы. Но в Debian не работает. Вроде пакет nvidia-prime
отсутствует в репозитории Debian, просто нужно добавить в список источников
приложений репозиторий с ним. В Ubuntu\Mint должно работать:

```bash
# sudo apt install nvidia-prime
# sudo prime-select nvidia //переключение на nvidia
# sudo prime-select intel  //переключение на intel
# sudo systemctl enable nvidia-suspend.service nvidia-hibernate.service \
# nvidia-resume.service
```

[## Настройка запуска приложений на дискретной видеокарте NVIDIA](98_start_apps_on_nvidia)


[## Настройка запуска приложений Flatpak на дискретной видеокарте NVIDIA](99_nvidia_in_flatpak)


## Установка clevo-fan-control и драйверов tuxedo:

  Клонируем репозиторий с исходным кодом утилиты управления вентиляторов,
Делаем установочный скрипт исполняемым и запускаем:

```bash
git clone https://github.com/pessimist21ru/clevo-fan-control.git
cd clevo-fan-control
chmod +x install.sh
sudo ./install.sh
```

  Или клонируем репозиторий с исходным кодом и вручную собираем и устанавливаем
только бинарный файл в систему в качестве службы:

```bash
git clone https://github.com/pessimist21ru/clevo-fan-control.git
cd clevo-fan-control
mkdir build && cd build
cmake ..
make -j$(nproc)   # сборка бинарного файла
sudo make install # установка службы
sudo systemctl daemon-reload
sudo systemctl enable clevo-fan-control
sudo systemctl start clevo-fan-control
```

  Проверяем произошла ли установка и запущена ли служба:

```bash
sudo systemctl status clevo-fan-control # для службы
sudo clevo-fan-control status           # для интерактивного запуска
```

# Удаляем лишнее из системы:


  Удаляем лишние базовые утилиты Linux Debian:
```bash
sudo apt-get remove --purge xterm imagemagick* zeitgeist* juk
sudo apt-get autoremove
sudo apt-get autoclean
```

  Компоненты `KDE Plasma`:

  Чтобы избежать автоматического восстановления удалённых пакетов, сначала
удаляем метапакет `kde-standard`, который содержит список всех пакетов для
обычной сборки рабочего графического окружения KDE Plasma:

```bash
sudo apt-get purge kde-standard task-kde-desktop
```

  Важно: Удаление метапакета может вызвать проблемы с работой `kde-plasma`, если
такие проблемы возникли нужна полная переустановка рабочего окружения командой:

```bash
sudo apt install --reinstall plasma-desktop plasma-workspace kde-runtime
```

  Исключение из списка приложений, подлежащих автоматическому удалению всех
текущих установленных приложений:

```bash
sudo dpkg --get-selections | grep -v "deinstall" | cut -f1 | xargs sudo apt-mark manual
```


  Или исключение из списка приложений, подлежащих автоматическому удалению
только нужных:
```bash
sudo apt-mark unmarkauto gwenview kate dolphin ark kcalc
```

  После удаления метапакета убираем из окружения `kde-plasma` эко-систему
`kmail`:

```bash
# Удаление компонентов Akonadi и KDEPIM
sudo apt-get purge \
    akonadi-backend-sqlite    # Backend для работы с SQLite в Akonadi  
    akonadi-contacts-data     # Данные для управления контактами  
    akonadi-mime-data         # Данные для обработки MIME-типов в Akonadi  
    akonadi-server           # Основной сервер Akonadi для управления данными  
    kaddressbook-data         # Данные для адресной книги  
    kaddressbook             # Приложение для управления контактами  
    kdepim-addons            # Дополнительные компоненты для KDEPIM  
    kdepim-runtime           # Основные компоненты среды выполнения KDEPIM  
    kdepim-themeeditors      # Редакторы тем для KDEPIM  
    kmail                    # Почтовый клиент KDE  
    kmailtransport-plugins   # Плагины для транспорта почты в KMail  
    korganizer               # Планировщик и органайзер KDE  
    libakonadi-data          # Основные данные для работы Akonadi  
    libakonadicalendar-data  # Данные для работы с календарем  
    libakonadisearch-bin     # Бинарные файлы для поиска в Akonadi  
    libakonadisearch-data    # Данные для поиска в Akonadi  
    libakonadisearch-plugins # Плагины для поиска в Akonadi  
    libkdepim-data           # Основные данные для KDEPIM  
    libkdepim-plugins        # Плагины для KDEPIM  
    libkmailtransport-data   # Данные для транспорта почты  
    libkpim6akonadiagentbase6 # Базовая библиотека для агентов Akonadi  
    libkpim6akonadicalendar6  # Библиотека для работы с календарем Akonadi  
    libkpim6akonadicontactcore6 # Ядро для работы с контактами Akonadi  
    libkpim6akonadicontactwidgets6 # Виджеты для работы с контактами  
    libkpim6akonadicore6      # Основные компоненты Akonadi  
    libkpim6akonadimime6      # Библиотека для работы с MIME в Akonadi  
    libkpim6akonadiprivate6   # Приватные компоненты Akonadi  
    libkpim6akonadisearchcore6 # Ядро для поиска в Akonadi  
    libkpim6akonadisearchdebug6 # Отладочные компоненты поиска  
    libkpim6akonadisearchpim6  # PIM-компоненты для поиска  
    libkpim6akonadisearchxapian6 # Интеграция с Xapian для поиска  
    libkpim6akonadiwidgets6   # Виджеты для Akonadi  
    libkpim6libkdepim6        # Основные библиотеки KDEPIM  
    libkpim6mailimporterakonadi6 # Импортер почты для Akonadi  
    libkpim6pimcommonakonadi6  # Общие компоненты PIM для Akonadi
```

  Удаляем ранее автоматически установленные пакеты, которые больше не нужны:

```bash
sudo apt autoremove --purge
```

  Проверка оставшихся хвостов от эко-системы `kmail`:

```bash
apt list --installed | grep kde-standard
apt list --installed | grep -E 'kdepim|akonadi|kmail|korganizer|kaddressbook'
```

  Очистка конфигурационных файлов эко-системы `kmail` у текущего пользователя:

```bash
rm -rf ~/.config/akonadi ~/.local/share/akonadi ~/.cache/akonadi
```


# Базовые утилиты для первоначальной установки системы


`mc` - консольный файловый менеджер
`neovim` - консольный текстовый редактор
`git` - система контроля версий
`wget` - консольный загрузчик файлов из интернета
`curl` - (от Client URL) — это универсальная утилита командной строки
    в Linux и других UNIX‑подобных системах для передачи данных по сети.
`inxi` - утилита для вывода информации о аппаратном обеспечении
`hardinfo2` - графическая утилита для вывода информации о аппаратном обеспечении
`neofetch` - консольная утилита для вывода информации о системе
`htop` - консольная утилита диспетчера задач
`cmake` - система сборки программного обеспечения из исходного кода
`ninja-build` - кроссплатформенная консольная утилита, система сборки 
    программного обеспечения из исходного кода
`openocd` - (open on-chip debugger) - открытый проект, предназначенный для
    отладки, внутрисхемного программирования и внутрисхемного тестирования
    встраиваемых систем
`synaptics` - графический интерфейс (GUI) для системы управления пакетами APT
    (Advanced Package Tool), используемой в дистрибутивах на базе
    Debian (Ubuntu, Linux Mint, Debian и др.)
`mesa-utils` - набор утилит для проверки работы 3D-графики

```bash
sudo apt-get install htop mc neovim git wget curl inxi hardinfo2 neowofetch \
synaptic mesa-utils -y
sudo apt-get remove imagemagick* -y
sudo apt-get install cmake ninja-build openocd -y
sudo apt-get autoremove
```


# Интернет утилиты:


`firefox` - стандартный браузер
`thunderbird` - почтовый клиент
`transmission` - торрент-клиент
`ktorrent` - торрент-клиент
//`deluge` - торрент-клиент
`amule` - DC-клиент
//`pidgin` - ICQ-клиент
`telegram` - клиент обмена сообщениями, установка из flatpak
`max` - российский клиент обмена сообщениями, установка из файла с сайта
`vlc` - один из лучших видеопроигрователей
`elisa`- музыкальный проигрывать для KDE Plasma от сообщества
`flatseal` - графический редактор прав (Offload Render для дискретной графики
             всё равно настраивается только через терминал... Но можно
             подглядеть основные параметры)
`kicad` - САПР-электроники
`SuperTux` - крутой клон Mario с пингвинчиком Такс

```bash
sudo apt-get install firefox-esr firefox-esr-l10n-ru thunderbird \
thunderbird-l10n-ru ktorrent \
vlc elisa \
amule # если нужен DC-клиент
```


# Flatpak - система распространения приложений linux


  `Flatpak` устанавливает приложения со своим окружением, метод схож с установкой
приложений под Windows. То есть все необходимые библиотеки, вся графика, всё,
что может использовать приложение устанавливается вместе с ним и в домашнюю
директорию пользователя. Поэтому установку приложений через `flatpak` необходимо
производить от текущего пользователя, а не использовать учётную запись `root`
(`su -`) или установку с правами суперпользователя (`sudo`).

```bash
sudo apt-get install flatpak -y
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## Установка приложений через `flatpak`:

* Установка Telegram (уже не актуально и не нужно):

```bash
flatpak install flathub telegram # а лучше не ставим
```

выбираем стабильную не веб-версию (2)
соглашаемся на установку необходимого окружения
ставим фильтр на кодек `h.264` от `cisco`, чтобы больше не выходило
предупреждение:

```bash
flatpak mask {название программы, к которой закрыли доступ}
```
* Установка Kicad.

  Рекомендуется установка именно из `flatpak`, поскольку
вместе с Kicad устанавливается `ngspice` - система моделирования
`spice-моделей`. При обычной установке, кроме того, что версия `Kicad` будет
сильно устаревшей, ещё возможны конфликты при работе программ моделирования
`spice-моделей`.

```bash
flatpak install flathub kicad
```

выбираем стабильную не веб-версию (9)
соглашаемся на установку необходимого окружения

* Установка Flatseal

Flatseal — это графическая утилита с открытым исходным кодом для управления
разрешениями приложений, установленных через систему упаковки
Flatpak itsfoss.gitlab.io +1.

Основная задача Flatseal — давать пользователям простой способ просматривать
и изменять разрешения, которые присвоены каждому Flatpak‑приложению. Это
альтернатива сложным командам в терминале для настройки доступа приложений
к ресурсам системы 

```bash
flatpak install flathub com.github.tchx84.Flatseal
```

также соглашаемся на всё и устанавливаем.

* Установка SuperTux

  Устанавливаем игрушку про пингвиненка для теста работы дискретной графики.

```bash
flatpak install flathub org.supertuxproject.SuperTux
```


# Офисные приложения:


`freecad` - САПР-механики
`vscode` - IDE для программирования
`inkscape` - редактор полиграфии
`GIMP` - редактор фотографий
`Audacity` - редактор аудио
`KdenLive` - редактор видео
`Blender` - трёхмерная графика и анимация

```bash
sudo apt-get install freecad inkscape gimp audacity kdenlive blender
```

# Эмуляторы


[Статья на habr по эмуляторам](https://habr.com/ru/articles/369599/)

dosbox - эмулятор ОС DOS
fuse - эмулятор ZX Spectrum
fceux - эмулятор NES/Dendy. В Debian доступен в репозитории unstable (sid).
nestopia - эмулятор NES/Dendy. В Debian доступен в репозитории unstable (sid).
retroarch - RetroArch сам по себе не эмулирует игры, но работает с библиотекой
    `libretro`, которая включает ядра для разных консолей.
//pcsx2 - эмулятор Playstation 2.

```bash
sudo apt-get install fuse-emulator-common fuse-emulator-sdl spectrum-roms \
nestopia retroarch
```