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
ms.date: 02/13/2017
ms.author: ruturajd
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c7c50c539149a929b15f50e4b52dc48d92534640
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>如何安装 Linux 主目标服务器
故障转移虚拟机后，可将虚拟机故障回复到本地站点。 若要故障回复，需要在本地站点中重新保护 Azure 中的虚拟机。 对于此过程，需要安装一个本地主目标服务器用于接收流量。 如果受保护的虚拟机是 Windows 虚拟机，则需要安装 Windows 主目标。 对于 Linux 虚拟机，需要安装 Linux 主目标。 请阅读以下步骤，了解如何创建和安装 Linux 主目标。

> [!IMPORTANT]
> 自 master 目标服务器版本 9.10.0 起，只能在 Ubuntu 16.04 服务器上安装最新的 master 目标服务器。 不允许在 CentOS6.6 服务器上进行新安装。 不过，可以继续使用 9.10.0 版本升级旧版 master 目标服务器。

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

**安装 Ubuntu 16.04.2 最简版**

请按照以下步骤操作，安装 Ubuntu 16.04.2 64 位操作系统。

**第 1 步：**单击以下链接，选择最接近的镜像来下载 Ubuntu 16.04.2 最简版 64 位 ISO

<https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64>

将 Ubuntu 16.04.2 最简版 64 位 ISO 保存在 DVD 驱动器中，再启动系统。

**第 2 步：**选择“英语”作为首选语言，再按 Enter。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)

**第 3 步：**选择“安装 Ubuntu Server”，再按 Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

**第 4 步：**选择“英语”作为首选语言，再按 Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

**第 5 步：**在“时区”选项列表中选择相应选项，再按 Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

**第 6 步：**选择默认选项“否”，再按 Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)

**第 7 步：**选择“英语(美国)”作为键盘原产地语言，再按 Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image6.png)

**第 8 步：**选择“英语(美国)”作为键盘布局，再按 Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image7.png)

**第 9 步：**在“主机名”文本框中输入服务器的主机名，再单击“继续”按钮

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image8.png)

**第 10 步：**在“文本框”中输入用于创建用户帐户的用户名，再单击“继续”按钮

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

**第 11 步：**在“文本框”中输入新用户帐户的密码，再单击“继续”按钮

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image10.png)

**第 12 步：**在“文本框”中确认新用户的密码，再单击“继续”按钮

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

**第 13 步：**选择默认选项“否”，再按 Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image12.png)

**第 14 步：**选择默认选项“是”（如果显示的时区正确无误），再按 Enter。

可以选择“否”选项来重新配置时区。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image13.png)

**第 15 步：**在分区方法选项列表中选择“引导式 - 使用整个磁盘”，再按 Enter。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

**第 16 步：**在“选择要分区的磁盘”选项列表中选择相应的磁盘，再按 Enter。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

**第 17 步：**选择“是”，将更改写入磁盘，再按 Enter。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image16.png)

**第 18 步：**选择默认选项，再选择“继续”按钮并按 Enter。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

**第 19 步：**选择相应选项来管理系统上的升级，再按 Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

> [!WARNING]
> 由于 Azure Site Recovery master 目标服务器需要非常具体的 Ubuntu 版本，因此必须确保已为虚拟机禁用内核升级。 如果启用，任意常规升级都会导致 master 目标服务器无法正常工作。 请务必选择“不自动更新”选项。


**第 20 步：**可以继续选择默认选项。 若要对 SSH 连接使用 openSSH，请依次选择“OpenSSH 服务器”选项和“继续”。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

**第 21 步：**选择选项“是”，再按 Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image20.png)

**第 22 步：**选择用于启动加载程序安装的适当设备（最好是 /dev/sda），再按 Enter。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image21.png)

**第 23 步：**选择“继续”按钮，再按 Enter，完成安装。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

安装完成后， 立即使用新用户凭据登录 VM（见第 10 步）。

按照下面屏幕截图中的步骤操作，设置根用户密码，并以根用户身份登录，以执行其他操作。

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


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

#### <a name="disable-kernel-upgrades"></a>**禁用内核升级**

由于 Azure Site Recovery master 目标服务器需要非常具体的 Ubuntu 版本，因此必须确保已为虚拟机禁用内核升级。 如果启用，任意常规升级都会导致 master 目标服务器无法正常工作。 请按照以下步骤操作，禁用内核升级。
> [!IMPORTANT]
> 需要在此处添加脚本步骤

#### <a name="download-and-install-additional-packages"></a>下载并安装其他包

> [!NOTE]
> 在下载并安装其他包之前，请确保已建立 Internet 连接。 如果未建立 Internet 连接，则需要手动找到并安装这些 RPM 包。

```
apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx
```

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
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    示例：/usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    等到脚本执行完成。 如果成功注册主目标，门户中的“Site Recovery 基础结构”页上会列出该主目标。


#### <a name="install-the-master-target-by-using-interactive-install"></a>使用交互式安装过程安装主目标

1. 运行以下命令安装主目标。 选择“主目标”作为代理角色。

    ```
    ./install
    ```

2. 选择默认安装位置，然后按 **Enter** 继续。

    ![选择主目标的默认安装位置](./media/site-recovery-how-to-install-linux-master-target/image17.png)

安装完成后，需要使用命令行注册配置服务器。

1. 记下配置服务器的 IP 地址， 因为下一步骤需要用到。

2. 运行以下命令安装主目标服务器并将它注册到配置服务器。

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    示例：/usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    等到脚本执行完成。 如果成功注册主目标，门户中的“Site Recovery 基础结构”页上会列出该主目标。


### <a name="upgrade-the-master-target"></a>升级 master 目标服务器

运行安装程序。 它会自动检测是否在 master 目标服务器上安装了代理。 选择“是”进行升级。 安装完成后，可以运行下列命令，检查安装的 master 目标服务器版本。

    ```
        cat /usr/local/.vx_version
    ```

“版本”字段中显示了 master 目标服务器版本号。

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

