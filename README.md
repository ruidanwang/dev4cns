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
    - 