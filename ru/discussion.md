![OpenIPC logo][logo]

Заметки от Игоря Залатова
=========================

Вопросы и ответы, коротко о главном
-----------------------------------

### Majestic

* Можно-ли вывести данные для настройки автофокуса линз вместо текущего
  sample_af в стандартный /metrics ?
* Нет, это отдельный тяжелый алгоритм, его нет смысла запускать просто так.


Сбор предложений по оформлению репозиториев проекта
---------------------------------------------------

### Предложения от @themactep

* Убрать из README файлов исходников ссылки на динамические графические
  элементы (бейджи).
* Оформить маркдаун разметку файлов для чтения в терминале при ширине поля
  не более 80 символов.
* Бейджи использовать на индивидуальных страницах проектов в вики.

### Предложения из чатов в Telegram

* Переименовать проект microbe-web в более короткое и схожее по смыслу,
  например amoeba.


Разработка нового Microbe Web UI
--------------------------------

### Цели

* Снизить порог вхождения в проект OpenIPC для тех, кто мало разбирается
  в SSH и UART консолях.
* Предоставить доступ к устройству с любого браузера, включающего мобильные.

### Безопасность

* Сделать постоянно висящее сообщение о необходимости смены дефолтного пароля.
* Разделить уровни доступа для пользователей admin (настройка сети, даты, и
  обновление стабильного релиза) и root (пролный доступ с массой диагностики).


Фичи
----

### Сброс конфигурации на заводские настройки

* Способы и варианты сброса?

### Поступили предложения

* Создание конструкторов прошивок подобных [wifi-iot](https://wifi-iot.com/) и
  [tasmocompiler](https://github.com/benzino77/tasmocompiler).
* Создание публичных FTP/TFTP/NFS серверов для тестовых сборок компонентов
  прошивки.


Программный переход с openipc-1.0 (OpenWrt) на openipc-2.x (Buildroot) 👻
-------------------------------------------------------------------------

Заходим на устройство со старым openipc-1.0 и останавливаем любыми способами
максимум сервисов кроме dropbear. Те сервисы которые "оживают" повторно
останавливаем по примеру snmp.

`/etc/init.d/snmpd stop; /etc/init.d/snmpd disable`

Меняем при помощи команды `fw_setenv` переменную `bootargs`, добавляя туда в
свою очередь переменную `init=/init`. Для моей платы строка выглядит вот так,
но у вас она может быть другой:

`fw_setenv bootargs 'console=ttyAMA 0,115200 root=/dev/mtdblock3 init=/init rootfstype=squashfs,jffs2 panic=20 mtdparts=hi_sfc:256k(boot),64k(env),2048k(kernel),5120k(rootfs),-(rootfs_data)'`

Добавляем новую переменную soc при помощи команды `fw_setenv` указав свой
процессор:

`fw_setenv soc hi3516ev100`

Прошиваем командой `flashcp` файловую систему, которую предварительно скачали
с GitHub аккаунта OpenIPC. В моём случае это раздел `/dev/mtd3`, но могут быть
отличия на каких-то старых железках:

`flashcp -v rootfs.squashfs.hi3516ev100 /dev/mtd3`

Делаем жесткий ребут плате:

`reboot -f`

Загружается **недо**-openipc-2.x с получением адреса по DHCP. После этого
выполняем команду для глобального и красивого обновления:

`sysupgrade -k -r -n`

Профит!


Ростелекомовская камера с NAND
------------------------------

```
setenv bootargs 'mem=${osmem:-32M} console=ttyAMA0,115200 panic=20 root=/dev/mtdblock3 rootfstype=squashfs init=/init mtdparts=hinand:512k(boot),512k(env),2048k(kernel),5120k(rootfs),-(rootfs_data)'

setenv bootcmd 'setenv setargs setenv bootargs ${bootargs}; run setargs; nand read 0x42000000 0x100000 0x300000; bootm 0x42000000'

setenv uk 'mw.b 0x42000000 ff 1000000; tftp 0x42000000 uImage.${soc} && nand erase 0x100000 0x200000; nand write 0x42000000 0x100000 ${filesize}'
setenv ur 'mw.b 0x42000000 ff 1000000; tftp 0x42000000 rootfs.squashfs.${soc} && nand erase 0x300000 0x500000; nand write 0x42000000 0x300000 ${filesize}'

setenv soc hi3516ev300
setenv osmem 32M
setenv totalmem 128M
setenv ipaddr 192.168.1.10
setenv serverip 192.168.1.107
saveenv

nand erase 0x800000 0x7800000

run uk; run ur; reset
```

Adapting syslogd to work with time zones other than GMT
-------------------------------------------------------

Some `syslog()` implementations like musl's[1] always send timestamps in UTC.
This change adds a new option to `syslogd`, `-Z`, to assume incoming timestamps
are always UTC and adjust them to the local timezone (of the syslogd) before
logging.

[sysklogd: add -Z option to adjust message timezones](http://lists.busybox.net/pipermail/busybox/2017-May/085437.html)


[logo]: https://cdn.themactep.com/images/logo_openipc.png
