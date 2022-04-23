# boot

## Задание
Попасть в систему без пароля несколькими способами.
Установить систему с LVM, после чего переименовать VG.
Добавить модуль в initrd. 4(*). Сконфигурировать систему без отдельного раздела с /boot, а только с LVM Репозиторий с пропатченым grub: https://yum.rumyantsev.com/centos/7/x86_64/ PV необходимо инициализировать с параметром --bootloaderareasize 1m

# Ход выполения


### A) bin/sh
В окне выбора ядра при загрузке системы нажал "е", добавил init=/bin/sh, попал в систему без пароля (скриншот ниже)
![image](https://user-images.githubusercontent.com/98832702/163666078-54f605c7-af3a-4111-858a-34509f391877.png)
Перемонтировал систему режим read-write командой "mount -o remount,rw /", убедился в том, что файлы создаются и редактируются
![image](https://user-images.githubusercontent.com/98832702/163666563-724fc788-d4f9-491f-ad20-f3eed5c7fbfe.png)

### B) rd.break
В окне выбора ядра при загрузке системы нажал "е", добавил rd.break, попал в emergency mode.
Перемонтировал sysroot режим read-write командой "mount -o remount,rw /sysroot", заменил корневой каталог "chroot /sysroot", изменил пароль рута "passwd root", создал файл "touch /.autorelabel"

![image](https://user-images.githubusercontent.com/98832702/163769003-9d51fa92-8acd-4b5b-aeb2-c072cc665d2f.png)

При следующей загрузке попал в систему с новым паролем rootа.

### C) rw init=/sysroot/bin/sh
В окне выбора ядра при загрузке системы нажал "е", в строке начиющейся с linux16 заменил ro на rw init=/sysroot/bin/sh и нажал сtrl-x
![image](https://user-images.githubusercontent.com/98832702/163768363-02e3722e-c6f6-4d4a-a15a-72e09862e7e4.png)



## Установить систему с LVM, после чего переименовать VG

Переименовал VG
![image](https://user-images.githubusercontent.com/98832702/164888533-173664e1-69b5-4d82-87e4-0807dbc64a69.png)

Заменил имя LV в файлах  /etc/fstab, /etc/default/grub, /boot/grub2/grub.cfg
![image](https://user-images.githubusercontent.com/98832702/164888747-02d18052-1397-4e6f-81bd-0287fe0a5151.png)

Пересоздал initrd образ командой "mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)"
После перезагрузки наблюдаю изменение VG
![image](https://user-images.githubusercontent.com/98832702/164888848-cabe921b-ca34-4175-a83b-458521b797a0.png)

## Добавить модуль в initrd

В папку /usr/lib/dracut/modules.d/01test добавил два файла module-setup.sh и test.sh
```
#!/bin/bash

check() {
    return 0
}

depends() {
    return 0
}

install() {
    inst_hook cleanup 00 "${moddir}/test.sh"
}
```
```
#!/bin/bash

exec 0<>/dev/console 1<>/dev/console 2<>/dev/console
cat <<'msgend'
Hello! You are in dracut module!

msgend
sleep 10
echo " continuing...."
```
Пересобрал образ initrd "mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)"

При перезагрузке без опций rghb и quiet пронаблюдал успешный запуск. 
![image](https://user-images.githubusercontent.com/98832702/164890740-ccf34eb5-2cc0-4e09-bce2-e6c8042cd5ae.png)

