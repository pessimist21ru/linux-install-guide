# Установка `arm toolchain`


**[Скачать архив с toolchain под необходимую платформу:](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases/)**


**Создаём директорию в '/opt':**

```bash
mkdir -p /opt/xpack         # создание директории

chown $USER /opt/xpack      # изменения владельца на нужного
                            # пользователя
chmod -R 755 /opt/xpack     # установка прав на полный доступ
                            # указанному пользователю
                            # чтение и исполнение всем остальным
                            # без разрешения на запись
```

**Добавляем путь к директории `/opt/xpack/bin` в переменную окружения `PATH`**

```bash
echo "# Add directories to PATH variable" | tee -a ~/.bashrc
echo "export PATH="$PATH:/opt/xpack/bin"" | tee -a ~/.bashrc
```

**Перезапускаем сеанс пользователя**


**Проверяем переменную `PATH`:**

```bash
echo $PATH

#Пример вывода:
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/opt/xpack/bin
```

В конце списка через двоеточие должен быть указан путь `/opt/xpack/`.

**Проверяем,которая версия компилятора будет использоваться системой при обращении среды разработки по имени `arm-none-eabi-gcc`:**

```bash
which arm-none-eabi-gcc

# Пример вывода:
/opt/xpack/bin/arm-none-eabi-gcc
```

В возврате команды должен быть указан путь `/opt/xpack/bin`.

