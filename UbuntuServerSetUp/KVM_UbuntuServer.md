# Hướng dẫn cài đặt KVM trên Ubuntu Server 20.04

## Mục lục

- [Hướng dẫn cài đặt KVM trên Ubuntu Server 20.04](#hướng-dẫn-cài-đặt-kvm-trên-ubuntu-server-2004)
  - [Mục lục](#mục-lục)
  - [Configuration](#configuration)
  - [Installation steps](#installation-steps)
    - [Steps 1: Kiểm tra và kích hoạt ảo hóa trong BIOS](#steps-1-kiểm-tra-và-kích-hoạt-ảo-hóa-trong-bios)
    - [Steps 2: Cập nhật hệ thống Ubuntu](#steps-2-cập-nhật-hệ-thống-ubuntu)
    - [Steps 3: Kiểm tra kích hoạt ảo hóa](#steps-3-kiểm-tra-kích-hoạt-ảo-hóa)
    - [Steps 4: Cài đặt KVM cho Ubuntu Server 20.04](#steps-4-cài-đặt-kvm-cho-ubuntu-server-2004)
    - [Steps 5: Kích hoạt và kiểm tra dịch vụ ảo hóa](#steps-5-kích-hoạt-và-kiểm-tra-dịch-vụ-ảo-hóa)
    - [Steps 6: Thêm tài khoản vào nhóm KVM và Libvirt](#steps-6-thêm-tài-khoản-vào-nhóm-kvm-và-libvirt)
    - [Steps 7: Chạy KVM Virtual Machines Manager (Tùy chọn)](#steps-7-chạy-kvm-virtual-machines-manager-tùy-chọn)

## Configuration

- Hệ điều hành: Ubuntu Server 20.04 LTS
- Quyền truy cập root
- Bộ xử lý hỗ trợ ảo hóa (Intel VT-x hoặc AMD-V)
- Tài nguyên hệ thống:
  - CPU: Tối thiểu 2 core
  - RAM: Tối thiểu 4GB
  - Ổ cứng: Tối thiểu 20GB

## Installation steps

### Steps 1: Kiểm tra và kích hoạt ảo hóa trong BIOS

- [Intel(VMX)](https://www.asus.com/vn/support/faq/1043786/)
- [Intel(VT-x)](https://www.asus.com/vn/support/faq/1043181/)
- [AMD-V](https://www.asus.com/vn/support/faq/1043992/)

### Steps 2: Cập nhật hệ thống Ubuntu

```sh
sudo apt update
sudo apt upgrade
```

### Steps 3: Kiểm tra kích hoạt ảo hóa

- Kiểm tra tương thích CPU:

```sh
egrep -c '(vmx|svm)' /proc/cpuinfo
```

- Nếu trả về lớn hơn 0 => KVM tương thích hệ thống => Có thể cài
- Kiểm tra KVM Virtualization:

```sh
kvm-ok
```

- Nếu nhận được thông báo "Command 'kvm-ok' not found ...", cần cài đặt gói cpu-checker

```sh
sudo apt install -y cpu-checker
```

```sh
kvm-ok
```

- Thành công sẽ trả về "KVM acceleration can be used"

### Steps 4: Cài đặt KVM cho Ubuntu Server 20.04

```sh
sudo apt install -y qemu-kvm virt-manager libvirt-daemon-system virtinst libvirt-clients bridge-utils
```

### Steps 5: Kích hoạt và kiểm tra dịch vụ ảo hóa

`1. Kích hoạt nền ảo hóa (virtualization daemon):`

```sh
sudo systemctl enable --now libvirtd
sudo systemctl start libvirtd
```

`2. Kiểm tra dịch vụ:`

```sh
   sudo systemctl status libvirtd
```

### Steps 6: Thêm tài khoản vào nhóm KVM và Libvirt

```sh
   sudo usermod -aG kvm $USER
   sudo usermod -aG libvirt $USER
```

### Steps 7: Chạy KVM Virtual Machines Manager (Tùy chọn)

```sh
   virt-manager
```

**Lưu ý:**

- Virt-Manager là giao diện đồ họa giúp tạo và quản lý các máy ảo KVM.
- Ubuntu Server không có giao diện đồ họa nền cần truy cập từ máy có giao diện đồ họa.

Chúc các bạn cài đặt thành công và có trải nghiệm tuyệt vời với KVM trên Ubuntu Server 20.04!
