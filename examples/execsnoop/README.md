
```
mkdir libbpf-tools-container && cd libbpf-tools-container
```


```yaml
cat <<EOF > Dockerfile
FROM ubuntu:20.04 as builder

RUN set -ex; \
    export DEBIAN_FRONTEND=noninteractive; \
    apt-get update && \
    apt-get install -y build-essential pkg-config clang llvm libcap-dev libelf-dev git && \
    git clone https://github.com/iovisor/bcc/ -b v0.19.0 --recurse-submodules && \
    cd bcc/libbpf-tools/ && \
    make && mkdir /libbpf-tools && DESTDIR=/libbpf-tools prefix="" make install

FROM ubuntu:20.04
RUN set -ex; \
    export DEBIAN_FRONTEND=noninteractive; \
    apt-get update && \
    apt-get install -y libelf-dev
COPY --from=builder /libbpf-tools/bin /usr/bin
EOF
```

```
docker build . --tag libbpf-tools:1.0
```
> Outputs :
```yaml
Sending build context to Docker daemon   2.56kB
Step 1/5 : FROM ubuntu:20.04 as builder
20.04: Pulling from library/ubuntu
ca1778b69356: Pull complete 
Digest: sha256:db8bf6f4fb351aa7a26e27ba2686cf35a6a409f65603e59d4c203e58387dc6b3
...
Setting up gnupg (2.2.19-3ubuntu2.2) ...
Processing triggers for libc-bin (2.31-0ubuntu9.9) ...
Processing triggers for ca-certificates (20211016ubuntu0.20.04.1) ...
Updating certificates in /etc/ssl/certs...
0 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
+ git clone https://github.com/iovisor/bcc/ -b v0.19.0 --recurse-submodules
Cloning into 'bcc'...
Note: switching to '4c561d037e2798563c2e87edcc5a406b020a458c'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

Submodule 'src/cc/libbpf' (https://github.com/libbpf/libbpf.git) registered for path 'src/cc/libbpf'
Cloning into '/bcc/src/cc/libbpf'...
Submodule path 'src/cc/libbpf': checked out '092a606856252091ccbded34114d544280c24d35'
+ cd bcc/libbpf-tools/
+ make
  MKDIR    .output
  MKDIR    libbpf
  LIB      libbpf.a
...
+ mkdir /libbpf-tools
+ DESTDIR=/libbpf-tools prefix= make install
   INSTALL libbpf-tools 
Removing intermediate container 686f7e22049a
 ---> 94298fa6a98e
Step 3/5 : FROM ubuntu:20.04
 ---> 88bd68917189
Step 4/5 : RUN set -ex;     export DEBIAN_FRONTEND=noninteractive;     apt-get update &&     apt-get install -y libelf-dev
 ---> Running in 2d1cdd3a4e3e
+ export DEBIAN_FRONTEND=noninteractive
...
Processing triggers for libc-bin (2.31-0ubuntu9.9) ...
Removing intermediate container 2d1cdd3a4e3e
 ---> 9ac6a2f256cc
Step 5/5 : COPY --from=builder /libbpf-tools/bin /usr/bin
 ---> 5e3959f1596a
Successfully built 5e3959f1596a
Successfully tagged libbpf:1.0
```

- [ ] Start the container running `libbpf-tools`

```
docker container run \
        --interactive --tty  \
        --privileged \
        --volume /sys/kernel/debug:/sys/kernel/debug \
        libbpf-tools:1.0
```
> root@d80c76a6e764:/# 

- [ ] Start the `execsnoop` 

```
execsnoop
```
> Returns :
```yaml
PCOMM            PID    PPID   RET ARGS
```

- [ ] Open another `SSH` connection to the VM

```
ssh -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" -p 2222 core@localhost
```

Type any commands:
```
ls -l
```

You should see the below output in the execsnoop container

```
execsnoop
```
> Outputs :
```yaml
PCOMM            PID    PPID   RET ARGS
ls               12521  7004     0 /usr/bin/ls --color=auto -l 
```
