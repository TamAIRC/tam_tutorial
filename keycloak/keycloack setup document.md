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
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdu8_zCmDylqg68jp5_KoTDvanJDAacvc99BV790hyEJ83Q6wHyC1Um8uHBZ-JWIBetP-nATq8y3pWTKxHqbvAKTSkR7xibr6iH1n0LUkyD4IZePJ8CXqwPcClg6-FDY5atSTXk6VrMSsC6551dQCrLZOA?key=MBbyo_v3b_dXzQprbNVVFQ)
Tiếp theo, bạn sẽ cần tải về phiên bản mới nhất của Keycloak từ trang web GitHub. Bạn có thể sử dụng lệnh wget để tải nó xuống hệ thống của mình.
```sh
wget https://github.com/keycloak/keycloak/releases/download/15.0.2/keycloak-15.0.2.tar.gz
```

Kết quả đầu ra:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXecHvWNC9pCCf0mDs7w1A8eb6vmT8FTkyT-Wq3U8qG8kysmjTHj14-uQOORAIF5-zKZQsSNUBxNAtaPf0xTIr85ehRE3g95tuk-nS6Gq4U6RYJltLRTonfh3XnzD6VCtt16PEQVte74Pt_hGNiC68gsmWw?key=MBbyo_v3b_dXzQprbNVVFQ)
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
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfF2gFbAwRwNoDIrxsDB21H8TeRPOgFQmlSU3X1fwUnoeBNHFirIkcMiglk52U3IlxMcMdamRnjB1PJCX8IsmfowIteAYmQnwzcHdTL7BtbxOs-Ze9rKwJprCC1kDMoqLT2gtEcTSqT9c2919ubRC2qC-vk?key=MBbyo_v3b_dXzQprbNVVFQ)
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
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf6t-UH_nmhQm9bRboBD4RH6HGBzgC7ameiCiunFBVOpy463_CO9UliMposbe2brgvLLLo1_tOfsufBBa0mCh7DJhVQ9-8y30RTdKWRqOn1BfjzKb56JAl3vyiGcm-klAM_FYCiK25OTV2czyhJfzQreoC6?key=MBbyo_v3b_dXzQprbNVVFQ)
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
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcQP9Fm7CVdu5OeLQRHJFh3vzdqwvHm8SIVtdFsWheA0oyJPajXhbgvE1merGNE_DMh5KGcjMUxPLvXpa-I2FYSyrTwfUq_AAVUlgjxiVLhDQwX6LTuTB2qIhcdVHHOiSiALPPX2FSB2Taf2Y9ZBPfZtYgn?key=MBbyo_v3b_dXzQprbNVVFQ)
Tại thời điểm này, máy chủ Keycloak được khởi động và lắng nghe trên cổng 8080. Bạn có thể kiểm tra nó bằng lệnh sau:
```sh
ss -antpl | grep 8080
```
Bạn sẽ nhận được kết quả đầu ra sau:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcsO7FjpHFmEN3g_Hylsr028AMeC8mE24BHisuGZ8drvaE-peHmVWzG1RYxvz6JSxZJZYQhkodJ8zdcZp4kXcwn7MULdvipQotDs-Pi0b1mYTDW7umU5iMW03aSs65N__vUUG_X6PGqySF9c5F-U3eB8600?key=MBbyo_v3b_dXzQprbNVVFQ)
Bạn cũng có thể kiểm tra nhật ký máy chủ Keycloak bằng lệnh sau:
```sh
tail -f /opt/keycloak/standalone/log/server.log
```
Tạo người dùng quản trị cho Keycloak

Tiếp theo, bạn sẽ cần tạo người dùng quản trị viên để truy cập vào giao diện web Keycloak. Chạy lệnh sau để tạo người dùng quản trị:

Đặt mật khẩu của bạn là "admin" như dưới đây:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcpQZuAulTIcIbjxY0ZRp5mWykhH66CGdJ2HoiyL06rqyaSUQi7PVVZurnlSFDCigsqcz71xXCbsZlwYmAHSY-1Y8dwIyHbGCOWi9CI2xJWr2r0EzciGi5qwnp40hrY-gy9_qkXWNxjJEUXJcAHdi0t2ONo?key=MBbyo_v3b_dXzQprbNVVFQ)
Tiếp theo, khởi động lại dịch vụ Keycloak để áp dụng các thay đổi:
```sh
systemctl restart keycloak
```
Tiếp theo, bạn sẽ cần tắt HTTPS cho Keycloak. Bạn có thể vô hiệu hóa nó bằng lệnh sau:
```sh
/opt/keycloak/bin/kcadm.sh config credentials --server http://localhost:8080/auth --realm master --user admin
```
Bạn nhâp mật khẩu "admin" như dưới đây:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdcg5muvpYrEx1nuV9QJbaU6E-Djv9DV2k6wY8sC20AVCzcXA7vIfUQKk4juOZd6mqncTdLX59la2b3o0yL2CWe5QIjTpCoDVQrHJLaxZzf8kSNL5tu6BFnX5kBC7pUoQ61zeuzuvKHKEzcCKLJuhPtZbed?key=MBbyo_v3b_dXzQprbNVVFQ)
Tiếp theo, chạy lệnh sau để tắt HTTPS:
```sh
/opt/keycloak/bin/kcadm.sh update realms/master -s sslRequired=NONE
```
Truy cập giao diện web Keycloak

Bây giờ, hãy mở trình duyệt web của bạn và truy cập vào giao diện web Keycloak bằng URL:"http://192.168.88.121:8080". Bạn sẽ thấy trang đăng nhập Keycloak:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcJk4z7NUNytP5nRhXUuYnkv5duHRlmD6fPVZZ_vIafvmI53zfApZhjr4HWFGFBSMAQdd9pk-QoB5ewGi-zhzMBEAWoIG6LuUMYiKjt9i5hP2x4d9iaMgzWkzZlRexvLKYI7Jth3oxc0X4yl8ccWcP7KJA?key=MBbyo_v3b_dXzQprbNVVFQ)
Cung cấp tên người dùng, mật khẩu quản trị viên của bạn và nhấp vào nút Đăng nhập. Bạn sẽ thấy bảng điều khiển Keycloak trên màn hình sau:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcuSvgtot1VmsgGdLKCi4yXqluphc41ypZ14Vb3qaH-E8SFywibbxCSsvR6CMpKGkbbKYk3oMdOvz2hkSinTQUEPl8texk9-YVcUjVOYD3T1Edoj5NQTxzpCLaMfeX3hhKt5DmxTCsZQaYlFuU2zg7uzaeE?key=MBbyo_v3b_dXzQprbNVVFQ)
Tiếp theo, bạn sẽ cần tạo một realm để quản lý các ứng dụng của mình. Di chuột qua Master trong bảng điều khiển bên trái và nhấp vào  Add Realm. Một realm quản lý một tập hợp người dùng, thông tin xác thực, vai trò và nhóm. Một người dùng thuộc về và đăng nhập vào một realm.

Bạn sẽ thấy màn hình sau:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXe8ymzKAI1W-RSyvCRt4fJHpsxVmSnrnK2tE4H_q2jY50XoivHqSbxtVm21YqxcNuzV0UJnllcg9KXPEDdXeWv9b0qbzPrrQMRbSrbUvhZ16B1qoeFMMKex0ItMA-b9A32uo9X168Lt3JxBqJyqobWvowhj?key=MBbyo_v3b_dXzQprbNVVFQ)
Sau khi điền "Realm name" và nhấn Create. Bạn sẽ thấy được hình sau:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXe-0aZzWl_DS5X6W6ghioEXHf7xpBPCHS_WldWYHBQe069iyUkm74-JJe8uX-40b1w83trG1hboXMVgk-JmV6xmEqRympBIQotYdfyF0_W6cv5c9KJAzfPjyTUDtizft0kJPILMxO_wwCE6wNCllXLMNsPL?key=MBbyo_v3b_dXzQprbNVVFQ)
Tiếp theo, bạn sẽ cần tạo người dùng mới cho Keycloak. Ở phần Manage -> Nhấp vào Users. Bạn sẽ thấy màn hình sau:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXczlwLP8KapMzr_o51geX74iJZHu-wbKHtFLLhkSPl6qC6zTN7KC6Su5AT1ogfakfH23BPTOZBPCdWrcjhJBwZgJUl2OZaf5A-QFAuGUTvs-uom1bk9nzI4EjvTwS0b2wN_GZYYB9TaIqc7Jde1OKuG7fw?key=MBbyo_v3b_dXzQprbNVVFQ)
Tiếp theo, nhấp vào nút "Add user" dùng. Bạn sẽ thấy màn hình sau:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeGZ1c1a4mqadqvujO6XSmncy5YtntaIOY9CAtZI8-FloYn2Yw1zIxOUqul1vspbhNLDXvMM1LKBwSGQYl4PcZaSAE9gEnsikYFbZUbIJFEaqlP9_nhPcYsedRPbFJ_Wm3n246Z171Vq_UjJpZbrb1d18k7?key=MBbyo_v3b_dXzQprbNVVFQ)
Cung cấp thông tin người dùng của bạn và nhấp vào nút "Save". Tiếp theo, nhấp vào tab "Credentials" và đặt mật khẩu cho người dùng như hiển thị bên dưới:
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdhm6wTfBhNSnQl3kcMBT1XaZTJ3EqSBa1LND6boa6c15wGr0n8TkLWTROD9k0rPb-HHUUnQ-6VgdXPddbK-R9gTbc_YzpmfA_DbzkjV8PqCi0x0109RhjVHnGTk3GbaY5PKXs1V8SfVRnBv-t9DJjqLKY?key=MBbyo_v3b_dXzQprbNVVFQ)
Trong hướng dẫn ở trên, chúng tôi đã giải thích cách cài đặt máy chủ Keycloak trên Ubuntu 20.04. Chúng tôi cũng đã giải thích cách thêm realm và user vào máy chủ Keycloak. Bây giờ bạn có thể quản lý mật khẩu ứng dụng của mình bằng máy chủ Keycloak.