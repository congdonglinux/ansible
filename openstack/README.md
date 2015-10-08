Hướng dẫn sử dụng Openstack ansible playbook để cài đặt Openstack
=================================================================
---
### Thông tin playbook
---
- Playbook đã được test trên MacOSX (ansible 1.9.3) và CentOS 6.7 (ansible 1.9.2)
- Play book cài đặt các dịch vụ cơ bản:
	* Openstack Identity Service (keystone)
	* Openstack Image Service (glance)
	* Openstack Compute Service (nova)
	* Openstack Block Service (cinder)
	* Openstack Networking Service (neutron)
	* Openstack Dashboard (horizon)
- Sử dụng lvm làm back-end cho cinder-volume
- Sử dụng local hardisk cho nova-compute
- Sử dụng module keystone (Chỉnh sửa từ module của Kevin Carter <kevin.carter@rackspace.com>)

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
