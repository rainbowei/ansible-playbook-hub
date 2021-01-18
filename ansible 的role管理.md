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
## 2. role举个小栗子：





