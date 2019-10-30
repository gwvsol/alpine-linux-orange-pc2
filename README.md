## Alpine Linux for Orange PI PC2

Создание Alpine Linux для Orange PI PC2

***

### Краткое описание

Процесс сборки [Alpine Linux](https://wiki.alpinelinux.org/wiki/DIY_Fully_working_Alpine_Linux_for_Allwinner_and_Other_ARM_SOCs) подробно описан в [DIY Fully working Alpine Linux for Allwinner and Other ARM SOCs](https://wiki.alpinelinux.org/wiki/DIY_Fully_working_Alpine_Linux_for_Allwinner_and_Other_ARM_SOCs). В полной точности указанную статью использовать не возможно так как [Orange PI PC2](http://www.orangepi.org/orangepipc2/) имеет 64-битную архитектуру [Aarch64](https://ru.wikipedia.org/wiki/ARM_(%D0%B0%D1%80%D1%85%D0%B8%D1%82%D0%B5%D0%BA%D1%82%D1%83%D1%80%D0%B0))

В качестве компилятора используется [Musl Cross Compiler](https://wiki.musl-libc.org/getting-started.html) - [GitHub](https://github.com/richfelker/musl-cross-make)

Для развертывания системы сборки используется [Virtual Box](https://www.virtualbox.org/wiki/Linux_Downloads) и [Vagrant](https://help.ubuntu.ru/wiki/vagrant). Создан Vagrantfile и набор скриптов для сборки [Alpine Linux](https://alpinelinux.org/). На виртуальной машине используется [Ubuntu 18.04 LTS (Bionic Beaver)](http://releases.ubuntu.com/18.04/)

Во время развертывания виртуальной машины устанавливаются все необходимые пакеты, копируется скрипт для сборки ```make-distr```, файл ```aarch64-linux-musl.sh```, для установки переменных окружения компилятора ```Musl Cross Compiler``` и ```config-4.19.80``` для настройки ядра перед его сборкой. Компилятор ```Musl Cross Compiler``` для сборки загрузщика Uboot и ядра Linux не устанавливается, его необходимо будет собрать из исходников перед началом сборки Alpine Linux. Так же имеется скрипит ```make-alpine-aarch64``` упрощающий установку дистрибутива на SD карту

### Что необходимо для сборки
* Исходный код загрузщика [u-boot](https://gitlab.denx.de/u-boot/u-boot.git)
* [Trusted Firmware-A](https://github.com/ARM-software/arm-trusted-firmware)
* Исходный код [Musl Cross Make](https://github.com/richfelker/musl-cross-make)
* Исходный код ядра [Linux](https://cdn.kernel.org/pub/linux/kernel/)
* Файлы ```initramfs``` и ```modloop``` из Alpine Linux - [Generic ARM Aarch64](https://alpinelinux.org/downloads/)

На момент написания ```README``` версия ядра Linux (LTS) - ```4.19.80``` и версия Alpine Linux - ```3.10.3```

### Подготовка к сборке

```shell
git clone https://github.com/gwvsol/Alpine-Linux-Orange-PC2.git

cd Alpine-Linux-Orange-PC2

-rw-r--r-- 1 work work    116 окт 29 12:06 aarch64-linux-musl.sh
-rw-r--r-- 1 work work 151902 окт 29 12:11 config-4.19.80
-rwxr-xr-x 1 work work   1630 окт 29 12:06 make-alpine-aarch64
-rwxr-xr-x 1 work work   7941 окт 29 19:05 make-distr
-rw-r--r-- 1 work work   1274 окт 29 12:06 README.md
-rw-r--r-- 1 work work   1699 окт 29 12:06 Vagrantfile
````
По необходимости внести изменения в ```Vagrantfile```, возможно следует изменить под ваши потребности следующие парамеметры ```config.vm.hostname = "VM06-Alpine-for-OrangePC2"```, ```config.vm.network "public_network", ip: "192.168.10.36"``` Следует так же убедится что в домашней директории имеются файлы ```id_rsa_vagrant``` и ```id_rsa_vagrant.pub```. Размер диска указан минимальный ```config.disksize.size = '20GB'``` для данной работы.

```vagrant up```

```vagrant ssh```

#### Сборка Alpine Linux

Далее все действия происходят внутри работающей виртуальной машины

````shell
vagrant@VM06-Alpine-for-OrangePC2:~$ make-distr 
###### - Работа со скриптом сборки make-distr help
````

````shell
vagrant@VM06-Alpine-for-OrangePC2:~$ make-distr help
###### - Получить переменные окружения и установленными компиляторы: make-distr env
###### - Сборка и настройка компилятора Musl: make-distr set
###### - Сборка загрузщика U-BOOT для orangepi_pc2: make-distr uboot
###### - Сборка ядра Linux для orangepi_pc2: make-distr kernel
###### - Сборка initramfs и modloop для orangepi_pc2: make-distr fs
###### - Полная сборка UBOOT, Kernel, initramfs и modloop для orangepi_pc2: make-distr all
````

````shell
vagrant@VM06-Alpine-for-OrangePC2:~$ make-distr env
####### Переменные окружения и установленные компиляторы
####### Компилятор Musl в системе не установлен
````
Запуск команды ```make-distr set``` запустит длительный процес сборки компилятора ```Musl Cross Compiler```
После успешного завершения установки компилятора ```Musl Cross Compiler``` повторный ввод команды ```make-distr set``` должен дать следующий результат:
````shell
vagrant@VM06-Alpine-for-OrangePC2:~$ make-distr set
####### Компилятор Musl в системе установлен
####### /usr/lib/gcc/musl-aarch64-linux-gnu
####### Глобальные переменные для компилятора
ARCH=arm64
CROSS_COMPILE=aarch64-linux-musl-
PATH=/usr/lib/gcc/musl-aarch64-linux-gnu/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
````
Ввод команды ```make-distr env``` должен давать аналогичный результат
````shell
vagrant@VM06-Alpine-for-OrangePC2:~$ make-distr env
####### Переменные окружения и установленные компиляторы
####### Компилятор Musl в системе установлен
####### /usr/lib/gcc/musl-aarch64-linux-gnu
####### ARCH=arm64
####### CROSS_COMPILE=aarch64-linux-musl-
####### PATH=/usr/lib/gcc/musl-aarch64-linux-gnu/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
````
Далее можно последовательно вводить команды ```make-distr uboot```, ```make-distr kernel```, ```make-distr fs``` для сборки вначале загрузщика u-boot, затем ядра Linux и в завершение ```initramfs``` и ```modloop```. Или же сразу ввести команду ```make-distr all``` для последовательного выполнения всех указанных операций сборки загрузщика, ядра и самого дистрибутива.

В результате описанных действий должен получиться следующий вариант
```shell
vagrant@VM06-Alpine-for-OrangePC2:~$ ls -l
total 12
drwxrwxr-x  4 vagrant vagrant 4096 Oct 29 18:50 alpine-aarch64
drwxr-xr-x  2 vagrant vagrant 4096 Oct 29 18:01 conf
drwxrwxr-x 26 vagrant vagrant 4096 Oct 29 18:44 linux-4.19.80
```
В директории ```alpine-aarch64``` будет находится результат нашей сборки
```shell
vagrant@VM06-Alpine-for-OrangePC2:~/alpine-aarch64$ ls -l
total 198668
-rw-rw-r-- 1 vagrant vagrant   3043842 Oct 29 18:19 System.map-4.19.80
-rw-rw-r-- 1 vagrant vagrant  31696603 Oct 29 18:50 alpine-aarch64-initramfs
-rw-r--r-- 1 vagrant vagrant  51867648 Oct 29 18:50 alpine-aarch64-modloop
-rw-rw-r-- 1 vagrant vagrant 101867460 Oct 29 18:50 alpine-orangepi-pc2-3.10.3-aarch64.tar.gz
-rw-rw-r-- 1 vagrant vagrant       390 Oct 29 18:50 boot.cmd
-rw-rw-r-- 1 vagrant vagrant       462 Oct 29 18:50 boot.scr
-rw-rw-r-- 1 vagrant vagrant    151902 Oct 29 18:19 config-4.19.80
drwxrwxr-x 3 vagrant vagrant      4096 Oct 29 18:19 dtbs
drwxrwxr-x 3 vagrant vagrant      4096 Oct 29 18:49 lib
-rw-rw-r-- 1 vagrant vagrant     17440 Oct 29 18:49 sun50i-h5-orangepi-pc2.dtb
-rw-rw-r-- 1 vagrant vagrant    711136 Oct 29 18:03 u-boot-2019-10-29-18-01-44.bin
-rw-rw-r-- 1 vagrant vagrant  14047240 Oct 29 18:19 vmlinuz-4.19.80
```
Файл ```alpine-orangepi-pc2-3.10.3-aarch64.tar.gz``` можно скопировать и используя скрипт ```make-alpine-aarch64``` записать на SD карту. Скрипт ```make-alpine-aarch64``` принимает два параметра, второй параметр в формате ```sdX```
```shell
work@work:~$ ./make-alpine-aarch64 
######## Укажите первым параметром архив с дистрибутивом
######## Укажите вторым параметром SD карту на которую будем устанaвливать дистрибутив
######## Второй параметр указывается в формате sdX
```
После чего SD карту можно установить в ```Orange Pi PC2``` и завершить установку Alpine Linux
````shell
Welcome to Alpine Linux 3.10
Kernel 4.19.80 on an aarch64 (/dev/ttyS0)

tvhost login: root
Password: 
Welcome to Alpine!

The Alpine Wiki contains a large amount of how-to guides and general
information about administrating Alpine systems.
See <http://wiki.alpinelinux.org/>.

You can setup the system with the command: setup-alpine

You may change this message by editing /etc/motd.

tvhost:~# lscpu
Architecture:        aarch64
Byte Order:          Little Endian
CPU(s):              4
On-line CPU(s) list: 0-3
Thread(s) per core:  1
Core(s) per socket:  4
Socket(s):           1
NUMA node(s):        1
Vendor ID:           ARM
Model:               4
Model name:          Cortex-A53
Stepping:            r0p4
BogoMIPS:            48.00
NUMA node0 CPU(s):   0-3
Flags:               fp asimd evtstrm aes pmull sha1 sha2 crc32 cpuid
````