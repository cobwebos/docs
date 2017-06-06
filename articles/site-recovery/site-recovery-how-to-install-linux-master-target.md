---
title: "如何安装 Linux 主目标服务器用于从 Azure 故障转移到本地 | Microsoft Docs"
description: "在重新保护 Linux 虚拟机之前，需要一个 Linux 主目标服务器。 本文介绍如何安装该服务器。"
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
ms.date: 06/05/2017
ms.author: ruturajd
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 60102ebb43efc8710f102450df5b98edcb1d4b39
ms.contentlocale: zh-cn
ms.lasthandoff: 05/08/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>如何安装 Linux 主目标服务器
故障转移虚拟机后，可将虚拟机故障回复到本地站点。 若要故障回复，需要在本地站点中重新保护 Azure 中的虚拟机。 对于此过程，需要安装一个本地主目标服务器用于接收流量。 如果受保护的虚拟机是 Windows 虚拟机，则需要安装 Windows 主目标。 对于 Linux 虚拟机，需要安装 Linux 主目标。 请阅读以下步骤，了解如何创建和安装 Linux 主目标。

## <a name="overview"></a>概述
本文提供有关安装 Linux 主目标的信息和说明。

请在本文末尾或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中发表任何评论或问题。

## <a name="prerequisites"></a>先决条件

* 若要正确选择需要用来部署主目标的主机，请确定是要故障回复到现有的本地虚拟机还是新的虚拟机（因为本地虚拟机已删除）。
    * 对于现有虚拟机，主目标的主机应有权访问虚拟机的数据存储。
    * 如果本地虚拟机不存在，需在主目标所在的同一台主机上创建故障回复虚拟机。 可以选择任何一台 ESXi 主机用于安装主目标。
* 主目标应在可与进程服务器和配置服务器通信的网络中。
* 主目标版本应该低于或等于进程服务器和配置服务器的版本。 例如，如果配置服务器版本为 9.4，则主目标的版本可以是 9.4 或 9.3，而不能是 9.5。
* 主目标只能是 VMware 虚拟机，而不能是物理服务器。

## <a name="master-target-sizing-guideline"></a>主目标调整大小准则

主目标需要使用以下调整大小准则创建
    * RAM：6GB 或更多
    * OS 磁盘大小：100GB 或更多（用于安装 CentOS6.6）
    * 保留驱动器的附加磁盘大小：1TB
    * CPU 核心数：4 个核心或更多


## <a name="steps-to-deploy-the-master-target-server"></a>部署主目标服务器的步骤

### <a name="install-centos-66-minimal"></a>安装 CentOS 6.6 Minimal

使用以下步骤安装 64 位 CentOS 6.6 操作系统：

1. 通过以下链接选择最接近的镜像，以下载 CentOS 6.6 Minimal 64 位 ISO。

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    将 CentOS 6.6 Minimal 64 位 ISO 放在 DVD 驱动器中，然后启动系统。

    ![“欢迎使用 CentoOS 6.6”对话框](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. 单击“跳过”忽略媒体测试过程。

    ![选择“跳过”忽略媒体测试过程](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. 在安装程序的欢迎屏幕上单击“下一步”按钮。

    ![安装程序欢迎屏幕上的“下一步”按钮](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. 选择“英语”作为首选语言，然后单击“下一步”。

    ![选择语言](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. 选择“美国英语”作为键盘布局，然后单击“下一步”。

    ![选择英语键盘布局](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. 选择“基本存储设备”，然后单击“下一步”。

    ![选择存储设备](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. 此时将显示一条警告消息，指出硬盘驱动器中的现有数据将被删除。 请确保硬盘驱动器中不包含任何重要数据，然后单击“是，丢弃所有数据”。

    ![警告消息，指出如果继续操作，将删除数据](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. 在“主机名”框中输入服务器的主机名，然后单击“配置网络”。 在“网络连接”对话框中选择网络接口，然后单击“编辑”按钮配置 IPV4Settings。

    ![选择主机名并配置 IPV4](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. 在“编辑系统 eth0”对话框中，选中“自动连接”复选框。 在“IPv4 设置”选项卡中，为“方法”选择“手动”，然后单击“添加”按钮。 提供“静态 IP”、“子网掩码”、“网关”和“DNS 服务器”详细信息。 单击“应用”保存详细信息。

    ![网络配置设置](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. 选择时区，然后单击“下一步”。

    ![选择时区](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. 输入“Root 密码”并确认该密码，然后单击“下一步”。

    ![添加密码](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. 选择“创建自定义布局”，然后单击“下一步”。

    ![选择安装类型](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. 选择“免费”分区，然后单击“创建”，创建使用 **ext4** 作为文件系统类型的 **/**、**/var/crash** 和 **/home** 分区。 创建使用 **swap** 作为文件系统类型的**交换分区**。 若要分配分区大小，请参考下表中的大小分配公式。

    > [!NOTE]
    > Linux 主目标服务器不应为根或保留存储空间使用逻辑卷管理器 (LVM)。 默认情况下，Linux 主目标已配置为避免发现 LVM 分区和磁盘。

    ![分区名称、分区大小和文件系统类型的表格](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. 创建分区后，单击“下一步”。

    ![显示分区选定值的对话框](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. 如果找到了任何现有设备，将显示一条警告消息，指出将要格式化该设备。 单击“格式化”，使用最新的分区表格式化硬盘驱动器。

    ![单击“格式化”按钮将磁盘格式化](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. 单击“将更改写入磁盘”，应用对磁盘所做的分区更改。

    ![单击“将更改写入磁盘”](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. 选中“安装启动加载程序”选项，然后单击“下一步”在根分区上安装启动加载程序。

    ![在根分区上安装启动加载程序](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. 安装过程随即开始。 可以监视进度。

    ![显示安装进度的对话框](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. 成功完成安装后，将显示以下屏幕。 单击“重新启动”。

    ![安装成功屏幕](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>安装后的步骤
接下来，准备要配置为主目标服务器的计算机。

若要获取 Linux 虚拟机中每个 SCSI 硬盘的 ID，应该启用 **disk.EnableUUID = TRUE** 参数。

若要启用此参数，请使用以下步骤：

1. 关闭你的虚拟机。

2. 在左窗格中右键单击虚拟机对应的条目，然后选择“编辑设置”。

3. 单击“**选项**”选项卡。

4. 在左窗格中选择“高级”&gt;“常规”，然后单击右侧的“配置参数”。

    ![“选项”选项卡](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    当计算机正在运行时，“配置参数”选项不可用。 若要使此选项卡处于活动状态，请关闭虚拟机。

5. 查看是否存在包含 **disk.EnableUUID** 的行。

    - 如果该值存在并且设置为 **False**，请将它更改为 **True**（值不区分大小写）。

    - 如果该值存在并且设置为 **True**，请单击“取消”。

    - 如果该值不存在，请单击“添加行”。

    - 在“名称”列中添加 **disk.EnableUUID**，并将其值设置为 **TRUE**。

    ![检查 disk.EnableUUID 是否存在](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-additional-packages"></a>下载并安装其他包

> [!NOTE]
> 在下载并安装其他包之前，请确保已建立 Internet 连接。 如果未建立 Internet 连接，则需要手动找到并安装这些 RPM 包。

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

上述命令将从 CentOS 6.6 存储库下载并安装以下 15 个包。 如果无法访问 Internet，需要下载以下 RPM 包：


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
> 如果受源保护的计算机为根设备或引导设备使用了 ReiserFS 或 XFS 文件系统，则应该在保护之前，在 Linux 主目标中下载并安装以下附加包。


***ReiserFS（如果在 Suse11SP3 中使用。ReiserFS 不是 Suse11SP3 中的默认文件系统）***

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
在主目标上启用多路径包时需要此包。

### <a name="get-the-installer-for-setup"></a>获取安装程序

如果主目标已建立 Internet 连接，你可以使用以下步骤下载安装程序。 否则，可以从进程服务器复制该安装程序并执行安装。

#### <a name="download-the-master-target-installation-packages"></a>下载主目标安装包

[下载最新的 Linux 主目标安装程序](https://aka.ms/latestlinuxmobsvc)。

若要使用 Linux 下载该安装程序，请键入：

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

请务必将安装程序下载并解压缩到主目录。 如果解压缩到 /usr/Local，安装将会失败。


#### <a name="access-the-installer-from-the-process-server"></a>从进程服务器访问安装程序

1. 在进程服务器上转到 C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository。

2. 从进程服务器复制所需的安装程序文件，并在主目录中将它保存为 latestlinuxmobsvc.tar.gz。


### <a name="apply-custom-configuration-changes"></a>应用自定义配置更改

若要应用自定义配置更改，请使用以下步骤：


1. 运行以下命令解压缩二进制文件。
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![执行的命令的屏幕截图](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. 运行以下命令来指定权限。
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. 运行以下命令来运行该脚本。
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> 仅在服务器上运行该脚本一次。 关闭服务器。 根据后续步骤中所述添加磁盘后，重新启动服务器。

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>将保留磁盘添加到 Linux 主目标虚拟机

使用以下步骤创建保留磁盘：

1. 将新的 **1-TB** 磁盘附加到主目标虚拟机并**启动**计算机。

2. 使用 **multipath -ll** 命令来了解保留磁盘的多路径 ID。

    ```
    multipath -ll
    ```

    ![保留磁盘的多路径 ID](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. 格式化驱动器并在新驱动器上创建文件系统。

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![在驱动器上创建文件系统](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. 创建文件系统后，请装载保留磁盘。
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![装载保留磁盘](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. 创建每次启动期间用于装载保留驱动器的 **fstab** 项。
    ```
    vi /etc/fstab
    ```
    按 **Insert** 开始编辑文件。 创建新行并插入以下文本。 根据前一命令中突出显示的多路径 ID 编辑磁盘多路径 ID。

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    按 **Esc**，键入 **:wq**（写入并退出）关闭编辑器窗口。

### <a name="install-the-master-target"></a>安装主目标

> [!IMPORTANT]
> 主目标服务器的版本应该低于或等于进程服务器和配置服务器的版本。 如果不满足此条件，重新保护可以成功，但复制将会失败。


> [!NOTE]
> 安装主目标服务器之前，请检查虚拟机上的 /etc/hosts 文件是否包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。

1. 在配置服务器上从 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 复制通行短语，然后运行以下命令，将通行短语保存在同一本地目录中的 passphrase.txt 内。

    ```
    echo <passphrase> >passphrase.txt
    ```
    示例：echo itUx70I47uxDuUVY >passphrase.txt

2. 记下配置服务器的 IP 地址， 因为下一步骤需要用到。

3. 运行以下命令安装主目标服务器并将它注册到配置服务器。

    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    示例：./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    等到脚本执行完成。 如果成功注册主目标，门户中的“Site Recovery 基础结构”页上会列出该主目标。

#### <a name="install-the-master-target-by-using-interactive-install"></a>使用交互式安装过程安装主目标

1. 运行以下命令安装主目标。 选择“主目标”作为代理角色。

    ```
    ./install
    ```

2. 选择默认安装位置，然后按 **Enter** 继续。

    ![选择主目标的默认安装位置](./media/site-recovery-how-to-install-linux-master-target/image17.png)


3. 选择要配置的“全局”设置。

    ![配置全局设置](./media/site-recovery-how-to-install-linux-master-target/image18.png)

4. 指定配置服务器的 IP 地址。

5. 将配置服务器的端口指定为 443。

    ![指定配置服务器的 IP 地址和端口](./media/site-recovery-how-to-install-linux-master-target/image19.png)

6. 在配置服务器上从 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 复制配置服务器的通行短语，然后将它粘贴到“通行短语”框中。 即使在粘贴该文本之后，该框也会显示空白。

7. 在菜单中转到“Quit”。

8. 等待安装和注册完成。

### <a name="install-vmware-tools-on-the-master-target-server"></a>在主目标服务器上安装 VMware 工具

需将 VMware 工具安装在主目标上，以便能够发现数据存储。 如果不安装这些工具，重新保护屏幕中不会列出数据存储。 安装 VMware 工具后，需要重新启动计算机。

## <a name="next-steps"></a>后续步骤
主目标安装和注册完成后，可以看到该主目标显示在“Site Recovery 基础结构”中“主目标”部分的配置服务器概述下面。

现在，可以继续执行[重新保护](site-recovery-how-to-reprotect.md)过程，然后执行故障回复。

## <a name="common-issues"></a>常见问题

* 切勿在主目标等任何管理组件上打开存储 vMotion。 如果重新保护成功后移动主目标，将无法分离虚拟机磁盘 (VMDK)，并且故障回复会失败。
* 主目标不应在虚拟机上留下任何快照。 如果有快照，故障回复将会失败。
* 由于某些客户使用某些自定义 NIC 配置，使得网络接口在启动期间被禁用，因此，主目标代理无法初始化。 请确保正确设置以下属性。 在以太网卡文件 /etc/sysconfig/network-scripts/ifcfg-eth* 中检查这些属性。
    * BOOTPROTO=dhcp
    * ONBOOT=yes

