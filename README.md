# boot

## Задание
Попасть в систему без пароля несколькими способами.
Установить систему с LVM, после чего переименовать VG.
Добавить модуль в initrd. 4(*). Сконфигурировать систему без отдельного раздела с /boot, а только с LVM Репозиторий с пропатченым grub: https://yum.rumyantsev.com/centos/7/x86_64/ PV необходимо инициализировать с параметром --bootloaderareasize 1m

## Ход выполения


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




