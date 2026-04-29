# Настройка приложений Flatpak для работы с дискретной графикой NVidia в режиме OffloadRender


  Главная боль: в Flatpak нужно вручную разрешить доступ к устройствам и
библиотекам Nvidia.

## Базовая теория:

    Приложению нужен доступ к `/dev/dri` (там есть render-ноды: `renderD128`
    для `Intel` и обычно `renderD129` для `NVidia`).

    Ему нужен доступ к библиотекам NVidia, которые лежат не в стандартном
    `/usr/lib`, а монтируются Flatpak отдельно из расширения 
    `org.freedesktop.Platform.GL.nvidia-...`.

Поэтому:

```bash
# Проверяем версию драйвера NVidia
nvidia-smi

# Проверяем нет ли уже установленного пакета nvidia
flatpak list | grep GL.nvidia

# Устанавливаем расширение GL для Nvidia, соответствующее вашему драйверу
flatpak install org.freedesktop.Platform.GL.nvidia-550-120  # пример, версия должна совпадать с host-драйвером!
```

## Универсальный шаблон (для OpenGL/GLX приложений):

```bash
flatpak override --user \
    --device=dri \
    --device=all \
    --filesystem=host \
    --env=__NV_PRIME_RENDER_OFFLOAD=1 \
    --env=__GLX_VENDOR_LIBRARY_NAME=nvidia \
    org.имя.приложения
```

**Для Vulkan-приложений:**

```bash
flatpak override --user \
    --device=dri \
    --device=all \
    --filesystem=host \
    --env=__NV_PRIME_RENDER_OFFLOAD=1 \
    --env=VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/nvidia_icd.json \
    --env=__VK_LAYER_NV_optimus=NVIDIA_only \
    org.имя.приложения
```


**Через графический интерфейс (Flatseal):**

Запустить Flatseal

Выбрать приложение из списка

В разделе Устройства: включить GPU Acceleration, All Devices, DRI

В разделе Файловая система: включить All System Files

В разделе Переменные окружения:
    добавить __NV_PRIME_RENDER_OFFLOAD=1 и __GLX_VENDOR_LIBRARY_NAME=nvidia

**Проверенный пример (SuperTux):**

```bash
flatpak install flathub org.supertuxproject.SuperTux
flatpak override --user \
    --device=dri --device=all --filesystem=host \
    --env=__NV_PRIME_RENDER_OFFLOAD=1 \
    --env=__GLX_VENDOR_LIBRARY_NAME=nvidia \
    org.supertuxproject.SuperTux
flatpak run org.supertuxproject.SuperTux
# В nvidia-smi должен появиться процесс supertux2
# Также его можно увидеть через nvtop
```

**Запуск «дочерних процессов»**

  При запуске главного процесса приложения с нужными переменными, дочерние
процессы по умолчанию наследуют окружение родительского процесса. Поэтому,
если приложение запущено через Flatpak с вышеуказанными env-переменными, то
специально для каждого отдельного процесса ничего делать не нужно — они внутри
того же sandbox'а (песочницы).

  Можно узнать, на каком графическом ядре выполняются программы, через `nvtop`
или запустив диагностическую команду внутри песочницы. Для этого:

```bash
# Открываем bash внутри контейнера приложения
flatpak run --env=__NV_PRIME_RENDER_OFFLOAD=1 \
    --env=__GLX_VENDOR_LIBRARY_NAME=nvidia \
    --command=bash org.supertuxproject.SuperTux

# Внутри контейнера проверяем:
echo $__NV_PRIME_RENDER_OFFLOAD
glxinfo -B | grep "OpenGL vendor"
```

  **Примечание:** Каждый вызов flatpak run создаёт новый экземпляр песочницы.
Чтобы проверить переменные внутри того же окружения, в котором работает
приложение, передавайте --env при запуске bash.


## Отдельный запуск на дискретной графике

  Если не нужно «навечно» привязывать приложение к Nvidia, можно сделать алиас
для запуска вручную:

```bash
alias [название_приложения]-nv='flatpak run --env=__NV_PRIME_RENDER_OFFLOAD=1 --env=__GLX_VENDOR_LIBRARY_NAME=nvidia org.имя.приложения'
```
  Тогда простое приложение из меню будет на Intel,
а `[название_приложения]-nv` в терминале — на GeForce.



## Особые случаи и исключения

Некоторые Flatpak-приложения (например, KiCad 10) могут падать с ошибкой сегментации при попытке использовать Nvidia внутри песочницы. Причина — конфликт библиотек GLX между Flatpak-рантаймом и проприетарным драйвером Nvidia.

Решение: Для таких приложений используйте встроенную графику Intel:

```bash
flatpak override --user --reset org.kicad.KiCad
flatpak override --user --device=dri --socket=x11 --socket=wayland org.kicad.KiCad
```
Встроенной графики Intel достаточно для комфортной работы в KiCad.

## Полезные команды

```bash
# Посмотреть override конкретного приложения
cat ~/.local/share/flatpak/overrides/org.имя.приложения

# Сбросить все override приложения
flatpak override --user --reset org.имя.приложения

# Запустить приложение с переменными без override (разово)
flatpak run --env=__NV_PRIME_RENDER_OFFLOAD=1 --env=__GLX_VENDOR_LIBRARY_NAME=nvidia org.имя.приложения

# Проверить, какое GPU видит приложение
flatpak run --command=glxinfo --env=__NV_PRIME_RENDER_OFFLOAD=1 --env=__GLX_VENDOR_LIBRARY_NAME=nvidia org.kicad.KiCad -B | grep "OpenGL vendor"
```