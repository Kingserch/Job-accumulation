+ ### Ansible简单使用
    + [Ansible使用](#Ansible使用)
	+ [坑位](#坑位)
	+ [坑位](#坑位)
	+ [坑位](#坑位)
	+ [坑位](#坑位)
+ ### Ansible使用
##### 1.安装Ansible
```
yum install -y epel-release
yum install -y ansible
[root@m129 ~]# ansible --version
ansible 2.9.2
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, Aug  7 2019, 00:51:29) [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)]
[root@m129 /]# systemctl stop nginx
[root@m129 /]# ansible localhost -b -c local -m service -a "name=nginx state=started"
localhost | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 	#nginx 没有启动时，ansible输出是黄颜色的字体
    "name": "nginx", 
    "state": "started", 
    "status": {
	...
    }
}
[root@m129 /]# systemctl status nginx
● nginx.service - nginx - high performance web server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since Tue 2020-01-07 22:19:46 CST; 1min 53s ago		#表示nginx启动
     Docs: http://nginx.org/en/docs/
  Process
  ...
[root@m129 /]# ansible localhost -b -c local -m service -a "name=nginx state=started"
localhost | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 	#nginx启动时，ansible输出是绿颜色的字体
    "name": "nginx", 
    "state": "started", 
    "status": {
	...
[root@m129 git_space]# git clone https://github.com/devops-book/ansible-playbook-sample.git
[root@m129 git_space]# cd ansible-playbook-sample/
[root@m129 ansible-playbook-sample]# tree
.
├── development
├── group_vars
│   ├── development-webservers.yml
│   └── production-webservers.yml
├── production
├── roles
│   ├── common
│   │   ├── meta
│   │   │   └── main.yml
│   │   └── tasks
│   │       └── main.yml
│   ├── jenkins
│   │   ├── defaults
│   │   │   └── main.yml
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── meta
│   │   │   └── main.yml
│   │   ├── README.md
│   │   ├── tasks
│   │   │   └── main.yml
│   │   ├── tests
│   │   │   ├── inventory
│   │   │   └── test.yml
│   │   └── vars
│   │       └── main.yml
│   ├── nginx
│   │   ├── meta
│   │   │   └── main.yml
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── templates
│   │       └── index.html.j2
│   ├── serverspec
│   │   ├── meta
│   │   │   └── main.yml
│   │   └── tasks
│   │       └── main.yml
│   └── serverspec_sample
│       ├── files
│       │   └── serverspec_sample
│       │       ├── Rakefile
│       │       └── spec
│       │           ├── localhost
│       │           └── spec_helper.rb
│       ├── meta
│       │   └── main.yml
│       ├── tasks
│       │   └── main.yml
│       ├── templates
│       │   ├── nginx_spec.rb.j2
│       │   └── web_spec.rb.j2
│       └── vars
│           └── main.yml
└── site.yml

28 directories, 27 files
[root@m129 ansible-playbook-sample]#

```

