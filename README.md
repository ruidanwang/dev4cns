# dev4cns (develop for container native storage)

## 2026-10-08

1. ZFS Zettabyte File System,也叫动态文件系统（Dynamic File System）,是第一个128位文件系统。
2. ZFS localPV
3. 实验 openEBS ZFS LocalPV
    - 更新包并安装ZFS工具
        ```bash
        apt update
        apt install -y zfsutils-linux
        ```
    - 加载ZFS内核模块（安装后通常自动加载，用以下命令验证）
        ```bash
        lsmod | grep zfs
        ```
    - 创建ZFS 存储池
        ```bash
        # 使用空闲磁盘（生产）
        zpool create zfspv-pool /dev/sdb
        ```
        ```bash
        #使用文件创建池
        #创建一个5GB 的文件
        truncate -s 5G /tmp/zfs-pool-file
        #使用该文件创建池
        zpool create zsfpv-pool /tmp/zfs-pool-file
        ```
        ```bash
        #验证
        zpool list
        zfs list
        ```
    - 安装openEBS ZFS localPV驱动
        ```bash
        #添加 OpenEBS ZFS LocalPV Helm 仓库
        helm repo add openebs-zfslocalpv https://openebs.github.io/zfs-localpv
        helm repo update
        ```
        ```bash
        #安装 ZFS LocalPV 驱动
        helm install zfs-localpv openebs-zfslocalpv/zfs-localpv \
            --namespace openebs \
            --create-namespace
        ```
        ```bash
        #验证驱动部署
        kubectl get pods -n openebs -l role=openebs-zfs
        ```
    - 创建ZFS StorageClass
        ```yaml
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
            name: openebs-zfspv
        allowVolumeExpansion: true
        parameters:
            poolname: "zfspv-pool"   # 指向创建的池名称
            fstype: "zfs"
        provisioner: zfs.csi.openebs.io
        ```
        ```bash
        echo 'apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
            name: openebs-zfspv
        allowVolumeExpansion: true
        parameters:
            poolname: "zfspv-pool"
            fstype: "zfs"
        provisioner: zfs.csi.openebs.io' > zfs-sc.yaml
        ```
    - 验证
        ```bash
        echo 'kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
            name: zfs-test-pvc
        spec:
            storageClassName: openebs-zfspv
            accessModes:
                - ReadWriteOnce
            resources:
                requests:
                    storage: 1Gi ' > test-pvc.yaml
        ```

## 2026-10-09

## 2026-10-10

1. openzfs的CoW机制是其核心设计原则
2. openzfs的pool structure结构指存储池的vdev；mirror是最常用结构
3. 