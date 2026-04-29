# Установка Visual Studio Code


## Переходим в сеанс суперпользователя

```bash
su - # нужно ввести пароль `root`

```

или лучше:

```bash
sudo -i # вводим пароль текущего пользователя, как при входе в систему
```


## Последовательно вводим команды

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
install -o root -g root -m 644 microsoft.gpg /etc/apt/keyrings/microsoft-archive-keyring.gpg
sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/microsoft-archive-keyring.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
```

  Первая команда скачивает `gpg-ключ`, который подтверждает подлинность
программного обеспечения.
  Вторая команда устанавливает его в систему с нужными правами.
  Третья команда добавляет источник программного обеспечения, в котором
указан сервер microsoft со стабильной версией `visual studio code`.


## Обновляем список доступных программ, так как добавили новый источник
приложений. Таким образом операционная система узнает, что для установки
доступно новое приложение.

```bash
apt update
```

Устанавливаем `vscode` командой

```bash
apt install code
```

## Запускаем командой из терминала `code` (либо из меню приложений)
и устанавливаем необходимые для программирования STM32 контроллеров плагины:

```txt
C/C++
C/C++ Extension Pack
Cortex-Debug
CMake
CSpell Bundled Dictionaries - Code Spell Checker
Russian - Code Spell Checker
Russian Language Pack for Visual Studio Code
```