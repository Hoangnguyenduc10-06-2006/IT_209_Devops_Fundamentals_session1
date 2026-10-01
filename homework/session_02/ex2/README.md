# Bai Tap 2 - Tao Non-root User va Cau Hinh SSH Key tren Ubuntu Server

## Muc Tieu

Tao tai khoan nguoi dung thuong (non-root user) ten **devops** tren Ubuntu Server de van hanh theo nguyen tac **Least Privilege** (Dac quyen toi thieu).
Cap quyen quan tri qua nhom `sudo` va cau hinh SSH Key de co the dang nhap truc tiep ma khong can dung tai khoan `root`.

---

## Boi Canh

> Dung `root` truc tiep cho moi thao tac hang ngay la rat rui ro vi bat ky lenh nao cung duoc thuc thi voi quyen cao nhat, khong co lop bao ve nao. Thay vao do, tao user `devops` co the su dung `sudo` khi can, giu nguyen tac Least Privilege.

---

## Cac Lenh Thuc Hien

### Buoc 1 - Dang Nhap vao Droplet bang root

Tu may ca nhan, ket noi SSH vao Droplet (thay IP bang dia chi thuc):

```bash
ssh -i ~/.ssh/id_ed25519 root@<IP_ADDRESS_DROPLET>
```

---

### Buoc 2 - Tao User Moi Ten devops

```bash
adduser devops
```

He thong se yeu cau nhap mat khau va thong tin cho user moi:

```
Adding user `devops' ...
Adding new group `devops' (1000) ...
Adding new user `devops' (1000) with group `devops' ...
Creating home directory `/home/devops' ...
Copying files from `/etc/skel' ...
New password:                    <- Nhap mat khau cho user devops
Retype new password:             <- Xac nhan mat khau
passwd: password updated successfully
Changing the user information for devops
Enter the new value, or press ENTER for the default
        Full Name []: DevOps User
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] Y
```

Kiem tra user da duoc tao:

```bash
id devops
```

Ket qua:
```
uid=1000(devops) gid=1000(devops) groups=1000(devops)
```

---

### Buoc 3 - Them User devops vao Nhom sudo

```bash
usermod -aG sudo devops
```

Xac nhan da them thanh cong:

```bash
id devops
```

Ket qua (thay doi so voi buoc truoc - co them nhom sudo):
```
uid=1000(devops) gid=1000(devops) groups=1000(devops),27(sudo)
```

---

### Buoc 4 - Sao Chep Cau Hinh SSH Key Tu root Sang devops

**4.1 - Tao thu muc .ssh cho user devops:**

```bash
mkdir -p /home/devops/.ssh
```

**4.2 - Sao chep file authorized_keys tu root sang devops:**

```bash
cp /root/.ssh/authorized_keys /home/devops/.ssh/authorized_keys
```

---

### Buoc 5 - Phan Quyen Chinh Xac cho Thu Muc .ssh

Day la buoc **bat buoc** - neu phan quyen sai, SSH se tu choi xac thuc.

```bash
# Dat chu so huu la user devops
chown -R devops:devops /home/devops/.ssh

# Thu muc .ssh phai co quyen 700 (chi owner duoc doc/ghi/chay)
chmod 700 /home/devops/.ssh

# File authorized_keys phai co quyen 600 (chi owner duoc doc/ghi)
chmod 600 /home/devops/.ssh/authorized_keys
```

Kiem tra lai phan quyen:

```bash
ls -la /home/devops/.ssh/
```

Ket qua mong doi:
```
total 12
drwx------ 2 devops devops 4096 Oct  2 17:00 .
drwxr-xr-x 4 devops devops 4096 Oct  2 17:00 ..
-rw------- 1 devops devops  571 Oct  2 17:00 authorized_keys
```

---

## Kiem Tra Ket Qua

### Kiem tra 1: Dang nhap SSH bang user devops

Thoat phien root hien tai, sau do dang nhap lai bang user `devops` tu may ca nhan:

```bash
ssh -i ~/.ssh/id_ed25519 devops@<IP_ADDRESS_DROPLET>
```

**Ket qua mong doi:**
```
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

devops@ubuntu-devops-test:~$
```

> Dang nhap thanh cong bang user devops, khong can nhap mat khau (xac thuc qua SSH Key)!

---

### Kiem tra 2: Chay lenh sudo whoami

Sau khi dang nhap bang `devops`, chay:

```bash
sudo whoami
```

He thong yeu cau nhap mat khau cua devops:

```
[sudo] password for devops:
```

Nhap mat khau da thiet lap o Buoc 2. Ket qua:

```
root
```

> Lenh `sudo whoami` tra ve `root` - user devops co day du quyen quan tri thong qua sudo!

---

## Log Terminal Toan Bo

```
# --- DANG NHAP BANG ROOT ---
$ ssh -i ~/.ssh/id_ed25519 root@159.65.130.45
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-91-generic x86_64)
root@ubuntu-devops-test:~#

# --- TAO USER DEVOPS ---
root@ubuntu-devops-test:~# adduser devops
Adding user `devops' ...
New password: ********
Retype new password: ********
passwd: password updated successfully
Is the information correct? [Y/n] Y

# --- THEM VAO NHOM SUDO ---
root@ubuntu-devops-test:~# usermod -aG sudo devops
root@ubuntu-devops-test:~# id devops
uid=1000(devops) gid=1000(devops) groups=1000(devops),27(sudo)

# --- SAO CHEP SSH KEY ---
root@ubuntu-devops-test:~# mkdir -p /home/devops/.ssh
root@ubuntu-devops-test:~# cp /root/.ssh/authorized_keys /home/devops/.ssh/authorized_keys

# --- PHAN QUYEN ---
root@ubuntu-devops-test:~# chown -R devops:devops /home/devops/.ssh
root@ubuntu-devops-test:~# chmod 700 /home/devops/.ssh
root@ubuntu-devops-test:~# chmod 600 /home/devops/.ssh/authorized_keys
root@ubuntu-devops-test:~# ls -la /home/devops/.ssh/
total 12
drwx------ 2 devops devops 4096 Oct  2 17:00 .
drwxr-xr-x 4 devops devops 4096 Oct  2 17:00 ..
-rw------- 1 devops devops  571 Oct  2 17:00 authorized_keys

# --- DANG NHAP BANG DEVOPS (tu may ca nhan) ---
$ ssh -i ~/.ssh/id_ed25519 devops@159.65.130.45
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-91-generic x86_64)
devops@ubuntu-devops-test:~$

# --- KIEM TRA SUDO ---
devops@ubuntu-devops-test:~$ sudo whoami
[sudo] password for devops:
root
```

---

## Giai Thich Phan Quyen SSH

| Duong dan | Quyen | Ly do |
|---|---|---|
| `/home/devops/.ssh/` | `700` (drwx------) | Chi owner duoc truy cap, SSH tu choi neu group/other co quyen |
| `/home/devops/.ssh/authorized_keys` | `600` (-rw-------) | Chi owner duoc doc/ghi, SSH tu choi neu group/other co quyen |
| `chown devops:devops` | Owner = devops | SSH kiem tra owner phai khop voi user dang nhap |

> **Tai sao phan quyen quan trong?** Daemon `sshd` co tinh nang bao mat tu dong: neu thu muc `.ssh` hoac file `authorized_keys` co quyen qua rong (vd: 777, 755), no se tu choi xac thuc va tra loi `Permission denied`.

---

## So Sanh Root vs Non-root User

| Tieu chi | root | devops (sudo) |
|---|---|---|
| **Rui ro** | Cao - moi lenh co hieu luc ngay | Thap - can xac nhan qua sudo |
| **Truy vet** | Kho truy vet hanh dong | Log ro rang `sudo` cua user nao |
| **Bao mat** | 1 lop | 2 lop (SSH Key + sudo password) |
| **Thuc te** | Chi dung khoi tao ban dau | Dung cho cong viec hang ngay |

---

## Cau Truc Thu Muc

```
homework/session_02/ex2/
|-- README.md              <- File bao cao nay
|-- screenshots/           <- Anh chup man hinh (tuy chon)
    |-- create-user.png
    |-- sudo-whoami.png
```

---

## Tai Lieu Tham Khao

- [Ubuntu Docs - User Management](https://ubuntu.com/server/docs/user-management)
- [DigitalOcean - Initial Server Setup Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-22-04)
- [Linux man page - adduser](https://manpages.ubuntu.com/manpages/jammy/man8/adduser.8.html)
- [Linux man page - usermod](https://manpages.ubuntu.com/manpages/jammy/man8/usermod.8.html)
