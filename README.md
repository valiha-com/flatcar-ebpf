# flatcar-ebpf

```
wget https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_qemu_image.img.bz2
```


```
bzip2 -d flatcar_production_qemu_image.img.bz2
```


```
wget https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_qemu.sh
```

```
chmod +x flatcar_production_qemu.sh
```

- [ ] On MacOS :apple:

```
brew install qemu
```


```
./flatcar_production_qemu.sh
```

<img src=images/Flatcar-QEMU.png width='' height='' > </img>

- [ ] Login into the VM on another terminal

```
ssh -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" -p 2222 core@localhost
```
> Outputs :
```yaml
Warning: Permanently added '[localhost]:2222' (ED25519) to the list of known hosts.
Last login: Thu May  4 07:59:03 UTC 2023 from 10.0.2.2 on pts/0
Flatcar Container Linux by Kinvolk stable 3510.2.1 for QEMU
```

On the FlatCar VM

- [ ] Let’s check that the IKHEADERS config is actually set

```
zgrep CONFIG_IKHEADERS /proc/config.gz
```
> CONFIG_IKHEADERS=m

- [ ] Before creating the :toolbox: toolbox environment, let’s load the `kheaders` kernel module. 

We do it that way to avoid having to mount the folders containing the kernel modules into the toolbox environment we create below. If we mount the kernel modules folder then this step is not needed and the BCC tool will load the module automatically for us.

```
sudo modprobe kheaders
```

```
lsmod | grep -i kheaders
```
> kheaders         	3674112  0

- [ ] Creating the :toolbox: toolbox environment

```
toolbox --bind=/sys/kernel/debug:/sys/kernel/debug
```
> Outputs :
```yaml
docker.io/library/fedora:latest:                                                  resolved       |++++++++++++++++++++++++++++++++++++++| 
index-sha256:a1aff3e01bb667ededb2e4d895a1f1f88b7d329bd22402d4a5ba5e7f1c7a48cb:    done           |++++++++++++++++++++++++++++++++++++++| 
manifest-sha256:c901180b4ca1a75a287266bf47ca204cc8c03db5cbee26adf7c8ac6f0f7ede3e: done           |++++++++++++++++++++++++++++++++++++++| 
layer-sha256:133d222e1578e65231a672c7f389e99f4bcce344dd0ed0ded3dfaf50178b04f2:    downloading    |++++++--------------------------------| 11.0 MiB/65.1 MiB 
config-sha256:83a840c3b816b6f76c9365378267b9d7ca69e60f09d7c7963e79046314794d8e:   done           |++++++++++++++++++++++++++++++++++++++| 
elapsed: 39.0s                                                                    total:  11.0 M (288.7 KiB/s)  

unpacking linux/amd64 sha256:a1aff3e01bb667ededb2e4d895a1f1f88b7d329bd22402d4a5ba5e7f1c7a48cb...
done: 10.806778848s	
sha256:4256f48a29250219371b4dc03e10808bf74199b3100d9f26503b118555985c32
/var/lib/toolbox/core-docker.io_library_fedora-latest
Spawning container core-docker.iolibraryfedora-latest on /var/lib/toolbox/core-docker.io_library_fedora-latest.
Press ^] three times within 1s to kill container.
[root@localhost ~]# 
```

```
dnf install bcc-tools xz -y
```
> Outputs :
```yaml
Fedora 38 openh264 (From Cisco) - x86_64                                                                 824  B/s | 2.5 kB     00:03    
Fedora Modular 38 - x86_64                                                                               245 kB/s | 2.8 MB     00:11    
Fedora 38 - x86_64 - Updates                                                                             7.2 MB/s |  12 MB     00:01    
Fedora Modular 38 - x86_64 - Updates                                                                      24  B/s | 257  B     00:10    
Dependencies resolved.
=========================================================================================================================================
 Package                                    Architecture          Version                                   Repository              Size
=========================================================================================================================================
Installing:
 bcc-tools                                  x86_64                0.25.0-3.fc38                             updates                537 k
 xz                                         x86_64                5.4.1-1.fc38                              fedora                 419 k
Installing dependencies:
 bcc                                        x86_64                0.25.0-3.fc38                             updates                631 k
 binutils                                   x86_64                2.39-9.fc38                               fedora                 5.4 M
 binutils-gold                              x86_64                2.39-9.fc38                               fedora                 784 k
 bison                                      x86_64                3.8.2-4.fc38                              fedora                 1.0 M
 clang15-libs                               x86_64                15.0.7-4.fc38                             fedora                  21 M
 clang15-resource-filesystem                x86_64                15.0.7-4.fc38                             fedora                  12 k
 cpp                                        x86_64                13.0.1-0.12.fc38                          fedora                  11 M
 elfutils-debuginfod-client                 x86_64                0.189-1.fc38                              fedora                  39 k
 elfutils-libelf-devel                      x86_64                0.189-1.fc38                              fedora                  24 k
 flex                                       x86_64                2.6.4-12.fc38                             fedora                 313 k
 gc                                         x86_64                8.2.2-3.fc38                              fedora                 110 k
 gcc                                        x86_64                13.0.1-0.12.fc38                          fedora                  34 M
 glibc-devel                                x86_64                2.37-1.fc38                               fedora                  50 k
 glibc-headers-x86                          noarch                2.37-1.fc38                               fedora                 530 k
 groff-base                                 x86_64                1.22.4-11.fc38                            fedora                 1.1 M
 guile22                                    x86_64                2.2.7-7.fc38                              fedora                 6.5 M
 jansson                                    x86_64                2.13.1-6.fc38                             fedora                  44 k
 kernel-headers                             x86_64                6.2.6-300.fc38                            fedora                 1.5 M
 libbpf                                     x86_64                2:1.1.0-2.fc38                            fedora                 164 k
 libedit                                    x86_64                3.1-45.20221030cvs.fc38                   fedora                 107 k
 libmpc                                     x86_64                1.3.1-2.fc38                              fedora                  70 k
 libpkgconf                                 x86_64                1.8.0-6.fc38                              fedora                  35 k
 libtool-ltdl                               x86_64                2.4.7-6.fc38                              fedora                  37 k
 libxcrypt-devel                            x86_64                4.4.33-7.fc38                             fedora                  30 k
 libzstd-devel                              x86_64                1.5.4-1.fc38                              fedora                  51 k
 llvm15-libs                                x86_64                15.0.7-4.fc38                             fedora                  25 M
 m4                                         x86_64                1.4.19-5.fc38                             fedora                 303 k
 make                                       x86_64                1:4.4-3.fc38                              fedora                 580 k
 ncurses                                    x86_64                6.4-3.20230114.fc38                       fedora                 412 k
 openssl-devel                              x86_64                1:3.0.8-2.fc38                            fedora                 2.6 M
 perl-AutoLoader                            noarch                5.74-496.fc38                             updates                 22 k
 perl-B                                     x86_64                1.83-496.fc38                             updates                182 k
 perl-Carp                                  noarch                1.52-490.fc38                             fedora                  29 k
 perl-Class-Struct                          noarch                0.66-496.fc38                             updates                 23 k
 perl-Data-Dumper                           x86_64                2.184-491.fc38                            fedora                  56 k
 perl-Digest                                noarch                1.20-490.fc38                             fedora                  25 k
 perl-Digest-MD5                            x86_64                2.58-490.fc38                             fedora                  36 k
 perl-DynaLoader                            x86_64                1.52-496.fc38                             updates                 27 k
 perl-Encode                                x86_64                4:3.19-493.fc38                           fedora                 1.7 M
 perl-Errno                                 x86_64                1.36-496.fc38                             updates                 16 k
 perl-Exporter                              noarch                5.77-490.fc38                             fedora                  31 k
 perl-Fcntl                                 x86_64                1.15-496.fc38                             updates                 22 k
 perl-File-Basename                         noarch                2.85-496.fc38                             updates                 18 k
 perl-File-Path                             noarch                2.18-490.fc38                             fedora                  35 k
 perl-File-Temp                             noarch                1:0.231.100-490.fc38                      fedora                  59 k
 perl-File-stat                             noarch                1.12-496.fc38                             updates                 18 k
 perl-FileHandle                            noarch                2.03-496.fc38                             updates                 17 k
 perl-Getopt-Long                           noarch                1:2.54-2.fc38                             fedora                  60 k
 perl-Getopt-Std                            noarch                1.13-496.fc38                             updates                 17 k
 perl-HTTP-Tiny                             noarch                0.082-2.fc38                              fedora                  55 k
 perl-IO                                    x86_64                1.50-496.fc38                             updates                 93 k
 perl-IPC-Open3                             noarch                1.22-496.fc38                             updates                 24 k
 perl-MIME-Base64                           x86_64                3.16-490.fc38                             fedora                  30 k
 perl-Net-SSLeay                            x86_64                1.92-5.fc38                               fedora                 361 k
 perl-POSIX                                 x86_64                2.03-496.fc38                             updates                 99 k
 perl-PathTools                             x86_64                3.84-490.fc38                             fedora                  87 k
 perl-Pod-Escapes                           noarch                1:1.07-490.fc38                           fedora                  20 k
 perl-Pod-Perldoc                           noarch                3.28.01-491.fc38                          fedora                  86 k
 perl-Pod-Simple                            noarch                1:3.43-491.fc38                           fedora                 219 k
 perl-Pod-Usage                             noarch                4:2.03-4.fc38                             fedora                  40 k
 perl-Scalar-List-Utils                     x86_64                5:1.63-490.fc38                           fedora                  72 k
 perl-SelectSaver                           noarch                1.02-496.fc38                             updates                 13 k
 perl-Socket                                x86_64                4:2.036-2.fc38                            fedora                  55 k
 perl-Storable                              x86_64                1:3.26-490.fc38                           fedora                  97 k
 perl-Symbol                                noarch                1.09-496.fc38                             updates                 15 k
 perl-Term-ANSIColor                        noarch                5.01-491.fc38                             fedora                  47 k
 perl-Term-Cap                              noarch                1.18-1.fc38                               fedora                  22 k
 perl-Text-ParseWords                       noarch                3.31-490.fc38                             fedora                  16 k
 perl-Text-Tabs+Wrap                        noarch                2021.0814-490.fc38                        fedora                  22 k
 perl-Time-Local                            noarch                2:1.300-490.fc38                          fedora                  33 k
 perl-URI                                   noarch                5.17-2.fc38                               fedora                 120 k
 perl-base                                  noarch                2.27-496.fc38                             updates                 17 k
 perl-constant                              noarch                1.33-491.fc38                             fedora                  23 k
 perl-if                                    noarch                0.61.000-496.fc38                         updates                 15 k
 perl-interpreter                           x86_64                4:5.36.1-496.fc38                         updates                 73 k
 perl-libnet                                noarch                3.15-1.fc38                               fedora                 128 k
 perl-libs                                  x86_64                4:5.36.1-496.fc38                         updates                2.2 M
 perl-mro                                   x86_64                1.26-496.fc38                             updates                 30 k
 perl-overload                              noarch                1.35-496.fc38                             updates                 47 k
 perl-overloading                           noarch                0.02-496.fc38                             updates                 14 k
 perl-parent                                noarch                1:0.241-1.fc38                            fedora                  15 k
 perl-podlators                             noarch                1:5.01-2.fc38                             fedora                 125 k
 perl-vars                                  noarch                1.05-496.fc38                             updates                 14 k
 pkgconf                                    x86_64                1.8.0-6.fc38                              fedora                  41 k
 pkgconf-m4                                 noarch                1.8.0-6.fc38                              fedora                  14 k
 pkgconf-pkg-config                         x86_64                1.8.0-6.fc38                              fedora                 9.6 k
 python3-bcc                                noarch                0.25.0-3.fc38                             updates                130 k
 python3-netaddr                            noarch                0.8.0-11.fc38                             fedora                 1.6 M
 zlib-devel                                 x86_64                1.2.13-3.fc38                             fedora                  45 k
Installing weak dependencies:
 kernel-devel                               x86_64                6.2.14-300.fc38                           updates                 19 M
 libatomic                                  x86_64                13.1.1-1.fc38                             updates                 31 k
 perl-IO-Socket-IP                          noarch                0.41-492.fc38                             fedora                  41 k
 perl-IO-Socket-SSL                         noarch                2.081-1.fc38                              fedora                 227 k
 perl-Mozilla-CA                            noarch                20221114-2.fc38                           fedora                  12 k
 perl-NDBM_File                             x86_64                1.15-496.fc38                             updates                 24 k

Transaction Summary
=========================================================================================================================================
Install  97 Packages

Total download size: 143 M
Installed size: 543 M
Downloading Packages:
(1/97): bison-3.8.2-4.fc38.x86_64.rpm                                                                     93 kB/s | 1.0 MB     00:10    
(2/97): binutils-gold-2.39-9.fc38.x86_64.rpm                                                              72 kB/s | 784 kB     00:10    
(3/97): clang15-resource-filesystem-15.0.7-4.fc38.x86_64.rpm                                             275 kB/s |  12 kB     00:00    
(4/97): binutils-2.39-9.fc38.x86_64.rpm                                                                  495 kB/s | 5.4 MB     00:11    
(5/97): elfutils-debuginfod-client-0.189-1.fc38.x86_64.rpm                                               539 kB/s |  39 kB     00:00    
(6/97): elfutils-libelf-devel-0.189-1.fc38.x86_64.rpm                                                    471 kB/s |  24 kB     00:00    
(7/97): flex-2.6.4-12.fc38.x86_64.rpm                                                                    3.1 MB/s | 313 kB     00:00    
(8/97): cpp-13.0.1-0.12.fc38.x86_64.rpm                                                                   15 MB/s |  11 MB     00:00    
(9/97): gc-8.2.2-3.fc38.x86_64.rpm                                                                       575 kB/s | 110 kB     00:00    
(10/97): glibc-devel-2.37-1.fc38.x86_64.rpm                                                              885 kB/s |  50 kB     00:00    
(11/97): glibc-headers-x86-2.37-1.fc38.noarch.rpm                                                        4.7 MB/s | 530 kB     00:00    
(12/97): clang15-libs-15.0.7-4.fc38.x86_64.rpm                                                            17 MB/s |  21 MB     00:01    
(13/97): groff-base-1.22.4-11.fc38.x86_64.rpm                                                            5.7 MB/s | 1.1 MB     00:00    
(14/97): jansson-2.13.1-6.fc38.x86_64.rpm                                                                549 kB/s |  44 kB     00:00    
(15/97): guile22-2.2.7-7.fc38.x86_64.rpm                                                                  20 MB/s | 6.5 MB     00:00    
(16/97): kernel-headers-6.2.6-300.fc38.x86_64.rpm                                                        6.4 MB/s | 1.5 MB     00:00    
(17/97): libbpf-1.1.0-2.fc38.x86_64.rpm                                                                  2.6 MB/s | 164 kB     00:00    
(18/97): libedit-3.1-45.20221030cvs.fc38.x86_64.rpm                                                      1.9 MB/s | 107 kB     00:00    
(19/97): libmpc-1.3.1-2.fc38.x86_64.rpm                                                                  1.4 MB/s |  70 kB     00:00    
(20/97): libpkgconf-1.8.0-6.fc38.x86_64.rpm                                                              691 kB/s |  35 kB     00:00    
(21/97): libtool-ltdl-2.4.7-6.fc38.x86_64.rpm                                                            694 kB/s |  37 kB     00:00    
(22/97): libxcrypt-devel-4.4.33-7.fc38.x86_64.rpm                                                        491 kB/s |  30 kB     00:00    
(23/97): libzstd-devel-1.5.4-1.fc38.x86_64.rpm                                                           577 kB/s |  51 kB     00:00    
(24/97): m4-1.4.19-5.fc38.x86_64.rpm                                                                     6.5 MB/s | 303 kB     00:00    
(25/97): make-4.4-3.fc38.x86_64.rpm                                                                      7.3 MB/s | 580 kB     00:00    
(26/97): ncurses-6.4-3.20230114.fc38.x86_64.rpm                                                          3.8 MB/s | 412 kB     00:00    
(27/97): openssl-devel-3.0.8-2.fc38.x86_64.rpm                                                            17 MB/s | 2.6 MB     00:00    
(28/97): perl-Carp-1.52-490.fc38.noarch.rpm                                                              590 kB/s |  29 kB     00:00    
(29/97): perl-Data-Dumper-2.184-491.fc38.x86_64.rpm                                                      1.2 MB/s |  56 kB     00:00    
(30/97): perl-Digest-1.20-490.fc38.noarch.rpm                                                            484 kB/s |  25 kB     00:00    
(31/97): perl-Digest-MD5-2.58-490.fc38.x86_64.rpm                                                        471 kB/s |  36 kB     00:00    
(32/97): perl-Encode-3.19-493.fc38.x86_64.rpm                                                             15 MB/s | 1.7 MB     00:00    
(33/97): llvm15-libs-15.0.7-4.fc38.x86_64.rpm                                                             21 MB/s |  25 MB     00:01    
(34/97): perl-Exporter-5.77-490.fc38.noarch.rpm                                                          137 kB/s |  31 kB     00:00    
(35/97): perl-File-Path-2.18-490.fc38.noarch.rpm                                                         865 kB/s |  35 kB     00:00    
(36/97): perl-File-Temp-0.231.100-490.fc38.noarch.rpm                                                    1.4 MB/s |  59 kB     00:00    
(37/97): perl-Getopt-Long-2.54-2.fc38.noarch.rpm                                                         1.3 MB/s |  60 kB     00:00    
(38/97): perl-HTTP-Tiny-0.082-2.fc38.noarch.rpm                                                          1.1 MB/s |  55 kB     00:00    
(39/97): perl-IO-Socket-IP-0.41-492.fc38.noarch.rpm                                                      915 kB/s |  41 kB     00:00    
(40/97): perl-IO-Socket-SSL-2.081-1.fc38.noarch.rpm                                                      4.1 MB/s | 227 kB     00:00    
(41/97): perl-MIME-Base64-3.16-490.fc38.x86_64.rpm                                                       761 kB/s |  30 kB     00:00    
(42/97): perl-Mozilla-CA-20221114-2.fc38.noarch.rpm                                                      287 kB/s |  12 kB     00:00    
(43/97): perl-Net-SSLeay-1.92-5.fc38.x86_64.rpm                                                          6.9 MB/s | 361 kB     00:00    
(44/97): perl-PathTools-3.84-490.fc38.x86_64.rpm                                                         1.6 MB/s |  87 kB     00:00    
(45/97): perl-Pod-Escapes-1.07-490.fc38.noarch.rpm                                                       483 kB/s |  20 kB     00:00    
(46/97): perl-Pod-Perldoc-3.28.01-491.fc38.noarch.rpm                                                    2.0 MB/s |  86 kB     00:00    
(47/97): perl-Pod-Simple-3.43-491.fc38.noarch.rpm                                                        4.7 MB/s | 219 kB     00:00    
(48/97): perl-Pod-Usage-2.03-4.fc38.noarch.rpm                                                           751 kB/s |  40 kB     00:00    
(49/97): perl-Scalar-List-Utils-1.63-490.fc38.x86_64.rpm                                                 1.6 MB/s |  72 kB     00:00    
(50/97): perl-Socket-2.036-2.fc38.x86_64.rpm                                                             1.2 MB/s |  55 kB     00:00    
(51/97): perl-Storable-3.26-490.fc38.x86_64.rpm                                                          2.1 MB/s |  97 kB     00:00    
(52/97): perl-Term-ANSIColor-5.01-491.fc38.noarch.rpm                                                    944 kB/s |  47 kB     00:00    
(53/97): perl-Term-Cap-1.18-1.fc38.noarch.rpm                                                            552 kB/s |  22 kB     00:00    
(54/97): perl-Text-ParseWords-3.31-490.fc38.noarch.rpm                                                   478 kB/s |  16 kB     00:00    
(55/97): perl-Text-Tabs+Wrap-2021.0814-490.fc38.noarch.rpm                                               529 kB/s |  22 kB     00:00    
(56/97): perl-Time-Local-1.300-490.fc38.noarch.rpm                                                       796 kB/s |  33 kB     00:00    
(57/97): perl-URI-5.17-2.fc38.noarch.rpm                                                                 2.5 MB/s | 120 kB     00:00    
(58/97): perl-constant-1.33-491.fc38.noarch.rpm                                                          442 kB/s |  23 kB     00:00    
(59/97): perl-libnet-3.15-1.fc38.noarch.rpm                                                              3.0 MB/s | 128 kB     00:00    
(60/97): perl-parent-0.241-1.fc38.noarch.rpm                                                             314 kB/s |  15 kB     00:00    
(61/97): perl-podlators-5.01-2.fc38.noarch.rpm                                                           3.0 MB/s | 125 kB     00:00    
(62/97): pkgconf-1.8.0-6.fc38.x86_64.rpm                                                                 882 kB/s |  41 kB     00:00    
(63/97): gcc-13.0.1-0.12.fc38.x86_64.rpm                                                                  11 MB/s |  34 MB     00:03    
(64/97): pkgconf-m4-1.8.0-6.fc38.noarch.rpm                                                               49 kB/s |  14 kB     00:00    
(65/97): pkgconf-pkg-config-1.8.0-6.fc38.x86_64.rpm                                                       33 kB/s | 9.6 kB     00:00    
(66/97): zlib-devel-1.2.13-3.fc38.x86_64.rpm                                                             832 kB/s |  45 kB     00:00    
(67/97): xz-5.4.1-1.fc38.x86_64.rpm                                                                      4.9 MB/s | 419 kB     00:00    
(68/97): python3-netaddr-0.8.0-11.fc38.noarch.rpm                                                        7.8 MB/s | 1.6 MB     00:00    
(69/97): bcc-tools-0.25.0-3.fc38.x86_64.rpm                                                              884 kB/s | 537 kB     00:00    
(70/97): bcc-0.25.0-3.fc38.x86_64.rpm                                                                    949 kB/s | 631 kB     00:00    
(71/97): libatomic-13.1.1-1.fc38.x86_64.rpm                                                              790 kB/s |  31 kB     00:00    
(72/97): perl-AutoLoader-5.74-496.fc38.noarch.rpm                                                        559 kB/s |  22 kB     00:00    
(73/97): perl-B-1.83-496.fc38.x86_64.rpm                                                                 2.5 MB/s | 182 kB     00:00    
(74/97): perl-Class-Struct-0.66-496.fc38.noarch.rpm                                                      452 kB/s |  23 kB     00:00    
(75/97): perl-DynaLoader-1.52-496.fc38.x86_64.rpm                                                        560 kB/s |  27 kB     00:00    
(76/97): perl-Errno-1.36-496.fc38.x86_64.rpm                                                             275 kB/s |  16 kB     00:00    
(77/97): perl-Fcntl-1.15-496.fc38.x86_64.rpm                                                             535 kB/s |  22 kB     00:00    
(78/97): perl-File-Basename-2.85-496.fc38.noarch.rpm                                                     354 kB/s |  18 kB     00:00    
(79/97): perl-File-stat-1.12-496.fc38.noarch.rpm                                                         395 kB/s |  18 kB     00:00    
(80/97): perl-FileHandle-2.03-496.fc38.noarch.rpm                                                        308 kB/s |  17 kB     00:00    
(81/97): perl-Getopt-Std-1.13-496.fc38.noarch.rpm                                                        280 kB/s |  17 kB     00:00    
(82/97): perl-IO-1.50-496.fc38.x86_64.rpm                                                                1.4 MB/s |  93 kB     00:00    
(83/97): perl-NDBM_File-1.15-496.fc38.x86_64.rpm                                                         488 kB/s |  24 kB     00:00    
(84/97): perl-IPC-Open3-1.22-496.fc38.noarch.rpm                                                         229 kB/s |  24 kB     00:00    
(85/97): perl-POSIX-2.03-496.fc38.x86_64.rpm                                                             1.2 MB/s |  99 kB     00:00    
(86/97): perl-SelectSaver-1.02-496.fc38.noarch.rpm                                                       258 kB/s |  13 kB     00:00    
(87/97): kernel-devel-6.2.14-300.fc38.x86_64.rpm                                                          14 MB/s |  19 MB     00:01    
(88/97): perl-Symbol-1.09-496.fc38.noarch.rpm                                                             85 kB/s |  15 kB     00:00    
(89/97): perl-base-2.27-496.fc38.noarch.rpm                                                               91 kB/s |  17 kB     00:00    
(90/97): perl-if-0.61.000-496.fc38.noarch.rpm                                                            401 kB/s |  15 kB     00:00    
(91/97): perl-interpreter-5.36.1-496.fc38.x86_64.rpm                                                     1.0 MB/s |  73 kB     00:00    
(92/97): perl-mro-1.26-496.fc38.x86_64.rpm                                                               498 kB/s |  30 kB     00:00    
(93/97): perl-libs-5.36.1-496.fc38.x86_64.rpm                                                             20 MB/s | 2.2 MB     00:00    
(94/97): perl-overloading-0.02-496.fc38.noarch.rpm                                                       392 kB/s |  14 kB     00:00    
(95/97): perl-overload-1.35-496.fc38.noarch.rpm                                                          824 kB/s |  47 kB     00:00    
(96/97): perl-vars-1.05-496.fc38.noarch.rpm                                                              304 kB/s |  14 kB     00:00    
(97/97): python3-bcc-0.25.0-3.fc38.noarch.rpm                                                            2.9 MB/s | 130 kB     00:00    
-----------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                    5.2 MB/s | 143 MB     00:27     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                                 1/1 
  Installing       : m4-1.4.19-5.fc38.x86_64                                                                                        1/97 
  Installing       : libmpc-1.3.1-2.fc38.x86_64                                                                                     2/97 
  Installing       : libbpf-2:1.1.0-2.fc38.x86_64                                                                                   3/97 
  Installing       : elfutils-debuginfod-client-0.189-1.fc38.x86_64                                                                 4/97 
  Installing       : cpp-13.0.1-0.12.fc38.x86_64                                                                                    5/97 
  Installing       : bison-3.8.2-4.fc38.x86_64                                                                                      6/97 
  Installing       : flex-2.6.4-12.fc38.x86_64                                                                                      7/97 
  Installing       : libatomic-13.1.1-1.fc38.x86_64                                                                                 8/97 
  Installing       : python3-netaddr-0.8.0-11.fc38.noarch                                                                           9/97 
  Installing       : pkgconf-m4-1.8.0-6.fc38.noarch                                                                                10/97 
  Installing       : ncurses-6.4-3.20230114.fc38.x86_64                                                                            11/97 
  Installing       : libtool-ltdl-2.4.7-6.fc38.x86_64                                                                              12/97 
  Installing       : libpkgconf-1.8.0-6.fc38.x86_64                                                                                13/97 
  Installing       : pkgconf-1.8.0-6.fc38.x86_64                                                                                   14/97 
  Installing       : pkgconf-pkg-config-1.8.0-6.fc38.x86_64                                                                        15/97 
  Installing       : libzstd-devel-1.5.4-1.fc38.x86_64                                                                             16/97 
  Installing       : openssl-devel-1:3.0.8-2.fc38.x86_64                                                                           17/97 
  Installing       : zlib-devel-1.2.13-3.fc38.x86_64                                                                               18/97 
  Installing       : elfutils-libelf-devel-0.189-1.fc38.x86_64                                                                     19/97 
  Installing       : libedit-3.1-45.20221030cvs.fc38.x86_64                                                                        20/97 
  Installing       : llvm15-libs-15.0.7-4.fc38.x86_64                                                                              21/97 
  Installing       : kernel-headers-6.2.6-300.fc38.x86_64                                                                          22/97 
  Installing       : jansson-2.13.1-6.fc38.x86_64                                                                                  23/97 
  Installing       : binutils-gold-2.39-9.fc38.x86_64                                                                              24/97 
  Installing       : binutils-2.39-9.fc38.x86_64                                                                                   25/97 
  Running scriptlet: binutils-2.39-9.fc38.x86_64                                                                                   25/97 
  Running scriptlet: groff-base-1.22.4-11.fc38.x86_64                                                                              26/97 
  Installing       : groff-base-1.22.4-11.fc38.x86_64                                                                              26/97 
  Running scriptlet: groff-base-1.22.4-11.fc38.x86_64                                                                              26/97 
  Installing       : perl-Digest-1.20-490.fc38.noarch                                                                              27/97 
  Installing       : perl-Digest-MD5-2.58-490.fc38.x86_64                                                                          28/97 
  Installing       : perl-B-1.83-496.fc38.x86_64                                                                                   29/97 
  Installing       : perl-FileHandle-2.03-496.fc38.noarch                                                                          30/97 
  Installing       : perl-AutoLoader-5.74-496.fc38.noarch                                                                          31/97 
  Installing       : perl-Data-Dumper-2.184-491.fc38.x86_64                                                                        32/97 
  Installing       : perl-libnet-3.15-1.fc38.noarch                                                                                33/97 
  Installing       : perl-base-2.27-496.fc38.noarch                                                                                34/97 
  Installing       : perl-URI-5.17-2.fc38.noarch                                                                                   35/97 
  Installing       : perl-Net-SSLeay-1.92-5.fc38.x86_64                                                                            36/97 
  Installing       : perl-Text-Tabs+Wrap-2021.0814-490.fc38.noarch                                                                 37/97 
  Installing       : perl-Time-Local-2:1.300-490.fc38.noarch                                                                       38/97 
  Installing       : perl-Mozilla-CA-20221114-2.fc38.noarch                                                                        39/97 
  Installing       : perl-if-0.61.000-496.fc38.noarch                                                                              40/97 
  Installing       : perl-IO-Socket-IP-0.41-492.fc38.noarch                                                                        41/97 
  Installing       : perl-File-Path-2.18-490.fc38.noarch                                                                           42/97 
  Installing       : perl-IO-Socket-SSL-2.081-1.fc38.noarch                                                                        43/97 
  Installing       : perl-Pod-Escapes-1:1.07-490.fc38.noarch                                                                       44/97 
  Installing       : perl-Term-ANSIColor-5.01-491.fc38.noarch                                                                      45/97 
  Installing       : perl-Class-Struct-0.66-496.fc38.noarch                                                                        46/97 
  Installing       : perl-POSIX-2.03-496.fc38.x86_64                                                                               47/97 
  Installing       : perl-IPC-Open3-1.22-496.fc38.noarch                                                                           48/97 
  Installing       : perl-File-Temp-1:0.231.100-490.fc38.noarch                                                                    49/97 
  Installing       : perl-HTTP-Tiny-0.082-2.fc38.noarch                                                                            50/97 
  Installing       : perl-Term-Cap-1.18-1.fc38.noarch                                                                              51/97 
  Installing       : perl-Pod-Simple-1:3.43-491.fc38.noarch                                                                        52/97 
  Installing       : perl-Socket-4:2.036-2.fc38.x86_64                                                                             53/97 
  Installing       : perl-SelectSaver-1.02-496.fc38.noarch                                                                         54/97 
  Installing       : perl-Symbol-1.09-496.fc38.noarch                                                                              55/97 
  Installing       : perl-File-stat-1.12-496.fc38.noarch                                                                           56/97 
  Installing       : perl-podlators-1:5.01-2.fc38.noarch                                                                           57/97 
  Installing       : perl-Pod-Perldoc-3.28.01-491.fc38.noarch                                                                      58/97 
  Installing       : perl-Text-ParseWords-3.31-490.fc38.noarch                                                                     59/97 
  Installing       : perl-Fcntl-1.15-496.fc38.x86_64                                                                               60/97 
  Installing       : perl-mro-1.26-496.fc38.x86_64                                                                                 61/97 
  Installing       : perl-IO-1.50-496.fc38.x86_64                                                                                  62/97 
  Installing       : perl-overloading-0.02-496.fc38.noarch                                                                         63/97 
  Installing       : perl-Pod-Usage-4:2.03-4.fc38.noarch                                                                           64/97 
  Installing       : perl-MIME-Base64-3.16-490.fc38.x86_64                                                                         65/97 
  Installing       : perl-Scalar-List-Utils-5:1.63-490.fc38.x86_64                                                                 66/97 
  Installing       : perl-constant-1.33-491.fc38.noarch                                                                            67/97 
  Installing       : perl-parent-1:0.241-1.fc38.noarch                                                                             68/97 
  Installing       : perl-Errno-1.36-496.fc38.x86_64                                                                               69/97 
  Installing       : perl-File-Basename-2.85-496.fc38.noarch                                                                       70/97 
  Installing       : perl-Getopt-Std-1.13-496.fc38.noarch                                                                          71/97 
  Installing       : perl-Storable-1:3.26-490.fc38.x86_64                                                                          72/97 
  Installing       : perl-overload-1.35-496.fc38.noarch                                                                            73/97 
  Installing       : perl-vars-1.05-496.fc38.noarch                                                                                74/97 
  Installing       : perl-Getopt-Long-1:2.54-2.fc38.noarch                                                                         75/97 
  Installing       : perl-Carp-1.52-490.fc38.noarch                                                                                76/97 
  Installing       : perl-Exporter-5.77-490.fc38.noarch                                                                            77/97 
  Installing       : perl-PathTools-3.84-490.fc38.x86_64                                                                           78/97 
  Installing       : perl-NDBM_File-1.15-496.fc38.x86_64                                                                           79/97 
  Installing       : perl-DynaLoader-1.52-496.fc38.x86_64                                                                          80/97 
  Installing       : perl-Encode-4:3.19-493.fc38.x86_64                                                                            81/97 
  Installing       : perl-libs-4:5.36.1-496.fc38.x86_64                                                                            82/97 
  Installing       : perl-interpreter-4:5.36.1-496.fc38.x86_64                                                                     83/97 
  Installing       : glibc-headers-x86-2.37-1.fc38.noarch                                                                          84/97 
  Installing       : libxcrypt-devel-4.4.33-7.fc38.x86_64                                                                          85/97 
  Installing       : glibc-devel-2.37-1.fc38.x86_64                                                                                86/97 
  Installing       : gc-8.2.2-3.fc38.x86_64                                                                                        87/97 
  Installing       : guile22-2.2.7-7.fc38.x86_64                                                                                   88/97 
  Installing       : make-1:4.4-3.fc38.x86_64                                                                                      89/97 
  Installing       : gcc-13.0.1-0.12.fc38.x86_64                                                                                   90/97 
  Installing       : kernel-devel-6.2.14-300.fc38.x86_64                                                                           91/97 
  Running scriptlet: kernel-devel-6.2.14-300.fc38.x86_64                                                                           91/97 
  Installing       : clang15-resource-filesystem-15.0.7-4.fc38.x86_64                                                              92/97 
  Installing       : clang15-libs-15.0.7-4.fc38.x86_64                                                                             93/97 
  Installing       : bcc-tools-0.25.0-3.fc38.x86_64                                                                                94/97 
  Installing       : bcc-0.25.0-3.fc38.x86_64                                                                                      95/97 
  Installing       : python3-bcc-0.25.0-3.fc38.noarch                                                                              96/97 
  Installing       : xz-5.4.1-1.fc38.x86_64                                                                                        97/97 
  Running scriptlet: xz-5.4.1-1.fc38.x86_64                                                                                        97/97 
  Verifying        : binutils-2.39-9.fc38.x86_64                                                                                    1/97 
  Verifying        : binutils-gold-2.39-9.fc38.x86_64                                                                               2/97 
  Verifying        : bison-3.8.2-4.fc38.x86_64                                                                                      3/97 
  Verifying        : clang15-libs-15.0.7-4.fc38.x86_64                                                                              4/97 
  Verifying        : clang15-resource-filesystem-15.0.7-4.fc38.x86_64                                                               5/97 
  Verifying        : cpp-13.0.1-0.12.fc38.x86_64                                                                                    6/97 
  Verifying        : elfutils-debuginfod-client-0.189-1.fc38.x86_64                                                                 7/97 
  Verifying        : elfutils-libelf-devel-0.189-1.fc38.x86_64                                                                      8/97 
  Verifying        : flex-2.6.4-12.fc38.x86_64                                                                                      9/97 
  Verifying        : gc-8.2.2-3.fc38.x86_64                                                                                        10/97 
  Verifying        : gcc-13.0.1-0.12.fc38.x86_64                                                                                   11/97 
  Verifying        : glibc-devel-2.37-1.fc38.x86_64                                                                                12/97 
  Verifying        : glibc-headers-x86-2.37-1.fc38.noarch                                                                          13/97 
  Verifying        : groff-base-1.22.4-11.fc38.x86_64                                                                              14/97 
  Verifying        : guile22-2.2.7-7.fc38.x86_64                                                                                   15/97 
  Verifying        : jansson-2.13.1-6.fc38.x86_64                                                                                  16/97 
  Verifying        : kernel-headers-6.2.6-300.fc38.x86_64                                                                          17/97 
  Verifying        : libbpf-2:1.1.0-2.fc38.x86_64                                                                                  18/97 
  Verifying        : libedit-3.1-45.20221030cvs.fc38.x86_64                                                                        19/97 
  Verifying        : libmpc-1.3.1-2.fc38.x86_64                                                                                    20/97 
  Verifying        : libpkgconf-1.8.0-6.fc38.x86_64                                                                                21/97 
  Verifying        : libtool-ltdl-2.4.7-6.fc38.x86_64                                                                              22/97 
  Verifying        : libxcrypt-devel-4.4.33-7.fc38.x86_64                                                                          23/97 
  Verifying        : libzstd-devel-1.5.4-1.fc38.x86_64                                                                             24/97 
  Verifying        : llvm15-libs-15.0.7-4.fc38.x86_64                                                                              25/97 
  Verifying        : m4-1.4.19-5.fc38.x86_64                                                                                       26/97 
  Verifying        : make-1:4.4-3.fc38.x86_64                                                                                      27/97 
  Verifying        : ncurses-6.4-3.20230114.fc38.x86_64                                                                            28/97 
  Verifying        : openssl-devel-1:3.0.8-2.fc38.x86_64                                                                           29/97 
  Verifying        : perl-Carp-1.52-490.fc38.noarch                                                                                30/97 
  Verifying        : perl-Data-Dumper-2.184-491.fc38.x86_64                                                                        31/97 
  Verifying        : perl-Digest-1.20-490.fc38.noarch                                                                              32/97 
  Verifying        : perl-Digest-MD5-2.58-490.fc38.x86_64                                                                          33/97 
  Verifying        : perl-Encode-4:3.19-493.fc38.x86_64                                                                            34/97 
  Verifying        : perl-Exporter-5.77-490.fc38.noarch                                                                            35/97 
  Verifying        : perl-File-Path-2.18-490.fc38.noarch                                                                           36/97 
  Verifying        : perl-File-Temp-1:0.231.100-490.fc38.noarch                                                                    37/97 
  Verifying        : perl-Getopt-Long-1:2.54-2.fc38.noarch                                                                         38/97 
  Verifying        : perl-HTTP-Tiny-0.082-2.fc38.noarch                                                                            39/97 
  Verifying        : perl-IO-Socket-IP-0.41-492.fc38.noarch                                                                        40/97 
  Verifying        : perl-IO-Socket-SSL-2.081-1.fc38.noarch                                                                        41/97 
  Verifying        : perl-MIME-Base64-3.16-490.fc38.x86_64                                                                         42/97 
  Verifying        : perl-Mozilla-CA-20221114-2.fc38.noarch                                                                        43/97 
  Verifying        : perl-Net-SSLeay-1.92-5.fc38.x86_64                                                                            44/97 
  Verifying        : perl-PathTools-3.84-490.fc38.x86_64                                                                           45/97 
  Verifying        : perl-Pod-Escapes-1:1.07-490.fc38.noarch                                                                       46/97 
  Verifying        : perl-Pod-Perldoc-3.28.01-491.fc38.noarch                                                                      47/97 
  Verifying        : perl-Pod-Simple-1:3.43-491.fc38.noarch                                                                        48/97 
  Verifying        : perl-Pod-Usage-4:2.03-4.fc38.noarch                                                                           49/97 
  Verifying        : perl-Scalar-List-Utils-5:1.63-490.fc38.x86_64                                                                 50/97 
  Verifying        : perl-Socket-4:2.036-2.fc38.x86_64                                                                             51/97 
  Verifying        : perl-Storable-1:3.26-490.fc38.x86_64                                                                          52/97 
  Verifying        : perl-Term-ANSIColor-5.01-491.fc38.noarch                                                                      53/97 
  Verifying        : perl-Term-Cap-1.18-1.fc38.noarch                                                                              54/97 
  Verifying        : perl-Text-ParseWords-3.31-490.fc38.noarch                                                                     55/97 
  Verifying        : perl-Text-Tabs+Wrap-2021.0814-490.fc38.noarch                                                                 56/97 
  Verifying        : perl-Time-Local-2:1.300-490.fc38.noarch                                                                       57/97 
  Verifying        : perl-URI-5.17-2.fc38.noarch                                                                                   58/97 
  Verifying        : perl-constant-1.33-491.fc38.noarch                                                                            59/97 
  Verifying        : perl-libnet-3.15-1.fc38.noarch                                                                                60/97 
  Verifying        : perl-parent-1:0.241-1.fc38.noarch                                                                             61/97 
  Verifying        : perl-podlators-1:5.01-2.fc38.noarch                                                                           62/97 
  Verifying        : pkgconf-1.8.0-6.fc38.x86_64                                                                                   63/97 
  Verifying        : pkgconf-m4-1.8.0-6.fc38.noarch                                                                                64/97 
  Verifying        : pkgconf-pkg-config-1.8.0-6.fc38.x86_64                                                                        65/97 
  Verifying        : python3-netaddr-0.8.0-11.fc38.noarch                                                                          66/97 
  Verifying        : xz-5.4.1-1.fc38.x86_64                                                                                        67/97 
  Verifying        : zlib-devel-1.2.13-3.fc38.x86_64                                                                               68/97 
  Verifying        : bcc-0.25.0-3.fc38.x86_64                                                                                      69/97 
  Verifying        : bcc-tools-0.25.0-3.fc38.x86_64                                                                                70/97 
  Verifying        : kernel-devel-6.2.14-300.fc38.x86_64                                                                           71/97 
  Verifying        : libatomic-13.1.1-1.fc38.x86_64                                                                                72/97 
  Verifying        : perl-AutoLoader-5.74-496.fc38.noarch                                                                          73/97 
  Verifying        : perl-B-1.83-496.fc38.x86_64                                                                                   74/97 
  Verifying        : perl-Class-Struct-0.66-496.fc38.noarch                                                                        75/97 
  Verifying        : perl-DynaLoader-1.52-496.fc38.x86_64                                                                          76/97 
  Verifying        : perl-Errno-1.36-496.fc38.x86_64                                                                               77/97 
  Verifying        : perl-Fcntl-1.15-496.fc38.x86_64                                                                               78/97 
  Verifying        : perl-File-Basename-2.85-496.fc38.noarch                                                                       79/97 
  Verifying        : perl-File-stat-1.12-496.fc38.noarch                                                                           80/97 
  Verifying        : perl-FileHandle-2.03-496.fc38.noarch                                                                          81/97 
  Verifying        : perl-Getopt-Std-1.13-496.fc38.noarch                                                                          82/97 
  Verifying        : perl-IO-1.50-496.fc38.x86_64                                                                                  83/97 
  Verifying        : perl-IPC-Open3-1.22-496.fc38.noarch                                                                           84/97 
  Verifying        : perl-NDBM_File-1.15-496.fc38.x86_64                                                                           85/97 
  Verifying        : perl-POSIX-2.03-496.fc38.x86_64                                                                               86/97 
  Verifying        : perl-SelectSaver-1.02-496.fc38.noarch                                                                         87/97 
  Verifying        : perl-Symbol-1.09-496.fc38.noarch                                                                              88/97 
  Verifying        : perl-base-2.27-496.fc38.noarch                                                                                89/97 
  Verifying        : perl-if-0.61.000-496.fc38.noarch                                                                              90/97 
  Verifying        : perl-interpreter-4:5.36.1-496.fc38.x86_64                                                                     91/97 
  Verifying        : perl-libs-4:5.36.1-496.fc38.x86_64                                                                            92/97 
  Verifying        : perl-mro-1.26-496.fc38.x86_64                                                                                 93/97 
  Verifying        : perl-overload-1.35-496.fc38.noarch                                                                            94/97 
  Verifying        : perl-overloading-0.02-496.fc38.noarch                                                                         95/97 
  Verifying        : perl-vars-1.05-496.fc38.noarch                                                                                96/97 
  Verifying        : python3-bcc-0.25.0-3.fc38.noarch                                                                              97/97 

Installed:
  bcc-0.25.0-3.fc38.x86_64                                             bcc-tools-0.25.0-3.fc38.x86_64                                   
  binutils-2.39-9.fc38.x86_64                                          binutils-gold-2.39-9.fc38.x86_64                                 
  bison-3.8.2-4.fc38.x86_64                                            clang15-libs-15.0.7-4.fc38.x86_64                                
  clang15-resource-filesystem-15.0.7-4.fc38.x86_64                     cpp-13.0.1-0.12.fc38.x86_64                                      
  elfutils-debuginfod-client-0.189-1.fc38.x86_64                       elfutils-libelf-devel-0.189-1.fc38.x86_64                        
  flex-2.6.4-12.fc38.x86_64                                            gc-8.2.2-3.fc38.x86_64                                           
  gcc-13.0.1-0.12.fc38.x86_64                                          glibc-devel-2.37-1.fc38.x86_64                                   
  glibc-headers-x86-2.37-1.fc38.noarch                                 groff-base-1.22.4-11.fc38.x86_64                                 
  guile22-2.2.7-7.fc38.x86_64                                          jansson-2.13.1-6.fc38.x86_64                                     
  kernel-devel-6.2.14-300.fc38.x86_64                                  kernel-headers-6.2.6-300.fc38.x86_64                             
  libatomic-13.1.1-1.fc38.x86_64                                       libbpf-2:1.1.0-2.fc38.x86_64                                     
  libedit-3.1-45.20221030cvs.fc38.x86_64                               libmpc-1.3.1-2.fc38.x86_64                                       
  libpkgconf-1.8.0-6.fc38.x86_64                                       libtool-ltdl-2.4.7-6.fc38.x86_64                                 
  libxcrypt-devel-4.4.33-7.fc38.x86_64                                 libzstd-devel-1.5.4-1.fc38.x86_64                                
  llvm15-libs-15.0.7-4.fc38.x86_64                                     m4-1.4.19-5.fc38.x86_64                                          
  make-1:4.4-3.fc38.x86_64                                             ncurses-6.4-3.20230114.fc38.x86_64                               
  openssl-devel-1:3.0.8-2.fc38.x86_64                                  perl-AutoLoader-5.74-496.fc38.noarch                             
  perl-B-1.83-496.fc38.x86_64                                          perl-Carp-1.52-490.fc38.noarch                                   
  perl-Class-Struct-0.66-496.fc38.noarch                               perl-Data-Dumper-2.184-491.fc38.x86_64                           
  perl-Digest-1.20-490.fc38.noarch                                     perl-Digest-MD5-2.58-490.fc38.x86_64                             
  perl-DynaLoader-1.52-496.fc38.x86_64                                 perl-Encode-4:3.19-493.fc38.x86_64                               
  perl-Errno-1.36-496.fc38.x86_64                                      perl-Exporter-5.77-490.fc38.noarch                               
  perl-Fcntl-1.15-496.fc38.x86_64                                      perl-File-Basename-2.85-496.fc38.noarch                          
  perl-File-Path-2.18-490.fc38.noarch                                  perl-File-Temp-1:0.231.100-490.fc38.noarch                       
  perl-File-stat-1.12-496.fc38.noarch                                  perl-FileHandle-2.03-496.fc38.noarch                             
  perl-Getopt-Long-1:2.54-2.fc38.noarch                                perl-Getopt-Std-1.13-496.fc38.noarch                             
  perl-HTTP-Tiny-0.082-2.fc38.noarch                                   perl-IO-1.50-496.fc38.x86_64                                     
  perl-IO-Socket-IP-0.41-492.fc38.noarch                               perl-IO-Socket-SSL-2.081-1.fc38.noarch                           
  perl-IPC-Open3-1.22-496.fc38.noarch                                  perl-MIME-Base64-3.16-490.fc38.x86_64                            
  perl-Mozilla-CA-20221114-2.fc38.noarch                               perl-NDBM_File-1.15-496.fc38.x86_64                              
  perl-Net-SSLeay-1.92-5.fc38.x86_64                                   perl-POSIX-2.03-496.fc38.x86_64                                  
  perl-PathTools-3.84-490.fc38.x86_64                                  perl-Pod-Escapes-1:1.07-490.fc38.noarch                          
  perl-Pod-Perldoc-3.28.01-491.fc38.noarch                             perl-Pod-Simple-1:3.43-491.fc38.noarch                           
  perl-Pod-Usage-4:2.03-4.fc38.noarch                                  perl-Scalar-List-Utils-5:1.63-490.fc38.x86_64                    
  perl-SelectSaver-1.02-496.fc38.noarch                                perl-Socket-4:2.036-2.fc38.x86_64                                
  perl-Storable-1:3.26-490.fc38.x86_64                                 perl-Symbol-1.09-496.fc38.noarch                                 
  perl-Term-ANSIColor-5.01-491.fc38.noarch                             perl-Term-Cap-1.18-1.fc38.noarch                                 
  perl-Text-ParseWords-3.31-490.fc38.noarch                            perl-Text-Tabs+Wrap-2021.0814-490.fc38.noarch                    
  perl-Time-Local-2:1.300-490.fc38.noarch                              perl-URI-5.17-2.fc38.noarch                                      
  perl-base-2.27-496.fc38.noarch                                       perl-constant-1.33-491.fc38.noarch                               
  perl-if-0.61.000-496.fc38.noarch                                     perl-interpreter-4:5.36.1-496.fc38.x86_64                        
  perl-libnet-3.15-1.fc38.noarch                                       perl-libs-4:5.36.1-496.fc38.x86_64                               
  perl-mro-1.26-496.fc38.x86_64                                        perl-overload-1.35-496.fc38.noarch                               
  perl-overloading-0.02-496.fc38.noarch                                perl-parent-1:0.241-1.fc38.noarch                                
  perl-podlators-1:5.01-2.fc38.noarch                                  perl-vars-1.05-496.fc38.noarch                                   
  pkgconf-1.8.0-6.fc38.x86_64                                          pkgconf-m4-1.8.0-6.fc38.noarch                                   
  pkgconf-pkg-config-1.8.0-6.fc38.x86_64                               python3-bcc-0.25.0-3.fc38.noarch                                 
  python3-netaddr-0.8.0-11.fc38.noarch                                 xz-5.4.1-1.fc38.x86_64                                           
  zlib-devel-1.2.13-3.fc38.x86_64                                     

Complete!
```

- [ ] Kill Container press `^]` :three: times within `1s`

```yaml
[root@localhost ~]# 
Container core-docker.iolibraryfedora-latest terminated by signal KILL.
core@localhost ~ $ 
```
# References

- [ ] [Using eBPF in Flatcar Container Linux](https://www.flatcar.org/blog/2021/04/using-ebpf-in-flatcar-container-linux/)
