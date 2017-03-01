---
title: "如何安装 Linux 主目标服务器 | Microsoft 文档"
description: "在重新保护 Linux VM 之前，需要一台 Linux 主目标服务器。 本文介绍如何安装该服务器。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 12/20/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: d1a7ed7e182530f81a426a4383297a49505f65ea
ms.openlocfilehash: d76ea0fb27ecece4e8dcd06a2dde9a0794071884


---
# <a name="how-to-install-linux-master-target-server"></a>如何安装 Linux 主目标服务器
Azure Site Recovery 服务有助于业务连续性和灾难恢复 (BCDR) 策略，因为它可以协调虚拟机和物理服务器的复制、故障转移和恢复。 虚拟机可复制到 Azure 中，也可复制到本地数据中心中。 如需快速概览，请阅读[什么是 Azure Site Recovery？](site-recovery-overview.md)

## <a name="overview"></a>概述
本文提供有关恢复（故障转移和故障回复）使用 Site Recovery 保护的虚拟机和物理服务器的信息与说明。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="pre-requisites"></a>先决条件

1. 若要正确选择需要用来部署 MT 的主机，请确定是要故障回复到现有的本地 VM 还是新的 VM（因为本地 VM 已删除）。 
    * 对于现有 VM，MT 的主机应有权访问 VM 的数据存储。
    * 如果该 VM 不在本地，将在 MT 所在的同一台主机上创建故障回复 VM。
2. MT 应在可与进程服务器和配置服务器通信的网络中。
3. MT 版本应该低于或等于进程服务器和配置服务器的版本。 （例如，如果 CS 版本为 9.4，则 MT 的版本可以是 9.4 和 9.3，而不能是 9.5）
4. MT 只能是 VMware VM，而不能是物理 VM。


## <a name="steps-to-deploy-the-master-target-server"></a>部署主目标服务器的步骤

### <a name="install-centos-66-minimal"></a>安装 CentOS 6.6 Minimal

遵循如下所述的步骤安装 CentOS 6.6 - 64 位操作系统。

**步骤 1：**通过以下链接选择最接近的镜像，以下载 CentOS 6.6 Minimal 64 位 ISO。

<http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

将 CentOS 6.6 Minimal 64 位 ISO 保存在 DVD 驱动器中，然后启动系统。

![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

**步骤 2：**选择“跳过”忽略媒体测试过程。

![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

**步骤 3：**现在，可以看到安装程序的欢迎屏幕。 请单击“下一步”按钮。

![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

**步骤 4：**选择“英语”作为首选语言，然后单击“下一步”继续。

![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

**步骤 5：**选择“美国英语”作为键盘布局。 单击“下一步”继续安装。

![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

**步骤 6：**选择要在其中安装的设备类型。 选择“基本存储设备”。

单击“下一步”继续安装。

![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

**步骤 7：**此时将显示一条警告消息，指出硬盘驱动器中的现有数据将被删除。 请确保硬盘驱动器中不包含任何重要数据，然后单击“是，丢弃所有数据”。

![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

**步骤 8：**在“主机名”文本框中输入服务器的主机名。
单击“配置网络”。

在“网络连接”窗口中选择网络接口。 单击“编辑”按钮配置 IPV4Settings。

![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

**步骤 9：**此时将显示以下“编辑系统 etho”窗口。 选中“自动连接”复选框。 在“IPv4 设置”选项卡中，选择“手动”作为方法，然后单击“添加”按钮。 提供“静态 IP”、“子网掩码”、“网关”和“DNS 服务器”详细信息。 单击“应用”保存详细信息。

![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

**步骤 10：**从组合框中选择你所在的时区，然后单击“下一步”继续。

![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

**步骤 11：**输入“Root 密码”并确认密码，然后单击“下一步”继续。

![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

**步骤 12：**选择“创建自定义布局”作为分区模式，然后单击“下一步”继续。

![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

**步骤 13：**选择“免费”分区，然后单击“创建”，创建使用 **ext4** 作为文件系统类型的 **/**、**/var/crash** 和 **/home** 分区。 创建使用 **swap** 作为文件系统类型的**交换分区**。 若要分配分区大小，请参考下表中所述的大小分配公式。

注意：Linux 主目标 (MT) 系统不应为根或保留存储空间使用 LVM。 默认情况下，Linux MT 已配置为避免发现 LVM 分区/磁盘。

![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

**步骤 14：**创建分区后，单击“下一步”继续。

![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

**步骤 15：**如果找到了任何现有设备，将显示一条警告消息，指出将要格式化该设备。

单击“格式化”，使用最新的分区表格式化硬盘驱动器。

![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)



**步骤 16：**单击“将更改写入磁盘”，应用在磁盘中所做的分区更改。

![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

**步骤 17：**选中“安装启动加载程序”选项，然后单击“下一步”在根分区上安装启动加载程序。

![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)



**步骤 18：**安装过程开始。 可以监视安装进度。

![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

**步骤 19：**成功完成安装后，将显示以下屏幕。 单击“重新启动”

![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>安装后的步骤

若要获取 Linux 虚拟机中每个 SCSI 硬盘的 SCSI ID，你应该启用参数“disk.EnableUUID = TRUE”。

若要启用此参数，请遵循下面提供的步骤：

a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 关闭你的虚拟机。

b.保留“数据库类型”设置，即设置为“共享”。 在左侧面板中右键单击 VM 对应的条目，然后选择“编辑设置”。

c. 单击“**选项**”选项卡。

d. 在左侧选择“高级”&gt;“常规”项，然后单击右侧显示的“配置参数”。

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

当计算机正在运行时，“配置参数”选项将处于停用状态。 若要使此选项卡处于活动状态，请关闭计算机。

e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 查看是否存在包含 **disk.EnableUUID** 的行。

  如果存在该行并且其值设置为 False，请将该值覆盖为True（True 和 False 值不区分大小写）。

  如果存在该行并且其值设置为 true，请单击“取消”，然后在启动来宾操作系统后，在来宾操作系统中测试 SCSI 命令。

f. 如果不存在该行，请单击“添加行”。

  在“名称”列中添加 disk.EnableUUID。

  将其值设置为 TRUE

注意：添加上述值时请不要包括双引号。

![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>下载并安装其他程序包

注意：在下载并安装其他程序包之前，请确保系统已建立 Internet 连接。

```
# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

上述命令将从 CentOS 6.6 存储库下载并安装下面提到的 15 个程序包。


bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm

注意：如果受保护的源计算机为根设备或引导设备使用了 Reiser 或 XFS 文件系统，则应该在保护之前，在 Linux 主目标中下载并安装以下附加包。

***ReiserFS（Suse11SP3。不过，ReiserFS 不是 Suse11SP3 中的默认文件系统）***

```
# cd /usr/local

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS（RHEL、CentOS 7 及更高版本）***

```
# cd /usr/local

# wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

# yum install device-mapper-multipath 
```
在 MT 服务器上启用多路径包时需要此包。

### <a name="download-the-mt-installation-packages"></a>下载 MT 安装包

从 [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc) 下载最新的 Linux 主目标安装程序。

若要通过 Linux 下载该安装程序，请键入 

```
    # wget https://aka.ms/latestlinuxmobsvc
```

请务必将安装程序下载并解压缩到主目录。 如果解压缩到 /usr/Local，安装将会失败。

### <a name="apply-custom-configuration-changes"></a>应用自定义配置更改

在应用自定义配置更改之前，请确保已完成安装后步骤

若要应用自定义配置更改，请遵循下述步骤：

1. 将 RHEL6-64 统一代理二进制文件复制到新建的 OS。

2. 运行以下命令来解压缩二进制文件。
    ```
    tar -zxvf <File name>
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
3. 执行以下命令来指定权限。
    ```
    # chmod 755 ./ApplyCustomChanges.sh
    ```

4. 执行以下命令来运行脚本。
    ```
    # ./ApplyCustomChanges.sh
    ```
注意：仅在服务器上执行该脚本一次。 成功执行上述脚本后，**重新启动**服务器。

### <a name="add-retention-disk-to-linux-mt-vm"></a>将保留磁盘添加到 Linux MT VM 

遵循如下所述的步骤创建保留磁盘。

**步骤 1：**将新的 **1 TB** 磁盘附加到 Linux MT VM 并找出其多路径 ID

调用 **multipath -ll** 命令来了解保留磁盘的多路径 ID。

![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

**步骤 2：**调用 **mkfs.ext4 /dev/mapper/<保留磁盘的多路径 ID>** 命令，在保留磁盘上创建文件系统。

![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

**步骤 3：**创建文件系统后，调用以下命令来装载保留磁盘。

![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

**步骤 4：**最后，创建 /etc/fstab 条目：
```
vi /etc/fstab 
```
并附加以下行

** /dev/mapper/36000c2989daa2fe6dddcde67f2079afe /mnt/retention ext4 rw 0 0 **

### <a name="install-master-target"></a>安装主目标


> [!NOTE]
> 主目标服务器版本应该低于或等于进程服务器和配置服务器的版本。 如果 MT 版本更高，重新保护可以成功，但复制将会失败。
> 

> [!NOTE]
> 安装主目标服务器之前，请检查 VM 上的 /etc/hosts 文件是否包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。
> 


1. 执行以下命令安装主目标。 选择“主目标”作为代理角色。
```
# ./install
```

2. 选择默认安装位置
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. 选择要配置的“全局”设置

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. 指定 CS 服务器 IP 地址

5. 指定 CX 服务器端口 - 通常是 9443。
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. 在配置服务器上从 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 复制 CS 通行短语。

7. 等待安装和注册完成。
### <a name="install-vmware-tools-on-the-master-target-server"></a>在主目标服务器上安装 VMware 工具

需将 VMware 工具安装在 MT 上，以便能够发现数据存储。 如果不安装这些工具，重新保护屏幕中不会列出数据存储。

## <a name="next-steps"></a>后续步骤
 

## <a name="common-issues"></a>常见问题



<!--HONumber=Feb17_HO3-->


