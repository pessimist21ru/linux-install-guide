# **Включение sudo**

```bash
su -
usermod -aG sudo $USER
```

# **Установка драйвера nvidia и заголовочных файлов ядра**

```bash
sudo apt update
sudo apt full-upgrade
sudo apt install linux-headers-$(uname -r) neovim
sudo nvim /etc/apt/sources.list
```
В строчках с адресами основных репозиториев необходимо добавить ключи:
```ini
deb http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
deb-src http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
```

```bash
sudo apt update
# sudo apt install nvidia-detect
# nvidia-detect
sudo apt install nvidia-driver firmware-misc-nonfree
nvidia-smi
sudo usermod -a -G video,render $USER
```

```bash
# sudo apt install nvidia-prime
# sudo prime-select nvidia //переключение на nvidia
# sudo prime-select intel  //переключение на intel
# sudo systemctl enable nvidia-suspend.service nvidia-hibernate.service nvidia-resume.service
```

# **Установка clevo-fan-control и драйверов tuxedo**

```bash
git clone https://github.com/pessimist21ru/clevo-fan-control.git
cd clevo-fan-control
chmod +x install.sh
sudo ./install.sh
```

или в ручном режиме:

```bash
git clone https://github.com/pessimist21ru/clevo-fan-control.git
cd clevo-fan-control
mkdir build && cd build
cmake ..
make -j$(nproc)
sudo make install
sudo systemctl daemon-reload
sudo systemctl enable clevo-fan-control
sudo systemctl start clevo-fan-control
sudo systemctl status clevo-fan-control.service
```