# Create a Fedora virtual machine

In this paper, we will create a new Fedora cloud virtual machine with QEMU/KVM

Prerequisites:
- Your host OS is a linux machine (better to run KVM ;))
- You already have QEMU/KVM installed on your host machine

### Download the image

You can check the last reease of Fedora cloud on the official website:
```https://fedoraproject.org/fr/cloud/download```

And download it from your browser or with to `curl`:
```
[paul@fedora vm]$ curl https://mirror.in2p3.fr/pub/fedora/linux/releases/40/Cloud/x86_64/images/Fedora-Cloud-Base-Generic.x86_64-40-1.14.qcow2 -o linux.qcow2
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  379M  100  379M    0     0  25.9M      0  0:00:14  0:00:14 --:--:-- 76.1M
```

### Configure the virtual machine

Fedora cloud is provided with no admin user and no credentials. This is why we need to create a root user. For this, we use `virt-customize`

```
[paul@fedora vm]$ virt-customize -a linux.qcow2 --root-password password:yoursecretpassword
[   0.0] Examining the guest ...
[   7.3] Setting a random seed
[   7.3] Setting passwords
[   8.6] SELinux relabelling
[  27.1] Finishing off
```

## I: configure virtual machine with virt-manager

