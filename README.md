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


# References

- [ ] [Using eBPF in Flatcar Container Linux](https://www.flatcar.org/blog/2021/04/using-ebpf-in-flatcar-container-linux/)
