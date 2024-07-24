# Keycloak setup document

# Cấu hình máy chủ

================

Phần mềm được cài đặt trên máy chủ có:

- Hệ điều hành: Ubuntu 20.04 server

- CPU: 2 core (Khuyến nghị 4 core)

- RAM: 8GB  (Khuyến nghị 8GB)

- Bộ nhớ: 50GB (Khuyến nghị 100GB)

- Cho phép truy cập SSH từ xa.

# Bắt đầu

================

## Cài đặt KeyCloak SSO trên Ubuntu 20.04

## Bước 1: Trước khi bắt đầu, bạn nên cập nhật các gói hệ thống lên phiên bản cập nhật. Chạy lệnh sau để cập nhật tất cả các gói lên phiên bản mới nhất:
```sh
apt-get update -y

apt-get upgrade -y
```
Keycloak là một ứng dụng dựa trên Java. Vì vậy, Java JDK phải được cài đặt trên máy chủ của bạn. Nếu chưa được cài đặt, bạn có thể cài đặt nó bằng lệnh sau:
```sh
apt-get install default-jdk -y
```
Khi Java được cài đặt, bạn có thể xác minh phiên bản Java bằng lệnh sau:
```sh
java --version
```
Bạn sẽ thấy phiên bản Java trong kết quả sau:

Tiếp theo, bạn sẽ cần tải về phiên bản mới nhất của Keycloak từ trang web GitHub. Bạn có thể sử dụng lệnh wget để tải nó xuống hệ thống của mình.
```sh
wget https://github.com/keycloak/keycloak/releases/download/15.0.2/keycloak-15.0.2.tar.gz
```

Kết quả đầu ra:

Sau khi quá trình tải xuống hoàn tất, hãy giải nén tệp đã tải xuống bằng lệnh sau:
```sh
tar -xvzf keycloak-15.0.2.tar.gz
```
Tiếp theo, di chuyển thư mục đã giải nén vào /opt bằng lệnh sau:
```sh
mv keycloak-15.0.2 /opt/keycloak
```
Tiếp theo, tạo một user và group chuyên dụng cho Keycloak bằng lệnh sau:
```sh
groupadd keycloak
useradd -r -g keycloak -d /opt/keycloak -s /sbin/nologin keycloak
```
Tiếp theo, thiết lập quyền sở hữu của thư mục /opt/keycloak thành keycloak:
```sh
chown -R keycloak: /opt/keycloak
chmod o+x /opt/keycloak/bin/
```
## Bước 2 : Cấu hình Keycloak

Tiếp theo, các bạn sẽ cần tạo một thư mục cấu hình Keycloak và copy file cấu hình mẫu. Bạn có thể tạo nó bên trong thư mục /etc:
```sh
mkdir /etc/keycloak
```
Tiếp theo, sao chép các tệp cấu hình mẫu từ thư mục /opt/keycloak bằng lệnh sau:
```sh
cp /opt/keycloak/docs/contrib/scripts/systemd/wildfly.conf /etc/keycloak/keycloak.conf
cp /opt/keycloak/docs/contrib/scripts/systemd/launch.sh /opt/keycloak/bin/
```
Tiếp theo, đặt quyền sở hữu phù hợp bằng lệnh sau:
```sh
chown keycloak: /opt/keycloak/bin/launch.sh
```
Tiếp theo, chỉnh sửa tệp launch.sh và xác định đường dẫn Keycloak của bạn:
```sh
nano /opt/keycloak/bin/launch.sh
```
Thay đổi tập tin như hình dưới đây:

Lưu và đóng tập tin khi bạn hoàn tất.

Tiếp theo, bạn sẽ cần tạo tệp dịch vụ systemd để quản lý dịch vụ Keycloak. Bạn có thể sao chép dịch vụ systemd mẫu bằng lệnh sau:
```sh
cp /opt/keycloak/docs/contrib/scripts/systemd/wildfly.service /etc/systemd/system/keycloak.service
```
Tiếp theo, chỉnh sửa tệp keycloak.service và xác định đường dẫn cài đặt Keycloak:
```sh
nano /etc/systemd/system/keycloak.service
```
Thay đổi tập tin như hình dưới đây:

Lưu và đóng tệp sau đó tải lại daemon systemd để áp dụng các thay đổi:
```sh
systemctl daemon-reload
```
Tiếp theo, khởi động dịch vụ Keycloak và kích hoạt nó khi khởi động lại hệ thống:
```sh
systemctl start keycloak

systemctl enable keycloak
```
Bạn có thể kiểm tra trạng thái của dịch vụ Keycloak bằng lệnh sau:
```sh
systemctl status keycloak
```
Kết quả đầu ra:

Tại thời điểm này, máy chủ Keycloak được khởi động và lắng nghe trên cổng 8080. Bạn có thể kiểm tra nó bằng lệnh sau:
```sh
ss -antpl | grep 8080
```
Bạn sẽ nhận được kết quả đầu ra sau:

Bạn cũng có thể kiểm tra nhật ký máy chủ Keycloak bằng lệnh sau:
```sh
tail -f /opt/keycloak/standalone/log/server.log
```
Tạo người dùng quản trị cho Keycloak

Tiếp theo, bạn sẽ cần tạo người dùng quản trị viên để truy cập vào giao diện web Keycloak. Chạy lệnh sau để tạo người dùng quản trị:

Đặt mật khẩu của bạn là "admin" như dưới đây:

Tiếp theo, khởi động lại dịch vụ Keycloak để áp dụng các thay đổi:
```sh
systemctl restart keycloak
```
Tiếp theo, bạn sẽ cần tắt HTTPS cho Keycloak. Bạn có thể vô hiệu hóa nó bằng lệnh sau:
```sh
/opt/keycloak/bin/kcadm.sh config credentials --server http://localhost:8080/auth --realm master --user admin
```
Bạn nhâp mật khẩu "admin" như dưới đây:

Tiếp theo, chạy lệnh sau để tắt HTTPS:
```sh
/opt/keycloak/bin/kcadm.sh update realms/master -s sslRequired=NONE
```
Truy cập giao diện web Keycloak

Bây giờ, hãy mở trình duyệt web của bạn và truy cập vào giao diện web Keycloak bằng URL:"http://192.168.88.121:8080". Bạn sẽ thấy trang đăng nhập Keycloak:

Cung cấp tên người dùng, mật khẩu quản trị viên của bạn và nhấp vào nút Đăng nhập. Bạn sẽ thấy bảng điều khiển Keycloak trên màn hình sau:

Tiếp theo, bạn sẽ cần tạo một realm để quản lý các ứng dụng của mình. Di chuột qua Master trong bảng điều khiển bên trái và nhấp vào  Add Realm. Một realm quản lý một tập hợp người dùng, thông tin xác thực, vai trò và nhóm. Một người dùng thuộc về và đăng nhập vào một realm.

Bạn sẽ thấy màn hình sau:

Sau khi điền "Realm name" và nhấn Create. Bạn sẽ thấy được hình sau:

Tiếp theo, bạn sẽ cần tạo người dùng mới cho Keycloak. Ở phần Manage -> Nhấp vào Users. Bạn sẽ thấy màn hình sau:

Tiếp theo, nhấp vào nút "Add user" dùng. Bạn sẽ thấy màn hình sau:

Cung cấp thông tin người dùng của bạn và nhấp vào nút "Save". Tiếp theo, nhấp vào tab "Credentials" và đặt mật khẩu cho người dùng như hiển thị bên dưới:

Trong hướng dẫn ở trên, chúng tôi đã giải thích cách cài đặt máy chủ Keycloak trên Ubuntu 20.04. Chúng tôi cũng đã giải thích cách thêm realm và user vào máy chủ Keycloak. Bây giờ bạn có thể quản lý mật khẩu ứng dụng của mình bằng máy chủ Keycloak.