+++
date = "2016-09-07T09:00:00+03:00"
draft = false
title = "Выбор простого бэкапа для Windows"
tags = ["windows"]

+++

Доступные и функциональные системы бэкапа под Windows.

Требования:

* Основное - использование жестких или символьных ссылок для дедупликации содержимого архива.
* Дополнительное - использование VSS (Volume Shadow Copy Service) для предварительного создания снэпшота.

Не рассматриваются сложные распределенные (и дорогие) системы бэкапа. Один компьютер, одна программа или скрипт.

### [Dublicati](http://www.duplicati.com/)

Duplicati is a free backup client that securely stores encrypted, incremental, compressed backups on cloud storage services and remote file servers. It works with Amazon S3, Windows Live SkyDrive, Google Drive (Google Docs), Rackspace Cloud Files or WebDAV, SSH, FTP (and many more).

Рассматривалась версия 1.3.4. 

Особенности:

* архив - zip-файл.

Преимущества:

* бесплатность;
* кроссплатформенность (требуются .NET 2.0+ или Mono);
* полные и инкрементальные бэкапы;
* есть возможность шифрования архивов;
* используется VSS;
* есть русский язык;
* есть мастер восстановления.

Недостатки:

* корявый интерфейс;
* файловые ссылки не используются;
* наглядность при просмотре архивов отсутствует;
* субъективно низкая скорость работы.

### [Cobian Backup](http://www.cobiansoft.com/)

Рассматривалась версия 11.

Особенности:

* архив - каталог.

Преимущества:

* отличный интерфейс;
* бесплатность;
* полные, инкрементальные и дифференциальные бэкапы;
* есть возможность шифрования файлов в архиве (каждый файл пакуется в зашифрованный архив);
* субъективно высокая скорость работы.
* есть русский язык;
* используется VSS.

Недостатки:

* файловые ссылки не используются;
* вообще, отличная программа, но отсутствие программы восстановления сводит все преимущества на нет - не руками же восстанавливать файлы из инкрементальных (разностных) архивов.

### [Hardlink Backup](http://www.lupinho.net/hardlinkbackup/)

Рассматривалась версия 2.1.1.

Особенности:

* архив - каталог.

Идеальная программа - подходит и под основное, и под дополнительное требования.

Преимущества:

* отличный интерфейс;
* используется VSS;
* используются файловые ссылки;
* наглядный просмотр архивов (из Проводника или программы);
* субъективно высокая скорость работы;
* есть возможность восстановления из программы.

Недостатки:

* нет русского языка;
* самый главный минус - в бесплатной версии отсутствует запуск процесса бэкапа по расписанию.
* платная версия стоит 29 евро.

### [Скрипт RoboShot](http://roboshot.sourceforge.net/)

Рассматривалась версия 0.3.

Особенности:

* написан на PowerShell;
* для копирования использует robocopy (т.е. высокая скорость работы);
* вроде использует VSS;
* использует файловые ссылки.

Недостатки:

* требует доработки напильником (например, надо править регекспы с немецкой локализации Windows на русской);
* на мой взгляд, слишком большой объем кода.

### [Комбайн из bat-скрипта, rsync и vssadmin](http://blog.jay2k1.com/2011/08/13/how-to-create-rsync-like-hard-link-backups-with-vss-on-windows/)

Недостатки:

* требует доработки напильником;
* для rsync требуется cygwin, что субъективно дает просадку по скорости.

### [Утилита LN и скрипт DeloreanCopy](http://schinagl.priv.at/nt/ln/ln.html)
На мой взгляд, самое простое и лучшее решение, если не нужен VSS из коробки.

* [Link Shell Extension](http://schinagl.priv.at/nt/hardlinkshellext/hardlinkshellext.html) 
* [ln](http://schinagl.priv.at/nt/ln/ln.html) - command line hardlinks 

DeLoreanCopy.bat требует 3 ясных параметра:
```
Usage: DeLoreanCopy <SourcePath> <DestPath> (<KeepMaxCopies>)
SourcePath directory containing the files to be backed up
DestPath directory where "DeLorean copy" sets will be created
KeepMaxCopies (opt.) remove any exceeding number of DLC sets before copying
e.g. DeLoreanCopy c:\data\source c:\data\backup 90
```

Преимущества:

* простота;
* бесплатность;
* используются файловые ссылки;
* очень просто прикручивается VSS.

Создание снэпшота с помощью [vshadow](http://msdn.microsoft.com/en-us/library/windows/desktop/bb530725(v=vs.85).aspx) и запуск скрипта vss-exec-delorean.cmd:
```
vshadow.exe -script=vss-setvar.cmd -exec=vss-exec-delorean.cmd c:
```

Скрипт vss-exec-delorean.cmd:
```
REM
REM called from vshadow via commandline e.g
REM
REM vshadow.exe -script=vss-setvar.cmd -exec=vss-exec.cmd e:
REM
REM vss-setvar.cmd is generated by vshadow.exe
REM
call vss-setvar.cmd
REM assign a DOS device to the created snapshot
REM
dosdev x: %shadow_device_1%
REM run the intended copy job
REM
DeLoreanCopy.bat X:\Users\paul\Documents C:\backup\ 3
REM finally remove the drive letter
REM
dosdev -r -d x:
```