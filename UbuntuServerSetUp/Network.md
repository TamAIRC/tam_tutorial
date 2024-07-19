# Hướng dẫn cấu hình mạng Bridge và tạo máy ảo Ubuntu Server 20.04 trong KVM

Hướng dẫn cấu hình mạng Bridge và tạo máy ảo Ubuntu Server 20.04 trong KVM. Hãy làm theo các bước dưới đây để thiết lập và quản lý máy ảo một cách dễ dàng.

## Mục lục

- [Hướng dẫn cấu hình mạng Bridge và tạo máy ảo Ubuntu Server 20.04 trong KVM](#hướng-dẫn-cấu-hình-mạng-bridge-và-tạo-máy-ảo-ubuntu-server-2004-trong-kvm)
  - [Mục lục](#mục-lục)
  - [Configuration](#configuration)
  - [Installation steps](#installation-steps)
  - [Bước 1: Lấy tên card mạng vật lý](#bước-1-lấy-tên-card-mạng-vật-lý)
  - [Bước 2: Cấu hình mạng Bridge](#bước-2-cấu-hình-mạng-bridge)
  - [Bước 3: Lưu và áp dụng cấu hình](#bước-3-lưu-và-áp-dụng-cấu-hình)
  - [Bước 4: Tải xuống tệp ISO Ubuntu Server](#bước-4-tải-xuống-tệp-iso-ubuntu-server)
  - [Bước 5: Chạy KVM Virtual Machines Manager](#bước-5-chạy-kvm-virtual-machines-manager)
  - [Bước 6: Tạo máy ảo Ubuntu Server 20.04 trong KVM](#bước-6-tạo-máy-ảo-ubuntu-server-2004-trong-kvm)
  - [Bước 7: Kiểm tra kết nối internet](#bước-7-kiểm-tra-kết-nối-internet)

## Configuration

## Installation steps

## Bước 1: Lấy tên card mạng vật lý

Chạy lệnh sau:

```sh
ip a
```

- Lệnh này sẽ hiển thị danh sách các card mạng hiện có trên máy.
- Ghi lại tên của card mạng vật lý mà bạn muốn sử dụng (ví dụ: `enp1s0`).

## Bước 2: Cấu hình mạng Bridge

- Mở file cấu hình netplan:

```sh
sudo nano /etc/netplan/00-installer-config.yaml
```

- Cập nhật cấu hình mạng sau vào file:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp1s0f0:
      dhcp4: no
      dhcp6: no
      mtu: 9000
  bridges:
    br0:
      interfaces: [enp1s0]
      dhcp4: no
      dhcp6: no
      addresses: [192.168.88.14/24]
      gateway4: 192.168.88.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
    bridge20:
      interfaces: [br0.20]
      dhcp4: no
      dhcp6: no
      addresses: [192.168.20.14/24]
    bridge88:
      interfaces: [br0.88]
      dhcp4: no
      dhcp6: no
      addresses: [192.168.88.14/24]
  vlans:
    br0.20:
      link: br0
      id: 20
    br0.88:
      link: br0
      id: 88
```

- Giải thích:

  - Chúng ta đang cấu hình một bridge (`br0`) sử dụng card mạng vật lý (`enp1s0`).
  - Cấu hình thêm các VLAN (`br0.20` và `br0.88`) và các bridges tương ứng (`bridge20` và `bridge88`).
  - Điều này giúp máy ảo kết nối được với mạng LAN và VLAN tương ứng.

## Bước 3: Lưu và áp dụng cấu hình

- Lưu file và thoát khỏi nano. Kích hoạt cấu hình:

```sh
sudo netplan apply
```

## Bước 4: Tải xuống tệp ISO Ubuntu Server

Chúng ta sẽ sử dụng `wget` hoặc `curl` để tải xuống tệp ISO của Ubuntu Server 20.04.

Sử dụng `wget` để tải xuống tệp ISO:

```sh
wget https://releases.ubuntu.com/20.04.6/ubuntu-20.04.6-live-server-amd64.iso
```

Sử dụng `curl` để tải xuống tệp ISO (tùy chọn):

```sh
curl -O https://releases.ubuntu.com/20.04.6/ubuntu-20.04.6-live-server-amd64.iso
```

Cài đặt `wget` hoặc `curl` nếu chưa có:

- Cài đặt `wget`:

```bash
sudo apt update
sudo apt install wget
```

- Cài đặt `curl`:

```bash
sudo apt update
sudo apt install curl
```

## Bước 5: Chạy KVM Virtual Machines Manager

```sh
  virt-manager
```

## Bước 6: Tạo máy ảo Ubuntu Server 20.04 trong KVM

- [Video hướng dẫn cài đặt KVM](https://www.youtube.com/watch?v=Si0J0P5Dey4)
- [Video hướng dẫn tạo máy ảo Ubuntu Server 20.04](https://www.youtube.com/watch?v=MHntZBOnnQU)

Trong quá trình tạo máy ảo, bạn sẽ được hướng dẫn cách chọn tệp ISO, cấu hình tài nguyên máy ảo và thiết lập mạng Bridge đã tạo ở bước trước.

## Bước 7: Kiểm tra kết nối internet

Sau khi tạo xong máy ảo, khởi động máy ảo và kiểm tra kết nối internet:

```sh
ping google.com
```

Nếu kết nối thành công, bạn có thể ping để kiểm tra kết nối nội bộ:

Ex:

```sh
ping 192.168.20.121
```

Chúc các bạn cài đặt thành công và có trải nghiệm tuyệt vời với KVM trên Ubuntu Server 20.04!
