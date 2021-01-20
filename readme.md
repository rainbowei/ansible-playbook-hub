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
    mode: "0755"
    owner: jenkins
    group: jenkins
```
1. 当我们想要创建的 /etc/docker是一个目录时，需要将state的值设置为directory，”directory”为目录之意。
2. ，当我们想要操作的/etc/docker是一个文件时，则需要将state的值设置为touch
3. mode,owner,group分别指所创建的文本或目录的权限，用户主，用户组。


- #### 4. copy：
```
- name: Copy maven .m2/settings.xml settings
  copy:
    src: settings.xml
    dest: /home/jenkins/.m2/settings.xml
    mode: "0644"
    owner: jenkins
    group: jenkins
  ```
1. src:将本地路径复制到远程服务器; 可以是绝对路径或相对的。如果是一个目录，它将被递归地复制。如果路径以/结尾，则只有该目录下内容被复制到目的地，如果没有使用/来结尾，则包含目录在内的整个内容全部复制
2. dest:目标绝对路径。如果src是一个目录，dest也必须是一个目录。如果dest是不存在的路径，并且如果dest以/结尾或者src是目录，则dest被创建。如果src和dest是文件，如果dest的父目录不存在，任务将失败
3. mode,owner,group分别指所创建的文本或目录的权限，用户主，用户组。
4. backup ：在覆盖之前将原文件备份，备份文件包含时间信息
   


- #### 5. service：
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

- #### 6. template：
  ```
  - name: Create jenkins master compose file
  template:
    src:  docker-compose.yml.j2
    dest: /data/docker-compose/jenkins/docker-compose.yml

  ```
| 参数名 | 是否必须 | 默认值 | 选项   | 说明                                                       |
| ------ | :------- | ----- | ------ | ---------------------------------------------------------- |
| backup | no     | no     | yes/no | 建立个包括timestamp在内的文件备份，以备不时之需            |
| dest   | yes      |        |        | 远程节点上的绝对路径，用于放置template文件。               |
| group  | no       |        |        | 设置远程节点上的的template文件的所属用户组                 |
| mode   | no       |        |        | 设置远程节点上的template文件权限。类似Linux中*chmod*的用法 |
| owner  | no       |        |        | 设置远程节点上的template文件所属用户                       |
| src    | yes      |        |        | 本地Jinjia2模版的template文件位置。                        |

- #### 7. sysctl：
```
# 将桥接的IPv4流量传递到iptables的链
  - name: Disable swappiness and pass bridged IPv4 traffic to iptable's chains
    sysctl:
      name: "{{ item.name }}"
      value: "{{ item.value }}"
      state: present
    with_items:
      - { name: 'vm.swappiness', value: '0' }
      - { name: 'net.bridge.bridge-nf-call-iptables', value: '1' }
      - { name: 'net.bridge.bridge-nf-call-ip6tables', value: '1' }
```
1. state：state=present修改,state=absent 删除.
2. reload ：reload=yes 重载 sysctl.conf 文件
- #### 8. user：
  ```
- name: Create the nginx user
  user:
    name: nginx
    uid: '1012'
    shell: /sbin/nologin
    create_home: no
   ```
* home：指定用户的家目录，需要与createhome配合使用
* groups：指定用户的属组
* uid：指定用的uid
* password：指定用户的密码
* name：指定用户名
* createhome：是否创建家目录 yes|no
* system：是否为系统用户
* remove：当state=absent时，remove=yes则表示连同家目录一起删除，等价于userdel -r
* state：是创建还是删除
* shell：指定用户的shell环境 
- #### 9.synchronize ：
```
- name: synchronize nginx lua config to /usr/local/openresty/nginx/lua
  synchronize:
    src: "{{ role_path }}/files/nginx/lua/"
    dest: /usr/local/openresty/nginx/lua
    delete: yes
    recursive: yes
    links: yes
  notify:
  - reload openresty
```
 要使用rsync模块，系统必须按照rsync包
* archive: 归档，相当于同时开启recursive(递归)、links、perms、times、owner、group、-D选项都为yes ，默认该项为开启

* checksum: 跳过检测sum值，默认关闭

* compress:是否开启压缩

* copy_links：复制链接文件，默认为no ，注意后面还有一个links参数

* delete: 删除不存在的文件，默认no

* dest：目录路径

* dest_port：默认目录主机上的端口 ，默认是22，走的ssh协议

* dirs：传速目录不进行递归，默认为no，即进行目录递归

* rsync_opts：rsync参数部分

* set_remote_user：主要用于/etc/ansible/hosts中定义或默认使用的用户与rsync使用的用户不同的情况

* mode: push或pull 模块，push模的话，一般用于从本机向远程主机上传文件，pull 模式用于从远程主机上取文
- #### 10.parted:
```
- name: Create a new primary partition
  parted:
    device: /dev/vdb
    number: 1
    label: gpt
    state: present

```
- 常用参数：
    * device:      ##分盘的设备
    * number:      ##第几块分区
    * part_start:  ##分区起始点
    * part_end:    ##分区结束点，即分区大小
    * state:       ##present建立分区，absent删除分区
    * unit:        ##默认的分区大小单位，Choices: s, B, KB, KiB, MB, MiB, GB, GiB, TB, TiB, %, cyl,chs, compact
    * label        ##设置盘符的标签，Choices: aix, amiga, bsd, dvh, gpt, loop, mac, msdos, pc98, sun
- #### 11.filesystem:
  ```
  - name: Create a xfs filesystem on /dev/vdb1
  filesystem:
    fstype: xfs
    dev: /dev/vdb1
    opts: '-n ftype=1'
  ```
  - 常用参数：
	fstype: 设置文件类型，(Aliases: type)(Choices: btrfs, ext2, ext3, ext4, ext4dev,f2fs, lvm, ocfs2, reiserfs, xfs, vfat, swap)
    dev :选择修改的硬盘文件

- #### 12.mount:
  ```
  - name: Mount /dev/vdb1 to /data
  mount:
    path: /data
    src: /dev/vdb1
    fstype: xfs
    opts: 'defaults,noatime'
    state: mounted
    ```
- 常用参数：
  backup：	  备份挂载目录的原有文件
  fstab :     永久挂载到的位置
  fstype:     挂载的硬盘类型
  path:        挂载点
  src:        挂载的文件
  state:      present挂载，absent解除挂载
  opts:       挂载参数( 如 ro,noauto)

  - #### 13.authorized_key:
```
- name: Copy authorized key to glops home directory
  authorized_key:
    user: glops
    path: /home/glops/.ssh/authorized_keys
    state: present
    key: "{{ lookup('file','glops_id_rsa.pub') }}"
    ```
  新增公钥内容到服务器用户家目录的.ssh目录的authorized_keys文件没有则创建authorized_keys文件 
  state: (1) present 添加 (2) absent 删除
```
* 例子： 创建ops用户，赋予sudo权限。并免登录。  
```
---
- hosts: nodes
  remote_user: root
  tasks:
   - name: Add the user 'ops' with a specific uid 
     user:
      name: ops
      comment: ops
      uid: 1002

   - name: Copy authorized key to ops home directory
     authorized_key:
       user: ops
       state: present
       key: "{{ lookup('file','/home/ops/.ssh/id_rsa.pub') }}"
  
   - name: Create Sudoers.d ops files
     template:
       src: sudoers.j2
       dest: "/etc/sudoers.d/{{ item }}"
     with_items:
     - "ops
```
- #### 14 blockinfile:
```
- name: sudoers 修改sudo文件
  blockinfile: 
    path: /etc/sudoers
    block: |
      {{user}} ALL=(ALL) NOPASSWD:ALL
    marker: "#{mark} add {{user}} in /etc/sudoers"  
    state: present
```
* path参数 ：必须参数，指定要操作的文件。

* block参数 ：此参数用于指定我们想要操作的那”一段文本”，此参数有一个别名叫”content”，使用content或block的作用是相同的。

* marker参数 ：假如我们想要在指定文件中插入一段文本，ansible会自动为这段文本添加两个标记，一个开始标记，一个结束标记，默认情况下，开始标记为# BEGIN ANSIBLE MANAGED BLOCK，结束标记为# END ANSIBLE MANAGED BLOCK，我们可以使用marker参数自定义”标记”。比如，marker=#{mark}test ，这样设置以后，开始标记变成了# BEGIN test，结束标记变成了# END test，没错，{mark}会自动被替换成开始标记和结束标记中的BEGIN和END，我们也可以插入很多段文本，为不同的段落添加不同的标记，下次通过对应的标记即可找到对应的段落。

* state参数 : state参数有两个可选值，present与absent，默认情况下，我们会将指定的一段文本”插入”到文件中，如果对应的文件中已经存在对应标记的文本，默认会更新对应段落，在执行插入操作或更新操作时，state的值为present，默认值就是present，如果对应的文件中已经存在对应标记的文本并且将state的值设置为absent，则表示从文件中删除对应标记的段落。

* insertafter参数 ：在插入一段文本时，默认会在文件的末尾插入文本，如果你想要将文本插入在某一行的后面，可以使用此参数指定对应的行，也可以使用正则表达式(python正则)，表示将文本插入在符合正则表达式的行的后面。如果有多行文本都能够匹配对应的正则表达式，则以最后一个满足正则的行为准，此参数的值还可以设置为EOF，表示将文本插入到文档末尾。

* insertbefore参数 ：在插入一段文本时，默认会在文件的末尾插入文本，如果你想要将文本插入在某一行的前面，可以使用此参数指定对应的行，也可以使用正则表达式(python正则)，表示将文本插入在符合正则表达式的行的前面。如果有多行文本都能够匹配对应的正则表达式，则以最后一个满足正则的行为准，此参数的值还可以设置为BOF，表示将文本插入到文档开头。

* backup参数 ：是否在修改文件之前对文件进行备份。

* create参数 ：当要操作的文件并不存在时，是否创建对应的文件

 - #### 15 unarchive模块:
1、将ansible主机上的压缩包在本地解压缩后传到远程主机上，这种情况下，copy=yes.   本地解压缩,解压缩位置不是默认的目录,没找到或传完删了后传到远程主机
2、将远程主机上的某个压缩包解压缩到指定路径下。这种情况下，需要设置copy=no   远程主机上面的操作,不涉及ansible服务端
```
- name: 解压jdk文件到服务器
  unarchive:
   src: "https://www.iworkh.com/download/share/99-%E8%BD%AF%E4%BB%B6/04-%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/jdk/jdk8u251/jdk-8u251-linux-x64.tar.gz"
   dest: '{{jdk_home}}'
   remote_src: yes
   mode: 0755
```
当src路径为远程http路径时，需要设置remote_src: yes 如果是ansible server宿主机本地路径则需要设置cope: yes 默认值是yes。
参数：

* copy：默认为yes，当copy=yes，那么拷贝的文件是从ansible主机复制到远程主机上的，如果设置为copy=no，那么会在远程主机上寻找src源文件

* src：源路径，可以是ansible主机上的路径，也可以是远程主机上的路径，如果是远程主机上的路径，则需要设置copy=no
* dest：远程主机上的目标路径
* remote_src:设置为yes指示已存档文件已在远程系统上，而不是Ansible控制器的本地文件。
* mode：设置解压缩后的文件权限

