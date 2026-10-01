# Bai Tap 1 - Khoi Tao Droplet Ubuntu tren DigitalOcean voi SSH Key

## Muc Tieu

Thuc hanh dang ky tai khoan, lua chon cau hinh phan cung phu hop va khoi tao thanh cong Droplet chay he dieu hanh **Ubuntu Server** tren ha tang dam may **DigitalOcean**. Cau hinh xac thuc an toan bang cap khoa SSH (SSH Keypair) va thuc hien ket noi thanh cong tu may ca nhan.

---

## Cau Hinh Droplet

| Thong so | Gia tri |
|---|---|
| **He dieu hanh** | Ubuntu 22.04 LTS x64 |
| **Plan** | Basic (Shared CPU) |
| **Loai o cung** | Regular SSD |
| **CPU / RAM** | 1 vCPU / 512MB (hoac 1GB) |
| **Chi phi** | ~$4/thang hoac $6/thang |
| **Datacenter Region** | Singapore (SGP1) |
| **Authentication** | SSH Keys |

---

## Cac Buoc Thuc Hien

### Buoc 1 - Dang Ky Tai Khoan DigitalOcean

1. Truy cap [https://www.digitalocean.com](https://www.digitalocean.com)
2. Nhan **Sign Up** va dien thong tin (email, mat khau)
3. Xac minh email va hoan tat thanh toan (the tin dung hoac PayPal)
4. Dang nhap vao **Control Panel**

---

### Buoc 2 - Tao Cap Khoa SSH Tren May Ca Nhan

Mo **Terminal** (Linux/macOS) hoac **PowerShell/Git Bash** (Windows) va chay lenh:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

> **Luu y:** Thuat toan `ed25519` hien dai va bao mat hon `rsa`.
> Neu muon dung RSA: `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`

**Ket qua sau khi chay lenh:**

```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/user/.ssh/id_ed25519):   <- Nhan Enter de dung mac dinh
Enter passphrase (empty for no passphrase):                          <- Co the de trong hoac nhap passphrase
Enter same passphrase again:
Your identification has been saved in /home/user/.ssh/id_ed25519
Your public key has been saved in /home/user/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx your_email@example.com
```

**Xem noi dung Public Key:**

```bash
# Linux / macOS
cat ~/.ssh/id_ed25519.pub

# Windows PowerShell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

**Vi du output:**
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAbcDefGhiJklMnoPqrStUvWxYz1234567890abcdef your_email@example.com
```

> **Canh bao:** Chi sao chep **Public Key** (`.pub`). Tuyet doi **KHONG** chia se **Private Key** voi bat ky ai.

---

### Buoc 3 - Them Public Key Vao DigitalOcean

1. Dang nhap vao [DigitalOcean Control Panel](https://cloud.digitalocean.com)
2. Vao **Settings** -> **Security** -> **SSH Keys**
3. Nhan **Add SSH Key**
4. Dan toan bo noi dung cua file `id_ed25519.pub` vao o **SSH Key content**
5. Dat ten cho key (vi du: `my-laptop-key`)
6. Nhan **Add SSH Key** de luu

---

### Buoc 4 - Tao Droplet

1. Tu Control Panel, nhan **Create** -> **Droplets**
2. **Choose Region:** Chon `Singapore` -> `SGP1`
3. **Choose an image:** Chon tab **OS** -> **Ubuntu** -> **22.04 (LTS) x64**
4. **Choose Size:**
   - Chon **Basic** (Shared CPU)
   - Chon **Regular SSD**
   - Chon goi **$4/thang** (512MB RAM / 1 vCPU / 10GB SSD) hoac **$6/thang** (1GB RAM / 1 vCPU / 25GB SSD)
5. **Authentication Method:** Chon **SSH Key** -> Tick chon key vua them o Buoc 3
6. **Hostname:** Dat ten (vi du: `ubuntu-devops-test`)
7. Nhan **Create Droplet** va cho khoang 30-60 giay
8. Sau khi tao xong, ghi lai **dia chi IP Public** cua Droplet (vi du: `159.65.x.x`)

---

### Buoc 5 - Ket Noi SSH Toi Droplet

Mo Terminal va chay lenh (thay `<IP_ADDRESS_DROPLET>` bang IP thuc):

```bash
ssh -i ~/.ssh/id_ed25519 root@<IP_ADDRESS_DROPLET>
```

**Vi du:**
```bash
ssh -i ~/.ssh/id_ed25519 root@159.65.130.45
```

Lan dau ket noi se xuat hien canh bao xac thuc host:

```
The authenticity of host '159.65.130.45 (159.65.130.45)' can not be established.
ED25519 key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```

Nhap `yes` -> Nhan Enter.

---

## Ket Qua Mong Doi

Sau khi ket noi thanh cong, Terminal hien thi giao dien dong lenh Ubuntu Server:

```
Warning: Permanently added '159.65.130.45' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

  System information as of Thu Oct  2 17:05:39 UTC 2025

  System load:  0.08              Processes:             86
  Usage of /:   5.1% of 24.06GB  Users logged in:       0
  Memory usage: 18%               IPv4 address for eth0: 159.65.130.45
  Swap usage:   0%

root@ubuntu-devops-test:~#
```

> **Ket noi thanh cong** - Khong can nhap mat khau, xac thuc hoan toan qua SSH Key!

---

## Anh Minh Hoa

### Giao dien Droplet tren DigitalOcean Console

*(Chup man hinh Droplet da tao thanh cong tren Control Panel va dat vao thu muc `screenshots/`)*

![DigitalOcean Droplet Console](screenshots/droplet-console.png)

### Log ket noi SSH thanh cong tu Terminal

*(Chup man hinh Terminal hien thi giao dien dong lenh Ubuntu sau khi SSH)*

![SSH Connection Success](screenshots/ssh-connection.png)

---

## Ly Do Dung SSH Key Thay Vi Mat Khau

| Tieu chi | SSH Key | Mat khau |
|---|---|---|
| **Bao mat** | Rat cao (ma hoa bat doi xung) | De bi brute-force |
| **Tien loi** | Khong can nhap moi lan | Phai nhap moi lan |
| **Tu dong hoa** | Dung duoc trong CI/CD | Khong phu hop |
| **Rui ro lo thong tin** | Thap (private key o local) | Cao neu bi lo |

---

## Cau Truc Thu Muc

```
homework/session_02/ex1/
|-- README.md              <- File huong dan nay
|-- screenshots/           <- Anh chup man hinh
    |-- droplet-console.png
    |-- ssh-connection.png
```

---

## Tai Lieu Tham Khao

- [DigitalOcean Docs - How to Create a Droplet](https://docs.digitalocean.com/products/droplets/how-to/create/)
- [DigitalOcean Docs - SSH Keys](https://docs.digitalocean.com/products/droplets/how-to/add-ssh-keys/)
- [OpenSSH Manual](https://www.openssh.com/manual.html)
- [Ubuntu 22.04 LTS Release Notes](https://releases.ubuntu.com/22.04/)
