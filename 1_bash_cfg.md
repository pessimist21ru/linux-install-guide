# Настройка цветовой палитры консоли

## Содержимое `/root/.bashrc`:

```ini
# ~/.bashrc: executed by bash(1) for non-login shells.

# Note: PS1 is set in /etc/profile, and the default umask is defined
# in /etc/login.defs. You should not need this unless you want different
# defaults for root.
# PS1='${debian_chroot:+($debian_chroot)}\h:\w\$ '
# umask 022

force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then
    if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
        color_prompt=yes
    else
        color_prompt=
    fi
fi

if [ "$color_prompt" = yes ]; then
    # Мягкие цвета в стиле modercon16: приглушённый красный и серо‑голубой:
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;31m\]\u@\h\[\033[00m\]:\[\033[36m\]\w\[\033[00m\]# '
    # Вариант 1. Красный + серый:
    # PS1='${debian_chroot:+($debian_chroot)}\[\033[01;31m\]\u@\h\[\033[00m\]:\[\033[37m\]\w\[\033[00m\]# '
    # Вариант 2. Красный + тёмно‑синий:
    # PS1='${debian_chroot:+($debian_chroot)}\[\033[01;31m\]\u@\h\[\033[00m\]:\[\033[34m\]\w\[\033[00m\]# '
    # Вариант 3. Бордовый + бирюзовый:
    # PS1='${debian_chroot:+($debian_chroot)}\[\033[31m\]\u@\h\[\033[00m\]:\[\033[36m\]\w\[\033[00m\]# '

else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w# '
fi
unset color_prompt force_color_prompt

# Подключение оформления цветов для директорий
if [ -f ~/.dircolors ]; then
    eval $(dircolors -b ~/.dircolors)
else
    eval $(dircolors -b)
fi


# Алиасы с подсветкой для основных утилит
alias ls='ls --color=auto'
alias ll='ls -l --color=auto'
alias la='ls -A --color=auto'
alias l='ls -CF --color=auto'

# Поиск с подсветкой
alias grep='grep --color=auto'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'

# Системные утилиты
alias df='df -h'
alias du='du -h'
alias free='free -h'

# История команд
alias history='history | grep --color=auto'

# Сетевые утилиты
alias ping='ping -c 5'
alias traceroute='traceroute -I'

# Управление процессами
alias ps='ps auxf'
alias top='htop'  # если установлен htop

# Редактирование файлов
alias vi='vim'
alias nano='nano -W'  # подсветка синтаксиса в nano

# Безопасность
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Навигация
alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'
alias cd..='cd ..'

# Информация о системе
alias mem='free -m -l -t'
alias ports='netstat -tulanp'
alias uptime='uptime -p'

# Пакеты (Debian)
alias update='apt-get update'
alias upgrade='apt-get upgrade'
alias install='apt-get install'
alias remove='apt-get remove'
alias search='apt-cache search'

# Логи системы
alias logs='journalctl -f'
alias syslogs='journalctl -xe'

# You may uncomment the following lines if you want `ls' to be colorized:
# export LS_OPTIONS='--color=auto'
# eval "$(dircolors)"
# alias ls='ls $LS_OPTIONS'
# alias ll='ls $LS_OPTIONS -l'
# alias l='ls $LS_OPTIONS -lA'
#
# Some more alias to avoid making mistakes:
# alias rm='rm -i'
# alias cp='cp -i'
# alias mv='mv -i'
```

## Раскомментировать строки `alias` в `~/.bashrc` для пользователей:

```ini
# enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    alias dir='dir --color=auto'
    alias vdir='vdir --color=auto'

    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# colored GCC warnings and errors
export GCC_COLORS='error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01'

# some more ls aliases
alias ll='ls -l'
alias la='ls -A'
alias l='ls -CF'
```


## Содержимое `/root/.dircolors` и `~/.dircolors`:

```ini
# Цвета для устройств (тёмно‑оранжевый вместо жёлтого)
BD 00;33 # блочные устройства
CD 00;33 # символьные устройства

# Основные типы
DIR 01;34       # директории — синий
LINK 01;36     # ссылки — голубой
EXEC 01;32     # исполняемые файлы — зелёный
MISSING 01;05;37;41 # отсутствующие файлы — мигающий белый на красном
NORMAL 00     # обычный текст

# Файловые системы (ключевое для вашей проблемы)
SETUID 37;41  # файлы с SUID — белый на красном
SETGID 30;43  # файлы с SGID — чёрный на жёлтом
CAPABILITY 30;41 # файлы с capabilities — чёрный на красном

# Права доступа (исправляет жёлтый фон на NTFS)
STICKY_OTHER_WRITABLE 01;34  # /tmp директории — синие
OTHER_WRITABLE 01;34         # NTFS и общие папки — синие
STICKY 01;37;44             # sticky директории — белый на синемk

# Типы файлов
.tar 01;31
.tgz 01;31
.arc 01;31
.arj 01;31
.taz 01;31
.lzh 01;31
.lzma 01;31
.tlz 01;31
.txz 01;31
.zip 01;31
.z 01;31
.Z 01;31
.dz 01;31
.gz 01;31
.lrz 01;31
.lz 01;31
.xz 01;31
.bz2 01;31
.bz 01;31
.tbz 01;31
.tbz2 01;31
.tz 01;31
.deb 01;31
.rpm 01;31
.jar 01;31
.war 01;31
.ear 01;31
.sar 01;31
.rar 01;31
.alz 01;31
.ace 01;31
.zoo 01;31
.cpio 01;31
.7z 01;31
.rz 01;31
.cab 01;31

.jpg 01;35
.jpeg 01;35
.gif 01;35
.bmp 01;35
.pbm 01;35
.pgm 01;35
.ppm 01;35
.tga 01;35
.xbm 01;35
.xpm 01;35
.tif 01;35
.tiff 01;35
.png 01;35
.svg 01;35
.mng 01;35
.pcx 01;35
.mov 01;35
.mpg 01;35
.mpeg 01;35
.m2v 01;35
.mkv 01;35
.webm 01;35
.ogm 01;35
.mp4 01;35
.m4v 01;35
.mp4v 01;35
.vob 01;35
.qt 01;35
.nuv 01;35
.wmv 01;35
.asf 01;35
.rm 01;35
.rmvb 01;35
.flc 01;35
.avi 01;35
.fli 01;35
.flv 01;35
.gl 01;35
.dl 01;35
.xcf 01;35
.xwd 01;35
.yuv 01;35
.cgm 01;35
.emf 01;35

.pdf 01;35
.ps 01;35
.txt 01;35
.patch 01;35
.diff 01;35
.log 01;35
.tex 01;35
.doc 01;35
.docx 01;35
.odt 01;35
.rtf 01;35
.xls 01;35
.xlsx 01;35
.ppt 01;35
.pptx 01;35

.aac 00;36
.au 00;36
.flac 00;36
.mid 00;36
.midi 00;36
.mka 00;36
.mp3 00;36
.mpc 00;36
.ogg 00;36
.ra 00;36
.wav 00;36

# Архивы (с подсветкой фона)
*.tar 01;31
*.tgz 01;31
*.arc 01;31
```

## Применение новых параметров профиля в текущей сессии

```bash
source ~/.bashrc
```