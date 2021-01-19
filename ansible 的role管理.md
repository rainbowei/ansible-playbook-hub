> #### 作者：孙科伟
## 1. role目录结构：
 一个简单的role目录结构如下：
 ```
 demorole/
├── defaults
│   └── main.yaml
├── files
│   └── main.yaml
├── meta
│   └── main.yaml
├── tasks
│   └── main.yaml
├── templates
│   └── main.yaml
└── vars
    └── main.yam
 ```

* defaults目录：角色会使用到的变量可以写入到此目录中的main.yml文件中，通常，defaults/main.yml文件中的变量都用于设置默认值，以便在你没有设置对应变量值时，变量有默认的值可以使用，定义在defaults/main.yml文件中的变量的优先级是最低的。
* files目录：角色可能会用到的一些其他文件可以放置在此目录中，比如，当你定义nginx角色时，需要配置https，那么相关的证书文件即可放置在此目录中。
* meta目录：如果你想要赋予这个角色一些元数据，则可以将元数据写入到meta/main.yml文件中，这些元数据用于描述角色的相关属性，比如 作者信息、角色主要作用等等，你也可以在meta/main.yml文件中定义这个角色依赖于哪些其他角色，或者改变角色的默认调用设定，在之后会有一些实际的示例，此处不用纠结。
* tasks目录：角色需要执行的主任务文件放置在此目录中，默认的主任务文件名为main.yml，当调用角色时，默认会执行main.yml文件中的任务，你也可以将其他需要执行的任务文件通过include的方式包含在tasks/main.yml文件中。
* templates目录： 角色相关的模板文件可以放置在此目录中，当使用角色相关的模板时，如果没有指定路径，会默认从此目录中查找对应名称的模板文件。
* vars目录：角色会使用到的变量可以写入到此目录中的main.yml文件中，看到这里你肯定会有疑问，vars/main.yml文件和defaults/main.yml文件的区别在哪里呢？区别就是，defaults/main.yml文件中的变量的优先级是最低的，而vars/main.yml文件中的变量的优先级非常高，如果你只是想提供一个默认的配置，那么你可以把对应的变量定义在defaults/main.yml中，如果你想要确保别人在调用角色时，使用的值就是你指定的值，则可以将变量定义在vars/main.yml中，因为定义在vars/main.yml文件中的变量的优先级非常高，所以其值比较难以覆盖。

* handlers目录：当角色需要调用handlers时，默认会在此目录中的main.yml文件中查找对应的handler

另外如果不想手动创建role目录结构的话也可以通过ansible-galaxy init rolename 命令创建，rolename表示角色名字。例如创建名为nginx的role。
```
[root@k8-master roles]# ansible-galaxy init nginx
- Role nginx was created successfully
[root@k8-master roles]# tree  nginx
nginx
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── README.md
├── tasks
│   └── main.yml
├── templates
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml
```
## 2. site.yaml文件：

我们可以在role目录的同级目录，建立site.yml(名字可以随便起)文件用来执行我们的role。这时候site.yml直接用相对目录即可例如：
```
[root@k8-master roles]# ls
demorole  nginx  site.yml  testrole
[root@k8-master roles]# cat site.yml 
- hosts: node1
  roles:
   - nginx
```
配置文件中site.yml 定义的roles nginx和nginx角色目录同级的时候，可以用绝对目录。另外也可以把nginx角色目录放在 ~/.ansible/roles 目录下，这时候site.yml文件则可以放在任意目录。另外还可以用绝对路径，这时候site.yml也可以放在任何目录执行。例如：
```
- hosts: node1
  roles:
   - "/data/roles/nginx"
```
你也可修改ansible的配置文件，设置自己的角色搜索目录，编辑/etc/ansible/ansible.cfg配置文件，设置roles_path选项，此项默认是注释掉的，将注释符去掉，当你想要设置多个路径时，多个路径之间用冒号隔开，示例如下:
```
roles_path    = /etc/ansible/roles:/opt:/testdir
```
## 3 role变量优先级：
/var/main.yml 文件中变量优先级是最高的。/defaults/main.yml 文件中变量优先级最低。另外site.yml中的变量是全局变量。




