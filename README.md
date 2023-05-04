# flatcar-ebpf

## :a: Install the `flatcar` VM

:round_pushpin: On MacOS :apple: Install the [QEMU](https://www.qemu.org/) hypervisor

```
brew install qemu
```

:round_pushpin: Install `flatcar` VM script

- [ ] Download the `flatcar` QEMU image

```
wget https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_qemu_image.img.bz2 \
bzip2 -d flatcar_production_qemu_image.img.bz2
```

- [ ] Download the `flatcar` shelle script and enable execution

```
wget https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_qemu.sh \
chmod +x flatcar_production_qemu.sh
```

:round_pushpin: Launch the `flatcar` VM 

```
./flatcar_production_qemu.sh
```
> Ouputs
```yaml
qemu-system-x86_64: invalid accelerator kvm
qemu-system-x86_64: falling back to HVF
```

- [ ] Press `^C` to exit from VM

```
^Cqemu-system-x86_64: terminating on signal 2 from pid 25465 (<unknown process>)
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

## :b: In the FlatCar VM 

- [ ] Let’s check that the [IKHEADERS](https://cateee.net/lkddb/web-lkddb/IKHEADERS.html) config is actually set

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

- [ ] Launching the deprecated `Docker` :toolbox: [toolbox](https://github.com/docker-archive/toolbox) 

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

- [ ] Install [BCC - Tools](https://github.com/iovisor/bcc) for BPF-based Linux IO analysis, networking, monitoring, and more

```
dnf install bcc-tools xz -y
```
> Outputs :  [Complete!](dnf_install_log.md)


- [ ] Kill Container press `^]` :three: times within `1s`

```yaml
[root@localhost ~]# 
Container core-docker.iolibraryfedora-latest terminated by signal KILL.
core@localhost ~ $ 
```
# References

- [ ] [Using eBPF in Flatcar Container Linux](https://www.flatcar.org/blog/2021/04/using-ebpf-in-flatcar-container-linux/)
