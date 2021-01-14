> #### 作者：孙科伟
## 1. 标准loops循环
ansible playbook循环主要是为了解决重复使用task。比如批量安装软件。按照以前的思路可能需要写多个task任务来安装多个软件。而通过循环，我只需要写一个task就可以。
* with_items 循环
1.  例：批量安装软件，可以直接跟列表。

```
  - name: install kubeadm，kubelet and kubectl
    yum:
      name:
        - kubelet-1.15.0
        - kubeadm-1.15.0 
        - kubectl-1.15.0
      state: installed 
```

2. 例：通过标准循环方式安装软件
  
```
  - name: install kubeadm，kubelet and kubectl
    yum:
      name: "{{item}}"
      state: installed 
    with_items:
       - kubelet-1.15.0
       - kubeadm-1.15.0 
       - kubectl-1.15.0  
```

等效于：
```
  - name: install kubeadm，kubelet and kubectl
    yum:
      name: "{{item}}"
      state: installed 
    with_items: [ kubelet-1.15.0,kubeadm-1.15.0,kubectl-1.15.0 ]
```

等效于：
```
 - name: install kubeadm，kubelet and kubectl
    yum:
      name: "{{item}}"
      state: installed 
    with_items:
        -  [  kubelet-1.15.0,kubeadm-1.15.0 ]
        -  [ kubectl-1.15.0 ]
```
当with _items为多个列表的时候（这时候也可以用关键字with_flattened，意思是一样的），这会以此循环输出每个列表的值。因此item的值分别为kubelet-1.15.0，kubeadm-1.15.0， kubectl-1.15.0 。和单个列表没什么区别。可以看结果```：changed: [10.2.1.196] => (item=[u'wget', u'vim', u'lrzsz'])```


  3. 例：通过字典的方式，批量copy文件
     with_items的值是python list数据结构，可以理解为每个task会循环读取list里面的值，然后key的名称是item，当然list里面也支持python 字典，
 ```
 - name: install kubeadm，kubelet and kubectl
    copy:
        src: "{{ item.src }}"
        dest: "{{ item.dest }}"
    with_items:
       - {src: "/opt/docker-ce.repo", dest: "/etc/yum.repos.d/docker-ce.repo" }
       - {src: "/opt/kubernetes.repo", dest: "/etc/yum.repos.d/kubernetes.repo" }
 ```
* with_items 多列表循环：
  
当使用with_items时，且为多个列表时，则会以此循环每个列表内的值。详见例3最后等效于。item会循环3次，每个值依次为，kubelet-1.15.0，kubeadm-1.15.0 ， kubectl-1.15.0 。如果想每次循环整个列表作为整体。则使用with_list.
```
---
- hosts: node1
  remote_user: root
  tasks:
    - name: install kubeadm，kubelet and kubectl
      yum:
        name: "{{ item }}"
        state: installed 
      with_list:
         - [ wget,vim ]
         - [ lrzsz ]
```
则输出结果为：```changed: [10.2.1.196] => (item=[u'wget', u'vim'])
changed: [10.2.1.196] => (item=[u'lrzsz'])```





  