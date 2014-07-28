![ansibl](http://www.jaimegago.com/wp-content/uploads/2013/03/Screen-Shot-2013-03-30-at-6.07.36-PM.png)

##What's 'Ansible' ?

Ansible là chương trình qủan lý cấu hình tương tự như Saltstack, puppet... nhưng thay vì sử  dụng agent để trao đổi dữ liệu thì Ansible trao đổi dữ liệu qua SSH và không yêu cầu cài đặt agent lên các máy cần quản lý. 

##Why's Ansible ?
Có 1 số lý do để sử dụng ansible thay cho puppet, saltstack (theo ý kiến chủ quan):
- Không muốn cài agent lên các server cần quản lý
- Không cần quá nhiều module

##How's Ansible?
###Cài đặt Ansible lên máy quản lý (Control Node)

#####Red Hat/CentOS/Fedora
```
rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum install ansible -y
```

#####Ubuntu/Debian
```
apt-get install python-software-properties
apt-add-repository ppa:ansible/ansible
apt-get update
apt-get install ansible
```

#####Cấu hình ansible

Để sử dụng được ansible thì chỉ cần cấu hình 1 file duy nhất, file này chứa danh sách các host sẽ được Ansible quản lý `/etc/ansible/hosts`

#####Mô hình Lab mình sử dụng 2 máy với thông tin như sau:

**OS :**| Ubuntu 14.04
---|-----
**eth0:**| 192.168.1.73
**SSH Port:**| 2246
**SSH Pass:**| Ubuntu121

**OS:**| CentOS 6.5
---|-----
**eth0:**| 192.168.5.149
**SSH Port:**| 5918
**SSH Pass:**| Centos313

Nội dung file `/etc/ansible/hosts`
```
[ansibleLab]
192.168.1.73 ansible_ssh_port=2246 ansible_ssh_pass=Ubuntu121
192.168.1.74 ansible_ssh_port=5918 ansible_ssh_pass=Centos313
```

#####Kiểm tra sau khi cài đặt
```
ansible ansibleLab -m ping
```
```
192.168.1.73 | success >> {
    "changed": false, 
    "ping": "pong"
}

192.168.1.74 | success >> {
    "changed": false, 
    "ping": "pong"
}
```
`ansible ansibleLab -m setup -a 'filter=ansible_distribution'`
```
192.168.1.74 | success >> {
    "ansible_facts": {
        "ansible_distribution": "CentOS"
    }, 
    "changed": false
}

192.168.1.73 | success >> {
    "ansible_facts": {
        "ansible_distribution": "Ubuntu"
    }, 
    "changed": false
}
```

###Ansible playbook

Ansible sử dụng khái niệm 'play' để ám chỉ các cấu hình để quản lý tài nguyên (bao gồm quản lý gói, service, cron, file...) và nhiều 'play' kết hợp với nhau tạo thành 'playbook', khái niệm này tương đương với state khi làm việc với SaltStack.

Với mục tiêu giới thiệu sơ bộ về Ansible, mình sẽ viết 1 playbook đơn giản để làm cài đặt apache và đảm bảo apache listen trên các host.

`vim /srv/playbook.yml`
```
---
- hosts: ansibleLab
  gather_facts: yes
  tasks:
  - name: Install apache Ubuntu
    apt: name=apache2 state=present
    when: ansible_os_family == "Debian"
    
  - name: Install apache CentOS
    yum: name=httpd state=present
    when: ansible_os_family == "RedHat"
    
  - name: Start apache2
    service: name=apache2 state=started enabled=yes
    when: ansible_os_family == "Debian"
    
  - name: Start httpd
    service: name=httpd state=started enabled=yes
    when: ansible_os_family == "RedHat"
```
ansible-playbook /srv/playbook.yml -v

```
PLAY RECAP ******************************************************************** 
192.168.1.73               : ok=3    changed=1    unreachable=0    failed=0   
192.168.1.74               : ok=3    changed=2    unreachable=0    failed=0 
```
Để ý trường `failed=0`, nếu failed=0 là okie. Lần lượt vào 2 host để kiểm tra bằng lệnh `netstat -nltp`

TroubleShooting
```
192.168.5.119 | FAILED => Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host.
192.168.5.149 | FAILED => Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host.
```

Sửa file `/etc/ansible/ansible.cfg`
```
host_key_checking = False
```

Trên đây chỉ là 1 số thông tin rất cơ bản về ansible, các bạn có thể vào link http://docs.ansible.com/ tìm hiểu thêm về ansible để phát triển theo ý muốn.
