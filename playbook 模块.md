> #### 作者：孙科伟
## 1. 常用模块
- #### yum：
```
- name: install the latest version of docker
  yum:
    name: docker
    state: latest
    enablerepo：docker-ce-stable
    disablerepo：epel
```

1. name 必选 指定安装包名
2. state 执行命令  present  installed removed latest absent其中installed and present等效  latest标志安装yum中最新版本，absent
and removed 等效 表示删除安装包
3. enablerepo：指定安装软件所用临时源，可选项。
4. disablerepo：用于指定安装软件包是临时启用的yum源。可选项
  
5. 安装多个软件包
```
- name: install package
  hosts: all
  tasks:
  - name: install php and mariaDB
    yum:
      name:
      - php
      - mariadb
      state: present
    when: ansible_hostname in groups['dev']  
```
- #### 2. yum_repository：
```
- name: Add the docker-ce yum repository
  yum_repository:
    name: docker-ce-stable
    description: Docker CE Stable - $basearch
    baseurl: https://mirrors.huaweicloud.com/docker-ce/linux/centos/7/$basearch/stable
    gpgkey: https://mirrors.huaweicloud.com/docker-ce/linux/centos/gpg 
    file: docker-ce
    gpgcheck: yes
    enabled: yes
  check_mode: no
  ```
1. name参数：必须参数，用于指定要操作的唯一的仓库ID，也就是”.repo”配置文件中每个仓库对应的”中括号”内的仓库ID。(必选)
2. baseurl参数：此参数用于设置 yum 仓库的 baseurl。（必选）
3. description参数：此参数用于设置仓库的注释信息，也就是”.repo”配置文件中每个仓库对应的”name字段”对应的内容。（避险）
4. gpgcakey参数：当 gpgcheck 参数设置为 yes 时，需要使用此参数指定验证包所需的公钥。
5. file参数：此参数用于设置仓库的配置文件名称，即设置”.repo”配置文件的文件名前缀，在不使用此参数的情况下，默认以 name 参数的仓库ID作为”.repo”配置文件的文件名前缀，同一个”.repo” 配置文件中可以存在多个 yum 源。
6. gpgcheck参数：此参数用于设置是否开启 rpm 包验证功能，默认值为 no，表示不启用包验证，设置为 yes 表示开启包验证功能。
7. enabled参数：此参数用于设置是否激活对应的 yum 源，此参数默认值为 yes，表示启用对应的 yum 源，设置为 no 表示不启用对应的 yum 源。
8. state参数：默认值为 present，当值设置为 absent 时，表示删除对应的 yum 源。


- #### 3 file：
  file 模块可以帮助我们完成一些对文件的基本操作。比如，创建文件或目录、删除文件或目录、修改文件权限等。

```
- name: Create /etc/docker directory 
  file:
    path: /etc/docker
    state: directory
```
1. 当我们想要创建的 /etc/docker是一个目录时，需要将state的值设置为directory，”directory”为目录之意。
2. ，当我们想要操作的/etc/docker是一个文件时，则需要将state的值设置为touch
- #### 3. service：
```
- name:systemctl stop/disable firewalld
  service:
    name: firewalld
    enable: no
    state: stopped
```
1. enabled: 此参数用于指定是否将服务设置为开机 启动项，设置为 yes 表示将对应服务设置为开机启动，设置为 no 表示不会开机启动。
2. name: 表示要控制哪一个服务
3. state: 此参数用于指定服务的状态，比如，我们想要启动远程主机中的 nginx，则可以将 state 的值设置为 started；如果想要停止远程主机中的服务，则可以将 state 的值设置为 stopped。此参数的可用值有 started、stopped、restarted、reloaded。 
4 arguments:表示向命令行传递的参数
