
# Система резервного копирования конфигураций

  После долгой и тщательной настройки системы возникает закономерный вопрос:
как сохранить этот хрупкий цифровой мир? Как сделать так, чтобы при
переустановке или сбое любимые конфиги, наработанные хоткеи и даже сессии
авторизации переехали на новое место без потерь?

  Ответ — создать свой собственный эталонный «скелет» конфигурации, который
будет жить в `/home/skel`, и два простых скрипта для управления им.

  Этот метод не зависит от дистрибутива, использует только встроенные средства
Linux и даёт полный контроль над тем, что именно попадает в бэкап.

---

## 1. Создание эталонной директории `/home/skel`

  `/home/skel` — это наш собственный «золотой стандарт», каталог, в котором
мы вручную собираем самые важные файлы из домашней директории пользователя и,
возможно, из `/root`. Он будет источником истины при восстановлении.

**Создаём структуру:**

```bash
sudo mkdir -p /home/skel
```

Наполняем её

Просто скопируйте в `/home/skel` те файлы и папки, которые хотите сохранить.
Например:

```bash
# Копируем свой .bashrc
cp ~/.bashrc /home/skel/

# Копируем папку с конфигами neovim
cp -r ~/.config/nvim /home/skel/.config/

# Создаём заготовку для root (если нужно)
sudo mkdir -p /home/skel/root
sudo cp /root/.bashrc /home/skel/root/
```

## 2. Скрипт автоматического обновления (update_skel.sh)

  Этот скрипт решает задачу синхронизации. Он проходит по всем файлам
внутри /home/skel и, если в вашей реальной домашней директории (или в /root)
есть такой же файл, но с более свежей датой изменения, он автоматически
обновляет эталонную копию.

  Вам больше не нужно вручную следить за тем, что изменилось.
Просто запустите скрипт.

**Создаём и сохраняем скрипт:**

```bash
sudo touch /home/skel/update_skel.sh
sudo tee /home/skel/update_skel.sh > /dev/null << 'EOF'
#!/bin/bash
# ==============================================
# update_skel.sh
# Обновляет файлы в /home/skel из домашних
# каталогов пользователя и root, если они новее.
# ==============================================

SKEL="/home/skel"

if [ "$EUID" -ne 0 ]; then
    echo "Ошибка: Запустите с sudo." >&2; exit 1
fi

# --- Синхронизация ОБЫЧНОГО ПОЛЬЗОВАТЕЛЯ ---
REAL_USER="${SUDO_USER:-$USER}"
SRC_USER=$(getent passwd "$REAL_USER" | cut -d: -f6)
if [ -z "$SRC_USER" ] || [ ! -d "$SRC_USER" ]; then
    echo "Ошибка: Не удалось найти директорию для '$REAL_USER'." >&2; exit 1
fi
echo "=== Обновление из $SRC_USER ==="
find "$SKEL" -path "$SKEL/root" -prune -o -type f -print0 | while IFS= read -r -d '' dst_file; do
    rel_path="${dst_file#$SKEL/}"
    src_file="$SRC_USER/$rel_path"
    if [ -f "$src_file" ] && [ "$src_file" -nt "$dst_file" ]; then
        echo "  [OK] Обновляю: $rel_path"
        cp -a "$src_file" "$dst_file"
    fi
done

# --- Синхронизация ROOT ---
SRC_ROOT="/root"
DST_ROOT="$SKEL/root"
mkdir -p "$DST_ROOT"
echo "=== Обновление из /root ==="
find "$DST_ROOT" -type f -print0 | while IFS= read -r -d '' dst_file; do
    rel_path="${dst_file#$DST_ROOT/}"
    src_file="$SRC_ROOT/$rel_path"
    if [ -f "$src_file" ] && [ "$src_file" -nt "$dst_file" ]; then
        echo "  [OK] Обновляю: $rel_path"
        cp -a "$src_file" "$dst_file"
    fi
done
echo "Готово! Не забудьте обновить права."
EOF
```

## 3. Скрипт обновления прав (update_skel_permissions.sh)

  После копирования файлов права доступа могут сбиться. Этот скрипт наводит
порядок, выставляя безопасные и правильные права на все файлы внутри
`/home/skel`.

**Создаём и сохраняем скрипт:**

```bash
sudo touch /home/skel/update_skel_permissions.sh
sudo tee /home/skel/update_skel_permissions.sh > /dev/null << 'EOF'
#!/bin/bash
# ==============================================
# update_skel_permissions.sh
# Выставляет безопасные права на /home/skel
# ==============================================
TARGET_DIR="/home/skel"
if [ "$EUID" -ne 0 ]; then
    echo "Ошибка: Запустите с sudo." >&2; exit 1
fi
echo "Обновляю права для $TARGET_DIR..."
chown -R root:sudo "$TARGET_DIR"
find "$TARGET_DIR" -type d -exec chmod 775 {} \;
find "$TARGET_DIR" -type f -exec chmod 664 {} \;
# Возвращаем флаг исполнения для скриптов
find "$TARGET_DIR" -type f \( -name "*.sh" -o -name "*.py" -o -name "*.pl" \) -exec chmod 775 {} \;
# Священный ключ
chown root:root "$TARGET_DIR/update_skel_permissions.sh"
chmod 700 "$TARGET_DIR/update_skel_permissions.sh"
echo "Права обновлены!"
EOF
```

**Делаем скрипты исполняемыми:**

```bash
sudo chmod +x /home/skel/update_skel.sh
sudo chmod 700 /home/skel/update_skel_permissions.sh
```

## Как этим пользоваться?

Порядок действий предельно прост:

* **Инициализация.** Вы вручную наполняете /home/skel всем, что нужно сохранить.

* **Обновление.** Поработали, изменили конфиги? Запустите:

```bash
sudo /home/skel/update_skel.sh
```

Он обновит только изменённые файлы.

**Безопасность.** Сразу после обновления запустите:

```bash
sudo /home/skel/update_skel_permissions.sh
```

Это гарантирует, что все файлы будут иметь правильные права и владельца.

## Восстановление.
  На новой системе просто скопируйте нужные файлы из `/home/skel` 
в домашнюю директорию.