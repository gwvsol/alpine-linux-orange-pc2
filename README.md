## Alpine Linux for Orange PI PC2

Создание Alpine Linux для Orange PI PC2

***

### Краткое описание

Процесс сборки [Alpine Linux](https://wiki.alpinelinux.org/wiki/DIY_Fully_working_Alpine_Linux_for_Allwinner_and_Other_ARM_SOCs) подробно описан в wiki статье [DIY Fully working Alpine Linux for Allwinner and Other ARM SOCs](https://wiki.alpinelinux.org/wiki/DIY_Fully_working_Alpine_Linux_for_Allwinner_and_Other_ARM_SOCs). Отличие заключается в том, что указанная статья рассказывает процес создания для "обычных" (32-битных) ARM. [Orange PI PC2](http://www.orangepi.org/orangepipc2/) имеет 64-битную архитектуру [aarch64](https://ru.wikipedia.org/wiki/ARM_(%D0%B0%D1%80%D1%85%D0%B8%D1%82%D0%B5%D0%BA%D1%82%D1%83%D1%80%D0%B0)), поэтому в точности использовать эту статью не возможно. Так же я отказался от использования [Armbian build tools](https://github.com/armbian/build), единственно что я позаимствовал у Armbian, это файл ```.config```, для упрощения настройки ядра перед сборкой. На момент сборки у Armbian существовал файл для ядра версии ```4.19.63-sunxi64```. Используя ```oldconfig``` этот файл был использован для сборки ядра ```4.19.80```.

Для упрощения развертывания системы сборки используется [Virtual Box](https://www.virtualbox.org/wiki/Linux_Downloads) и [Vagrant](https://help.ubuntu.ru/wiki/vagrant) 

Создан Vagrantfile для создания виртуальной машины и набор скриптов для сборки [Alpine Linux](https://alpinelinux.org/). На созданной виртуальной машине используется [Ubuntu 18.04 LTS (Bionic Beaver)](http://releases.ubuntu.com/18.04/). Все необходимые для сборки пакеты устанавливаются при создании виртуальной машины. Замечено, что после создания виртуальной машины система сразу же требует обновления некоторых пакетов. Рекомендуется сделать это сразу после создания виртуальной машины и выполнить перезагрузку. Не стоит удалять старые ядра из системы так как этот процес тянет за собой удаление пакетов которые необходимы во время сборки Alpine Linux.

Во время создания виртуальной машины в нее копируется скрипт для сборки Alpine Linux - ```make-distr```, а так же файлы ```aarch64-linux-musl.sh``` - для установки переменных окружения компилятора ```Musl``` и файл ```config-4.19.80``` для настройки ядра перед сборкой. При создании виртуальной машины, компилятор для сборки загрузщика Uboot и ядра Linux не устанавливается. 

В качестве компилятора используется [Musl Cross Compiler](https://wiki.musl-libc.org/getting-started.html) - репозиторий на [GitHub](https://github.com/richfelker/musl-cross-make). Компилятор собирается перед началом сборки Alpine Linux.



После завершения компиляции, файл загрузщика будет скопирован на основную машину для записи на SD карту

#### Запись загрузщика на SD карту

Подготавливаем SD карту для записи загрузщика, затираем загрузочную область, где ```sdX``` необходимо заменить на фактический адрес SD карты в системе

```shell

dd if=/dev/zero of=/dev/sdX bs=1M count=1

```

Записываем новый загрузщик, где ```u-boot-sunxi-with-spl.bin``` необходимо заменить на фактическое имя файла загрузщика и ```sdX``` заменить на фактический адрес SD карты в системе

```shell

dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=1024 seek=8

```


