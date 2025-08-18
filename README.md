# kubespray-k8s
## kubespray là gì ?
Kubespray là một công cụ mã nguồn mở dùng để triển khai Kubernetes trên nhiều máy chủ vật lý hoặc máy ảo một cách tự động, dựa trên Ansible. Nó giúp bạn tạo ra một cụm Kubernetes (cluster) production-ready mà không phải cấu hình thủ công từng node.

Cụ thể, Kubespray cung cấp:

**Triển khai multi-node Kubernetes:**

1. Hỗ trợ cả cluster high-availability (HA) với nhiều control plane.

2. Hỗ trợ cả các node worker và node control plane.

**Tùy biến cấu hình dễ dàng:**

1. Sử dụng file YAML để định nghĩa các tham số cluster, mạng, lưu trữ, addons, v.v.

2. Cho phép chọn CNI plugin (Calico, Flannel, Weave, Canal…).

**Tự động hóa bằng Ansible:**

1.  Mọi bước như cài Docker/Containerd, kubelet, kubeadm, cấu hình mạng, HA setup, load balancer đều được tự động hóa.

**Hỗ trợ nhiều nền tảng:**

1. Có thể chạy trên bare-metal, VMs, hoặc cloud (AWS, GCP, Azure…).

**Nói ngắn gọn: Kubespray là một “bộ sưu tập Ansible playbooks” giúp bạn triển khai và quản lý Kubernetes cluster một cách nhanh chóng, nhất quán và dễ bảo trì.**

## Cách cài đặt

**Tải kubespray**

1. Clone lại repo Kubespray:

`git clone https://github.com/kubernetes-sigs/kubespray.git`

2. Vào thư mục Kubespray:

`cd kubespray`

3. Copy inventory mẫu:

`cp -rfp inventory/sample inventory/mycluster`

**Chuẩn bị môi trường**

1. Cài đặt python

```
sudo apt update
sudo apt install python3.10 python3.10-venv -y
```

2. Tạo venv

```
python3.10 -m venv venv-kubespray
source venv-kubespray/bin/activate
pip install --upgrade pip
pip install "ansible-core>=2.15,<2.18"
```

3. Tạo venv mới với Python 3.10

```
python3.10 -m venv venv-kubespray
```

4. Kích hoạt venv

`source venv-kubespray/bin/activate`

5. Nâng pip & cài ansible-core

```
pip install --upgrade pip
pip install "ansible-core>=2.15,<2.18"
```

**Cài requirements của Kubespray**

`pip install -r requirements.txt`


**Sửa inventory/mycluster/hosts.yaml để khai báo các node (master, worker).**

```
all:
  hosts:
    master1:
      ansible_host: 192.168.1.10
      ip: 192.168.1.10
      access_ip: 192.168.1.10
    worker1:
      ansible_host: 192.168.1.11
      ip: 192.168.1.11
      access_ip: 192.168.1.11

  children:
    kube_control_plane:
      hosts:
        master1:
    kube_node:
      hosts:
        worker1:
    etcd:
      hosts:
        master1:
    k8s_cluster:
      children:
        kube_control_plane:
        kube_node:
    calico_rr:
      hosts: {}
```

**Triển khai Kubernetes Cluster**

`ansible-playbook -i inventory/mycluster/hosts.yaml cluster.yml -b -v`

 *Nếu gặp lỗi yêu cầu mật khẩu*

 **Cho phép user trên các node sudo không cần password**

 1. Trên cả master1 và worker1, SSH vào rồi chạy:

`sudo visudo`

2. Thêm dòng (giả sử user bạn dùng để SSH là ubuntu):

`ubuntu ALL=(ALL) NOPASSWD: ALL`

**Rồi test:**

`kubectl get nodes`

(fix)

**mở**

`nano inventory/mycluster/group_vars/all/download.yml`

nội dung:

```
---
## Directory where to cache downloaded files
## Nếu bạn không muốn cache → để false
download_cache_dir: false

## Keep the package cache on the hosts
download_keep_remote_cache: false

## Run download tasks only once for all hosts
download_run_once: true

## Nếu bạn muốn tải file về localhost rồi phân phối lại cho các node → bật lên
download_localhost: true

## Có verify checksum khi tải file không
download_verify_checksum: true

## Dùng ipv6 khi tải (nếu có)
download_use_ipv6: false

## Timeout khi tải file (giây)
download_connect_timeout: 20
download_retries: 4
download_delay: 5
```





















