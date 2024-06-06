# Hướng dẫn cài đặt KVM trên Ubuntu Server 20.04

## Bước 1: Kiểm tra và kích hoạt ảo hóa trong BIOS

- [Intel(VMX)](https://www.asus.com/vn/support/faq/1043786/)
- [Intel(VT-x)](https://www.asus.com/vn/support/faq/1043181/)
- [AMD-V](https://www.asus.com/vn/support/faq/1043992/)

## Bước 2: Cập nhật hệ thống Ubuntu

```sh
sudo apt update
sudo apt upgrade
```

## Bước 3: Kiểm tra kích hoạt ảo hóa

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

## Bước 4: Cài đặt KVM cho Ubuntu Server 20.04

```sh
sudo apt install -y qemu-kvm virt-manager libvirt-daemon-system virtinst libvirt-clients bridge-utils
```

## Bước 5: Kích hoạt và kiểm tra dịch vụ ảo hóa

1. Kích hoạt nền ảo hóa (virtualization daemon):

```sh
sudo systemctl enable --now libvirtd
sudo systemctl start libvirtd
```

2. Kiểm tra dịch vụ:

```sh
   sudo systemctl status libvirtd
```

## Bước 6: Thêm tài khoản vào nhóm KVM và Libvirt

```sh
   sudo usermod -aG kvm $USER
   sudo usermod -aG libvirt $USER
```

## Bước 7: Chạy KVM Virtual Machines Manager (Tùy chọn)

```sh
   virt-manager
```

**Lưu ý:**

- Virt-Manager là giao diện đồ họa giúp tạo và quản lý các máy ảo KVM.
- Ubuntu Server không có giao diện đồ họa nền cần truy cập từ máy có giao diện đồ họa.

Chúc các bạn cài đặt thành công và có trải nghiệm tuyệt vời với KVM trên Ubuntu Server 20.04!
