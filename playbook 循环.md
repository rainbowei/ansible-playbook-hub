> #### 作者：孙科伟
## 1. 标准loops循环
ansible playbook循环主要是为了解决重复使用task。比如批量安装软件。按照以前的思路可能需要写多个task任务来安装多个软件。而通过循环，我只需要写一个task就可以。
* 例1：批量安装软件，可以直接跟列表。

```
  - name: install kubeadm，kubelet and kubectl
    yum:
      name:
        - kubelet-1.15.0
        - kubeadm-1.15.0 
        - kubectl-1.15.0
      state: installed 
```

* 例2：通过标准循环方式安装软件
  
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
with_items的值是python list数据结构，可以理解为每个task会循环读取list里面的值，然后key的名称是item，当然list里面也支持python 字典，
* 例3：通过字典的方式，批量copy文件。
 ```
 - name: install kubeadm，kubelet and kubectl
    copy:
        src: "{{ item.src }}"
        dest: "{{ item.dest }}"
    with_items:
       - {src: "/opt/docker-ce.repo", dest: "/etc/yum.repos.d/docker-ce.repo" }
       - {src: "/opt/kubernetes.repo", dest: "/etc/yum.repos.d/kubernetes.repo" }
 ```