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
