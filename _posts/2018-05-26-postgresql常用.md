---
categories: [SQL]
tags: [LVM,磁盘]
---


# Linux LVM使用记录

```bash
yum install -y lvm2 #安装组件
```



```bash
 fdisk -l #找出新硬盘
```
![fdisk -l](/upload/201706/lvm1.png)


```bash
#创建pv和vg
pvcreate /dev/sdb
```
![pvcreate](/upload/201706/lvm2.png)

```bash
 vgcreate testvg /dev/sdb
```
![vgcreate](/upload/201706/lvm3.png)

```bash
#创建lv并分配大小
lvcreate -l 100%vg testvg
```
![lvcreate](/upload/201706/lvm4.png)

```bash
#格式化逻辑卷
mkfs.xfs /dev/testvg/lvol0
```
![formartlv](/upload/201706/lvm5.png)

> 下面可以创建个目录挂载
如果是扩容至现有的lvm中可以采取下面的操作

> vgs 查看当前的vg情况
![vgsls](/upload/201706/lvm6.png)

```bash
#添加新硬盘到 cl 的vg中
vgextend cl /dev/sdb
#再次运行vgs，可以看的VFree里有了剩余
````
![vgsextend](/upload/201706/lvm7.png)
```bash
#把剩余空间分配给想扩容的lvs分区里
lvextend -l +100%free /dev/mapper/cl-root
```
![lvfree](/upload/201706/lvm8.png)

```bash
#初始下
xfs_growfs /dev/mapper/cl-root
```
![xfsinit](/upload/201706/lvm9.png)
```bash
#这是xfsd扩容，如果是ext4 则用 
resize2fs   /dev/mapper/cl-root
```

```bash
#扩容命令
#先找到新硬盘
fdisk -l
#如新硬盘为 /dev/vdf
#然后创建pv 
pvcreate /dev/vdf
#运行vgs 查看vg
#vg名称为 vgmdd
#增加pv到vg中
vgextend vgmdd /dev/vdf
#查看lvs 名称为 vgmdd-lvol0
#扩容lv
lvextend -l +100%free /dev/mapper/vgmdd-lvol0
#刷新下lv
xfs_growfs /dev/mapper/vgmdd-lvol0
#搞定
```