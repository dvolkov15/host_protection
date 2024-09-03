# Домашнее задание к занятию "Защита хоста" - Волков Дмитрий 

## Задание 1

Установите eCryptfs.
Добавьте пользователя cryptouser.
Зашифруйте домашний каталог пользователя с помощью eCryptfs.
В качестве ответа пришлите снимки экрана домашнего каталога пользователя с исходными и зашифрованными данными.

установка

```
sudo apt install ecryptfs-utils
```

Создание пользователя cryptouser

```
adduser --encrypt-home   cryptouser
```

Вывод создание пользователя и провекра зашифрованных данных

```
root@ubuntu:/home/user# adduser --encrypt-home   cryptouser
Adding user `cryptouser' ...
Adding new group `cryptouser' (1001) ...
Adding new user `cryptouser' (1001) with group `cryptouser' ...
Creating home directory `/home/cryptouser' ...
Setting up encryption ...

************************************************************************
YOU SHOULD RECORD YOUR MOUNT PASSPHRASE AND STORE IT IN A SAFE LOCATION.
  ecryptfs-unwrap-passphrase ~/.ecryptfs/wrapped-passphrase
THIS WILL BE REQUIRED IF YOU NEED TO RECOVER YOUR DATA AT A LATER TIME.
************************************************************************


Done configuring.

Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for cryptouser
Enter the new value, or press ENTER for the default
        Full Name []: Dmitriy
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] Y
root@ubuntu:/home/user#
cryptouser@ubuntu:~$ touch test_file.txt^C
cryptouser@ubuntu:~$ ecryptfs-mount-private
Enter your login passphrase:
Inserted auth tok with sig [9b58821986aa0f97] into the user session keyring

INFO: Your private directory has been mounted.
INFO: To see this change in your current shell:
  cd /home/cryptouser

cryptouser@ubuntu:~$ touch test_file.txt
touch: cannot touch 'test_file.txt': Permission denied
cryptouser@ubuntu:~$ cd
cryptouser@ubuntu:~$ touch test_file.txt
cryptouser@ubuntu:~$ ls -la
total 60
drwx------ 2 cryptouser cryptouser 4096 Sep  3 20:14 .
drwxr-xr-x 5 root       root       4096 Sep  3 20:07 ..
-rw-r--r-- 1 cryptouser cryptouser  220 Sep  3 20:07 .bash_logout
-rw-r--r-- 1 cryptouser cryptouser 3771 Sep  3 20:07 .bashrc
lrwxrwxrwx 1 cryptouser cryptouser   36 Sep  3 20:07 .ecryptfs -> /home/.ecryptfs/cryptouser/.ecryptfs
lrwxrwxrwx 1 cryptouser cryptouser   35 Sep  3 20:07 .Private -> /home/.ecryptfs/cryptouser/.Private
-rw-r--r-- 1 cryptouser cryptouser  807 Sep  3 20:07 .profile
-rw-rw-r-- 1 cryptouser cryptouser    0 Sep  3 20:14 test_file.txt
cryptouser@ubuntu:~$
cryptouser@ubuntu:~$ pwd
/home/cryptouser
cryptouser@ubuntu:~$ exit
exit
root@ubuntu:/home/user# ls -la /home/cryptouser/
total 8
dr-x------ 2 cryptouser cryptouser 4096 Sep  3 20:07 .
drwxr-xr-x 5 root       root       4096 Sep  3 20:07 ..
lrwxrwxrwx 1 cryptouser cryptouser   56 Sep  3 20:07 Access-Your-Private-Data.desktop -> /usr/share/ecryptfs-utils/ecryptfs-mount-private.desktop
lrwxrwxrwx 1 cryptouser cryptouser   36 Sep  3 20:07 .ecryptfs -> /home/.ecryptfs/cryptouser/.ecryptfs
lrwxrwxrwx 1 cryptouser cryptouser   35 Sep  3 20:07 .Private -> /home/.ecryptfs/cryptouser/.Private
lrwxrwxrwx 1 cryptouser cryptouser   52 Sep  3 20:07 README.txt -> /usr/share/ecryptfs-utils/ecryptfs-mount-private.txt
root@ubuntu:/home/user#
```



## Заданаие 2

Установите поддержку LUKS.
Создайте небольшой раздел, например, 100 Мб.
Зашифруйте созданный раздел с помощью LUKS.
В качестве ответа пришлите снимки экрана с поэтапным выполнением задания.

Устанавливаем gparted cryptsetup (LUKS)
```
apt install gparted cryptsetup
```

Проверка
```
root@ubuntu:/home/user# cryptsetup --version
cryptsetup 2.4.3
```


```
root@ubuntu:/home/user# fdisk -l
Disk /dev/sdb: 50 GiB, 53687091200 bytes, 104857600 sectors
Disk model: Virtual disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```


Подготавливаем раздел (тип luks2)

```
root@ubuntu:/home/user# cryptsetup -y -v --type luks2 luksFormat /dev/sdb

WARNING!
========
This will overwrite data on /dev/sdb irrevocably.

Are you sure? (Type 'yes' in capital letters): YES
Enter passphrase for /dev/sdb:
Verify passphrase:
Key slot 0 created.
Command successful.
root@ubuntu:/home/user#
```

Открываем устройство /dev/sdb и задаем ему имя cryptodisk

```
root@ubuntu:/home/user# sudo cryptsetup luksOpen /dev/sdb cryptodisk
Enter passphrase for /dev/sdb:

```

Проверяем

```
root@ubuntu:/home/user# ls -al /dev/mapper/cryptodisk
lrwxrwxrwx 1 root root 7 Sep  3 20:57 /dev/mapper/cryptodisk -> ../dm-1
```

Форматируем раздел и размечаем в ext4
```
root@ubuntu:/home/user# sudo dd if=/dev/zero of=/dev/mapper/cryptodisk
```

```
root@ubuntu:/home/user# mkfs.ext4 /dev/mapper/cryptodisk
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 13103104 4k blocks and 3276800 inodes
Filesystem UUID: 24d5bc17-5dff-4862-ad72-347a4a571c6f
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424

Allocating group tables: done
Writing inode tables: done
Creating journal (65536 blocks): done
Writing superblocks and filesystem accounting information: done

```

Создаем директорию .secret а в ней тестовый файл
```
root@ubuntu:/home/user# mkdir .secret
root@ubuntu:/home/user# cd .secret/
root@ubuntu:/home/user/.secret# touch file.txt

```

итог

```
root@ubuntu:/home/user# sudo cryptsetup luksClose cryptodisk
root@ubuntu:/home/user# mount /dev/sdb /mnt/
mount: /mnt: unknown filesystem type 'crypto_LUKS'.
root@ubuntu:/home/user#

```
