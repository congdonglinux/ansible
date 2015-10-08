Hướng dẫn sử dụng Openstack ansible playbook để cài đặt Openstack
=================================================================
---
### Thay đổi cấu hình cluster
---
- Edit file `user_config.yml` để cập nhật 1 số thông tin như
	* Mật khẩu các service.
	* Số lượng, tên các project sẽ được tạo trong Openstack
	* Các tham số liên quan đến các service (listen IP, listen Port, public/internal/admin endpoints . . .)

- Edit file `inventory.ini` để câp nhật thông tin các server trong cluster

### Trình tự cài đặt
---
- Để cài đặt cơ bản (cấu hình hostname, thêm record vào `/etc/hosts`, upgrade system):

	```ansible-playbook -i inventory.ini -e @user_secret.yml -e @user_config.yml basic-hosts-setup.yml -k```
- Để cài đặt các dịch vụ hỗ trợ Openstack (mysql-server, ntp, message queue):

	```ansible-playbook -i inventory.ini -e @user_secret.yml -e @user_config.yml setup-infrastructure.yml -k```
- Để cài đặt từ đầu đến cuối chạy lệnh:

	```ansible-playbook -i inventory.ini -e @user_secret.yml -e @user_config.yml setup-everything.yml -k```
