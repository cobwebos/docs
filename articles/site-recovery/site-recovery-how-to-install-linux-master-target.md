---
title: "如何安装 Linux 主目标服务器用于从 Azure 故障转移到本地 | Microsoft 文档"
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
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 8ac18526e6781f189444233a191aa1d6c5b863ff
ms.lasthandoff: 02/22/2017


---
# <a name="how-to-install-linux-master-target-server"></a>如何安装 Linux 主目标服务器
故障转移虚拟机后，可将 VM 故障回复到本地。 若要故障回复，首先需要通过在本地重新保护 Azure 中的虚拟机，将虚拟机置于受保护状态。 为此，需要安装一个主目标服务器用于在本地接收流量。 如果受保护的虚拟机是 Windows VM，则需要安装 Windows 主目标。 对于 Linux VM，需要安装 Linux 主目标才能重新保护它。 请阅读以下步骤，了解如何创建和安装 Linux 主目标。

## <a name="overview"></a>概述
本文提供有关安装 Linux 主目标的信息和说明。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="pre-requisites"></a>先决条件

* 若要正确选择需要用来部署 MT 的主机，请确定是要故障回复到现有的本地 VM 还是新的 VM（因为本地 VM 已删除）。 
    * 对于现有 VM，MT 的主机应有权访问 VM 的数据存储。
    * 如果该 VM 不在本地，需在 MT 所在的同一台主机上创建故障回复 VM。 可以选择任何一台 ESXi 主机用于安装 MT。
* MT 应在可与进程服务器和配置服务器通信的网络中。
* MT 版本应该低于或等于进程服务器和配置服务器的版本。 （例如，如果 CS 版本为 9.4，则 MT 的版本可以是 9.4 和 9.3，而不能是 9.5）
* MT 只能是 VMware VM，而不能是物理 VM。


## <a name="steps-to-deploy-the-master-target-server"></a>部署主目标服务器的步骤

### <a name="install-centos-66-minimal"></a>安装 CentOS 6.6 Minimal

遵循如下所述的步骤安装 CentOS 6.6 - 64 位操作系统。

1. 通过以下链接选择最接近的镜像，以下载 CentOS 6.6 Minimal 64 位 ISO。

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    将 CentOS 6.6 Minimal 64 位 ISO 保存在 DVD 驱动器中，然后启动系统。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. 选择“跳过”忽略媒体测试过程。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. 现在应会出现安装程序的欢迎屏幕。 请单击“下一步”按钮。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. 选择“英语”作为首选语言，然后单击“下一步”继续。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. 选择“美国英语”作为键盘布局。 单击“下一步”继续安装。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. 选择要在其中安装的设备类型。 选择“基本存储设备”。 单击“下一步”继续安装。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. 此时将显示一条警告消息，指出硬盘驱动器中的现有数据将被删除。 请确保硬盘驱动器中不包含任何重要数据，然后单击“是，丢弃所有数据”。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. 在“主机名”文本框中输入服务器的主机名。 单击“配置网络”，在“网络连接”窗口中选择网络接口。 单击“编辑”按钮配置 IPV4Settings。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. 此时将显示以下“编辑系统 eth0”窗口。 选中“自动连接”复选框。 在“IPv4 设置”选项卡中，选择“手动”作为方法，然后单击“添加”按钮。 提供“静态 IP”、“子网掩码”、“网关”和“DNS 服务器”详细信息。 单击“应用”保存详细信息。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. 从组合框中选择你所在的时区，然后单击“下一步”继续。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. 输入“Root 密码”并确认密码，然后单击“下一步”继续。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. 选择“创建自定义布局”作为分区模式，然后单击“下一步”继续。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. 选择“免费”分区，然后单击“创建”，创建使用 **ext4** 作为文件系统类型的 **/**、**/var/crash** 和 **/home** 分区。 创建使用 **swap** 作为文件系统类型的**交换分区**。 若要分配分区大小，请参考下表中所述的大小分配公式。

    > [!NOTE]
    > Linux 主目标 (MT) 系统不应为根或保留存储空间使用 LVM。 默认情况下，Linux MT 已配置为避免发现 LVM 分区/磁盘。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. 创建分区后，单击“下一步”继续。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. 如果找到了任何现有设备，将显示一条警告消息，指出将要格式化该设备。 单击“格式化”，使用最新的分区表格式化硬盘驱动器。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. 单击“将更改写入磁盘”，应用在磁盘中所做的分区更改。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. 选中“安装启动加载程序”选项，然后单击“下一步”在根分区上安装启动加载程序。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. 安装过程开始。 可以监视安装进度。

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. 成功完成安装后，将显示以下屏幕。 单击“重新启动”

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>安装后的步骤
接下来，准备要配置为主目标服务器的计算机。

若要获取 Linux 虚拟机中每个 SCSI 硬盘的 SCSI ID，你应该启用参数“disk.EnableUUID = TRUE”。

若要启用此参数，请遵循下面提供的步骤：

a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 关闭你的虚拟机。

b.保留“数据库类型”设置，即设置为“共享”。 在左侧面板中右键单击 VM 对应的条目，然后选择“编辑设置”。

c. 单击“**选项**”选项卡。

d. 在左侧选择“高级”&gt;“常规”项，然后单击右侧显示的“配置参数”。

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

当计算机正在运行时，“配置参数”选项将处于停用状态。 若要使此选项卡处于活动状态，请关闭计算机。

e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 查看是否存在包含 **disk.EnableUUID** 的行。

f. 如果该值存在并且设置为 False，请将它覆盖为 True（True 和 False 值不区分大小写）。

g. 如果该值存在并且设置为 true，请单击“取消”。
    
h. 如果该值不存在，请单击“添加行”。

i. 在“名称”列中添加 disk.EnableUUID，并将其值设置为 TRUE

   * 如果存在该行并且其值设置为 False，请将该值覆盖为True（True 和 False 值不区分大小写）。

   * 如果存在该行并且其值设置为 true，请单击“取消”，然后在启动来宾操作系统后，在来宾操作系统中测试 SCSI 命令。

f. 如果不存在该行，请单击“添加行”。

  在“名称”列中添加 disk.EnableUUID。

  将其值设置为 TRUE



![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>下载并安装其他程序包

> [!NOTE]
> 在下载并安装其他包之前，请确保系统已建立 Internet 连接，否则需要手动找到并安装这些包 RPM。

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

上述命令将从 CentOS 6.6 存储库下载并安装下面提到的 15 个程序包。 如果无法访问 Internet，需要下载以下 RPM。


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


#### <a name="install-additional-packages-for-specific-operating-systems"></a>安装特定操作系统的附加包

> [!NOTE]
> 如果受保护的源计算机为根设备或引导设备使用了 Reiser 或 XFS 文件系统，则应该在保护之前，在 Linux 主目标中下载并安装以下附加包。
 

***ReiserFS（如果在 Suse11SP3 中使用。不过，ReiserFS 不是 Suse11SP3 中的默认文件系统）***

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS（RHEL、CentOS 7 及更高版本）***

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath 
```
在 MT 服务器上启用多路径包时需要此包。

### <a name="get-the-installer-for-setup"></a>获取安装程序

如果可以从主目标服务器访问 Internet，可通过以下步骤下载安装程序 - 否则，可以从进程服务器复制该安装程序并执行安装。

#### <a name="download-the-mt-installation-packages"></a>下载 MT 安装包

从 [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc) 下载最新的 Linux 主目标安装程序。

若要通过 Linux 下载该安装程序，请键入 

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

请务必将安装程序下载并解压缩到主目录。 如果解压缩到 /usr/Local，安装将会失败。


#### <a name="access-the-installer-from-the-process-server"></a>从进程服务器访问安装程序

转到进程服务器并导航到以下目录 - C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

从进程服务器复制所需的安装程序文件，并在主目录中将它保存为 latestlinuxmobsvc.tar.gz


### <a name="apply-custom-configuration-changes"></a>应用自定义配置更改

若要应用自定义配置更改，请遵循下述步骤：


1. 运行以下命令来解压缩二进制文件。
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
2. 执行以下命令来指定权限。
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. 执行以下命令来运行脚本。
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> 仅在服务器上执行该脚本一次。 关闭服务器。 根据后续步骤中所述添加磁盘后，重新启动服务器。

### <a name="add-retention-disk-to-linux-mt-vm"></a>将保留磁盘添加到 Linux MT VM 

遵循如下所述的步骤创建保留磁盘。

1. 将新的 **1-TB** 磁盘附加到 Linux MT VM 并**启动**计算机。

2. 调用 **multipath -ll** 命令来了解保留磁盘的多路径 ID。

    ```
    multipath -ll
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. 格式化驱动器并在新驱动器上创建文件系统。 
    
    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. 创建文件系统后，装载保留磁盘。
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. 最后，创建每次启动期间用于装载保留驱动器的 fstab 项
    ```
    vi /etc/fstab 
    ```
    按 [Insert] 开始编辑文件。 创建新行并在其中插入以下文本。 根据前一命令中突出显示的多路径 ID 编辑磁盘多路径 ID。

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    按 [Esc]，键入 :wq（写入并退出）关闭编辑器窗口。

### <a name="install-master-target"></a>安装主目标

> [!NOTE]
> 主目标服务器版本应该低于或等于进程服务器和配置服务器的版本。 如果 MT 版本更高，重新保护可以成功，但复制将会失败。
 

> [!NOTE]
> 安装主目标服务器之前，请检查 VM 上的 /etc/hosts 文件是否包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。
 
1. 在配置服务器上从 **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** 复制通行短语，然后运行以下命令，将通行短语保存在同一本地目录中的 passphrase.txt 内。

    ```
    echo <passphrase> >passphrase.txt
    ```
    示例：echo itUx70I47uxDuUVY >passphrase.txt

2. 记下配置服务器的 IP 地址， 下一步骤需要用到它

3. 运行以下命令安装主目标服务器并将它注册到配置服务器。
    
    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    示例：./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    等到脚本执行完成。 如果成功注册主目标，门户中的“Site Recovery 基础结构”页上会列出该 MT。

#### <a name="installing-master-target-using-interactive-install"></a>使用交互式安装过程安装主目标

1. 执行以下命令安装主目标。 选择“主目标”作为代理角色。

    ```
    ./install
    ```

2. 选择默认安装位置 - 按 [Enter] 继续
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. 选择要配置的“全局”设置

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. 指定 CS 服务器 IP 地址

5. 将“CS 服务器端口”指定为 443。
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. 在配置服务器上从 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 复制 CS 通行短语，然后在通行短语框中指定该通行短语。 即使在复制并粘贴之后，该框也会显示空白。

7. 在菜单中转到 [Quit]

8. 等待安装和注册完成。

### <a name="install-vmware-tools-on-the-master-target-server"></a>在主目标服务器上安装 VMware 工具

需将 VMware 工具安装在 MT 上，以便能够发现数据存储。 如果不安装这些工具，重新保护屏幕中不会列出数据存储。 安装 VMware 工具后，需要重新启动计算机。

## <a name="next-steps"></a>后续步骤
主目标安装和注册完成后，可以看到该 MT 显示在“Site Recovery 基础结构”中“主目标”部分的配置服务器概述下面。

现在，可以继续执行[重新保护](site-recovery-how-to-reprotect.md)过程，然后执行故障回复。

## <a name="common-issues"></a>常见问题

* 切勿在 MT 等任何管理组件上打开存储 vMotion。 如果重新保护成功后移动 MT，将无法分离 VMDK，并且故障回复会失败。
* MT 计算机不应在虚拟机上留下任何快照。 如果有快照，故障回复将会失败。
* 由于某些客户使用某些自定义 NIC 配置，使得网络接口在启动期间被禁用，在这种情况下，MT 代理无法初始化。 请确保正确设置以下属性。
    * 检查以太网卡文件 /etc/sysconfig/network-scripts/ifcfg-eth* 中的两个属性
        * BOOTPROTO=dhcp 
        * ONBOOT=yes


