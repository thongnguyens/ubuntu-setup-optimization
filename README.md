# Ubuntu Setup & Optimization

> Bộ lệnh cài đặt, cấu hình và tối ưu hóa Ubuntu cho môi trường làm việc & học tập.  
> **Cảnh báo:** Hãy đọc kỹ mô tả trước khi chạy từng lệnh; nhiều thao tác ảnh hưởng hệ thống.

<p align="left">
  <img alt="Ubuntu" src="https://img.shields.io/badge/Ubuntu-24.04%7C22.04-E95420?logo=ubuntu&logoColor=white">
  <img alt="Shell"  src="https://img.shields.io/badge/Shell-Bash-121011?logo=gnu-bash">
  <img alt="License" src="https://img.shields.io/badge/License-MIT-green">
  <img alt="PRs" src="https://img.shields.io/badge/PRs-welcome-brightgreen">
</p>

---

## Mục lục
- [Trước khi bắt đầu](#trước-khi-bắt-đầu)
- [1. Xóa lịch sử lệnh](#1-xóa-lịch-sử-lệnh)
- [2. Gỡ cài đặt Snapd](#2-gỡ-cài-đặt-snapd)
- [3. Cài đặt Times New Roman](#3-cài-đặt-phông-chữ-times-new-roman)
- [4. Ubuntu Restricted Extras](#4-ubuntu-restricted-extras)
- [5. Sửa lệch giờ Windows ↔ Ubuntu](#5-sửa-lệch-giờ-windows--ubuntu)
- [6. Đăng nhập vân tay (Fingerprint)](#6-đăng-nhập-vân-tay-fingerprint)
- [7. ClamAV/ClamTK (diệt virus)](#7-clamavclamtk-diệt-virus)
- [8. Gnome Boxes (ảo hóa)](#8-gnome-boxes-ảo-hóa)
- [9. Brave Browser](#9-brave-browser)
- [10. Bộ gõ Tiếng Việt (IBus Bamboo)](#10-bộ-gõ-tiếng-việt-ibus-bamboo)
- [11. Quản lý VM bằng virsh](#11-quản-lý-vm-bằng-virsh)
- [12. Flathub](#12-flathub)
- [13. Firefox qua Flathub](#13-firefox-qua-flathub)
- [14. Firefox bản chính thức (APT Mozilla)](#14-firefox-bản-chính-thức-apt-mozilla)
- [15. TimeShift (snapshot hệ thống)](#15-timeshift-snapshot-hệ-thống)
- [16. Git](#16-git)
- [17. OpenJDK & Java IDEs](#17-openjdk--java-ides)
- [18. GParted](#18-gparted)
- [Giấy phép](#giấy-phép)
- [Miễn trừ trách nhiệm](#miễn-trừ-trách-nhiệm)

---

## Trước khi bắt đầu

> [!IMPORTANT]
> - Chạy các lệnh dưới đây trên **Ubuntu 22.04/24.04** (hoặc tương đương).
> - Một số mục **xung đột lẫn nhau** (ví dụ: *gỡ snapd* nhưng phần 17 cài IDE bằng Snap). Nếu đã gỡ Snapd, **bỏ qua** phần cài IDE qua Snap hoặc dùng bản Flatpak/DEB thay thế.

---

## 1. Xóa lịch sử lệnh
> [!WARNING]
> Xóa **toàn bộ lịch sử bash** của người dùng hiện tại và thoát phiên làm việc.

```bash
cat /dev/null > ~/.bash_history && history -c && exit
```

---

## 2. Gỡ cài đặt Snapd
> [!CAUTION]
> Thao tác này **loại bỏ Snap hoàn toàn** và “hold” gói snapd. Một số ứng dụng (chẳng hạn một số IDE trong mục 17) sẽ **không cài qua Snap được**.

```bash
sudo apt autoremove --purge snapd && sudo apt-mark hold snapd && sudo apt install gnome-software --no-install-recommends && sudo rm -rf /var/cache/snapd/ && rm -rf ~/snap && sudo rm -rf /snap && sudo rm -rf /var/snap && sudo rm -rf /var/lib/snapd && sudo apt autoremove --purge snapd gnome-software-plugin-snap && sudo apt-mark hold snapd
```

---

## 3. Cài đặt phông chữ Times New Roman
```bash
sudo apt-get install ttf-mscorefonts-installer
```

---

## 4. Ubuntu Restricted Extras
```bash
sudo add-apt-repository multiverse
sudo apt update
sudo apt install ubuntu-restricted-extras
```

---

## 5. Sửa lệch giờ Windows ↔ Ubuntu
```bash
timedatectl set-local-rtc 1 --adjust-system-clock && timedatectl
```

---

## 6. Đăng nhập vân tay (Fingerprint)
```bash
sudo apt install fprintd libpam-fprintd
fprintd-enroll
sudo pam-auth-update
sudo systemctl restart fprintd.service
reboot
```

---

## 7. ClamAV/ClamTK (diệt virus)

**Cài đặt:**
```bash
sudo apt install -y clamav clamav-daemon
sudo apt install -y clamtk clamtk-gnome
```

**Cập nhật database:**
```bash
sudo systemctl stop clamav-freshclam
sudo freshclam
sudo systemctl enable --now clamav-freshclam
```

**Quét thư mục/hệ thống:**
```bash
clamscan -r "/home/thongnguyen/Ubuntu Sharing"
clamscan -r --remove "/home/thongnguyen/Ubuntu Sharing"
sudo clamscan -r --bell -i /
```

---

## 8. Gnome Boxes (ảo hóa)
```bash
sudo apt install -y gnome-boxes qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
sudo apt update
sudo systemctl enable --now libvirtd
gnome-boxes --checks
sudo usermod -aG kvm $USER
sudo usermod -aG libvirt $USER
```

> [!NOTE]
> Sau khi thêm vào nhóm `kvm`/`libvirt`, **đăng xuất & đăng nhập lại** (hoặc reboot) để có hiệu lực.

---

## 9. Brave Browser
```bash
sudo apt install -y curl
sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main" | sudo tee /etc/apt/sources.list.d/brave-browser-release.list
sudo apt update
sudo apt install -y brave-browser
```

---

## 10. Bộ gõ Tiếng Việt (IBus Bamboo)
```bash
sudo add-apt-repository ppa:bamboo-engine/ibus-bamboo
sudo apt-get update
sudo apt-get install -y ibus ibus-bamboo --install-recommends
ibus restart
env DCONF_PROFILE=ibus dconf write /desktop/ibus/general/preload-engines "['BambooUs', 'Bamboo']" && gsettings set org.gnome.desktop.input-sources sources "[('xkb', 'us'), ('ibus', 'Bamboo')]"
```

---

## 11. Quản lý VM bằng `virsh`
```bash
virsh -c qemu:///session list --all
virsh -c qemu:///session edit win10-enterp
```

---

## 12. Flathub
```bash
sudo apt install -y flatpak
sudo apt install -y gnome-software-plugin-flatpak
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

---

## 13. Firefox qua Flathub
```bash
flatpak install flathub org.mozilla.firefox
```

---

## 14. Firefox bản chính thức (APT Mozilla)
```bash
sudo install -d -m 0755 /etc/apt/keyrings
sudo apt-get install -y wget
wget -q https://packages.mozilla.org/apt/repo-signing-key.gpg -O- | sudo tee /etc/apt/keyrings/packages.mozilla.org.asc > /dev/null
gpg -n -q --import --import-options import-show /etc/apt/keyrings/packages.mozilla.org.asc | awk '/pub/{getline; gsub(/^ +| +$/,""); if($0 == "35BAA0B33E9EB396F59CA838C0BA5CE6DC6315A3") print "\nThe key fingerprint matches ("$0").\n"; else print "\nVerification failed: the fingerprint ("$0") does not match the expected one.\n"}'
echo "deb [signed-by=/etc/apt/keyrings/packages.mozilla.org.asc] https://packages.mozilla.org/apt mozilla main" | sudo tee -a /etc/apt/sources.list.d/mozilla.list > /dev/null
cat <<'EOF' | sudo tee /etc/apt/preferences.d/mozilla >/dev/null
Package: *
Pin: origin packages.mozilla.org
Pin-Priority: 1000
EOF
sudo apt-get update && sudo apt-get install -y firefox
```

> **Fingerprint mong đợi:** `35BAA0B33E9EB396F59CA838C0BA5CE6DC6315A3`.

---

## 15. TimeShift (snapshot hệ thống)
```bash
sudo apt-get update
sudo apt-get install -y timeshift
```

---

## 16. Git
```bash
sudo add-apt-repository ppa:git-core/ppa
sudo apt update && sudo apt install -y git
```

---

## 17. OpenJDK & Java IDEs

**OpenJDK:**
```bash
sudo apt install -y default-jdk
```

> [!IMPORTANT]
> Nếu bạn đã **gỡ Snapd ở mục 2**, **bỏ qua** các lệnh cài IDE bằng Snap dưới đây hoặc dùng bản **Flatpak/DEB** thay thế.

**Java IDEs (Snap):**
```bash
sudo snap install eclipse --classic
sudo snap install intellij-idea-community --classic
sudo snap install code --classic
sudo snap install android-studio --classic
```

---

## 18. GParted
```bash
sudo apt install -y gparted
```

---

## Giấy phép
MIT (tùy chọn theo nhu cầu của bạn).

---

## Miễn trừ trách nhiệm
- Các lệnh có thể thay đổi hành vi hệ thống. Hãy sao lưu dữ liệu quan trọng trước khi thực hiện.
- Một số lệnh yêu cầu quyền `sudo` và có thể xung đột với chính sách IT của tổ chức.
