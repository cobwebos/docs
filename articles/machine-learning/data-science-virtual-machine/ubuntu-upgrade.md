---
title: 如何将 Data Science Virtual Machine 升级到 Ubuntu 18.04
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何从 CentOS 和 Ubuntu 16.04 升级到最新的 Ubuntu 18.04 Data Science Virtual Machine。
keywords: 深度学习，AI，数据科学工具，数据科学虚拟机，团队数据科学过程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: d57de4d52ccf3a029a8dd1350635fb65dd3ac829
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828383"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>将 Data Science Virtual Machine 升级到 Ubuntu 18.04

如果你的 Data Science Virtual Machine 运行的是早期版本，如 Ubuntu 16.04 或 CentOS，则应将 DSVM 迁移到 Ubuntu 18.04。 迁移将确保你获取最新的操作系统修补程序、驱动程序、预安装软件和库版本。 本文档说明如何从 Ubuntu 或 CentOS 的早期版本进行迁移。 

## <a name="prerequisites"></a>必备条件

- 熟悉 SSH 和 Linux 命令行

## <a name="overview"></a>概述

可以通过两种方式迁移：

- 就地迁移，也称为 "相同服务器" 迁移。 此迁移会升级现有 VM，而不会创建新的虚拟机。 就地迁移是从 Ubuntu 16.04 迁移到 Ubuntu 18.04 的更简单方法。
- 并行迁移，也称为 "服务器间迁移"。 此迁移将数据从现有虚拟机传输到新创建的虚拟机。 并行迁移是从 Centos 迁移到 Ubuntu 18.04 的一种方式。 如果你认为旧安装已变得混乱混乱，你可能更倾向于并行迁移，以便在 Ubuntu 版本之间进行升级。

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>如果需要回滚，请为 VM 拍摄快照

在 Azure 门户中，使用搜索栏查找 **快照** 功能。 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="显示 Azure 门户和搜索栏的屏幕截图，其中突出显示了 * * 快照 * *&quot;:::

1. 选择 &quot; **添加**&quot;，这会转到 &quot; **创建快照** &quot; 页。 选择虚拟机的订阅和资源组。 对于 " **区域**"，请选择目标存储所在的同一区域。 选择 DSVM 存储磁盘和其他备份选项。 对于此备份方案，**标准 HDD**是合适的存储类型。

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="显示 Azure 门户和搜索栏的屏幕截图，其中突出显示了 * * 快照 * *&quot;:::

1. 选择 &quot; **添加**&quot;，这会转到 &quot; **创建快照** &quot; 页。 选择虚拟机的订阅和资源组。 对于 " 来验证和创建快照。 快照成功完成后，你将看到一条消息，告知你部署已完成。

## <a name="in-place-migration"></a>就地迁移

如果迁移的是较旧的 Ubuntu 版本，可以选择进行就地迁移。 此迁移不会创建新的虚拟机，并且执行的步骤少于并行迁移的步骤。  如果你想要进行并行迁移，因为你需要更多控制，或者是从不同的分发（如 CentOS）进行迁移，请跳到 [并行迁移](#side-by-side-migration) 部分。 

1. 在 Azure 门户中，使用 SSH 启动 DSVM 并登录。 为此，请选择 " **连接** " 和 " **SSH** "，然后按照连接说明进行操作。 

1. 连接到 DSVM 上的终端会话后，运行以下升级命令：

    ```bash
    sudo do-release-upgrade
    ```

升级过程需要一段时间才能完成。 结束时，程序将要求提供重新启动虚拟机的权限。 答案 **是**。 系统重新启动时，会断开 SSH 会话的连接。

### <a name="if-necessary-regenerate-ssh-keys"></a>如有必要，请重新生成 SSH 密钥

> [!IMPORTANT] 
> 升级和重新启动后，可能需要重新生成 SSH 密钥。

VM 升级并重新启动后，尝试通过 SSH 再次访问。 IP 地址可能在重新启动过程中发生了更改，因此请在尝试连接之前进行确认。

如果收到错误 " **远程主机标识已更改**"，则需要重新生成 SSH 凭据。

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="显示 Azure 门户和搜索栏的屏幕截图，其中突出显示了 * * 快照 * *&quot;:::

1. 选择 &quot; **添加**&quot;，这会转到 &quot; **创建快照** &quot; 页。 选择虚拟机的订阅和资源组。 对于 " 链接以 **解决 SSH 连接问题**。

## <a name="side-by-side-migration"></a>并行迁移

如果要从 CentOS 迁移或要进行干净的操作系统安装，可以进行并行迁移。 这种类型的迁移包含更多步骤，但使你可以精确控制要执行的文件。

从基于同一组上游源包的其他系统进行的迁移应相对简单明了，例如 [FAQ/CentOS3](https://wiki.centos.org/FAQ/CentOS3)。

您可以选择升级文件系统的操作系统部分，并保留用户目录，例如 `/home` 就地。 如果确实要将旧的用户主目录保留在一起，则会出现有关 GNOME/KDE 菜单和其他桌面项目的一些问题。 最简单的方法是在文件系统中创建新的用户帐户，并将旧目录装载到文件系统中的其他某个位置，以便在迁移后引用、复制或链接用户的资料。

### <a name="migration-at-a-glance"></a>快速迁移

1.  如前文所述，创建现有 VM 的快照

1.  从快照创建磁盘

1.  创建新的 Ubuntu Data Science Virtual Machine

1.  在新虚拟机上重新创建 () 的用户帐户

1.  在新的 Data Science Virtual Machine 上装载快照 VM 的磁盘作为数据磁盘

1.  手动复制所需的数据

### <a name="create-a-disk-from-your-vm-snapshot"></a>从 VM 快照创建磁盘

如果尚未创建 VM 快照，请执行此操作。 

1. 在 Azure 门户中，搜索 " **磁盘** " 并选择 " **添加**"，这将打开 " **磁盘** " 页。

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="显示 Azure 门户和搜索栏的屏幕截图，其中突出显示了 * * 快照 * *&quot;:::

1. 选择 &quot; **添加**&quot;，这会转到 &quot; **创建快照** &quot; 页。 选择虚拟机的订阅和资源组。 对于 " **快照** "，并选择 VM 快照作为 **源快照**。 查看并创建该磁盘。 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="显示 Azure 门户和搜索栏的屏幕截图，其中突出显示了 * * 快照 * *&quot;:::

1. 选择 &quot; **添加**&quot;，这会转到 &quot; **创建快照** &quot; 页。 选择虚拟机的订阅和资源组。 对于 ":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>创建新的 Ubuntu Data Science Virtual Machine

使用 [Azure 门户](https://portal.azure.com) 或 [ARM 模板](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-tutorial-resource-manager)创建新的 Ubuntu Data Science Virtual Machine。 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>) 新的 Data Science Virtual Machine 重新创建用户帐户 (

由于只需从旧计算机复制数据，因此需要重新创建要在新计算机上使用的任何用户帐户和软件环境。

Linux 的灵活性足以使您能够自定义新安装的目录和路径，以遵循您的旧计算机。 但一般情况下，使用新式 Ubuntu 的首选布局比修改用户环境和脚本更为容易。

有关详细信息，请参阅 [快速入门：设置 Linux (Ubuntu) 的 Data Science Virtual Machine ](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)。

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>在新的 Data Science Virtual Machine 上装载快照 VM 的磁盘作为数据磁盘

1. 在 Azure 门户中，确保 Data Science Virtual Machine 正在运行。

2. 在 Azure 门户中，请切换到 Data Science Virtual Machine 的页面。 选择左侧导轨上的 " **磁盘** " 边栏选项卡。 选择 " **附加现有磁盘**"。

3. 在 " **磁盘名称** " 下拉列表中，选择从旧 VM 的快照创建的磁盘。

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="显示 Azure 门户和搜索栏的屏幕截图，其中突出显示了 * * 快照 * *&quot;:::

1. 选择 &quot; **添加**&quot;，这会转到 &quot; **创建快照** &quot; 页。 选择虚拟机的订阅和资源组。 对于 " **X** "，启动 VM，然后重新附加它。

### <a name="manually-copy-the-wanted-data"></a>手动复制所需的数据 

1. 使用 SSH 登录到正在运行的虚拟机。

2. 通过运行以下命令，确认已从旧 VM 的快照中附加了磁盘：

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    结果应类似于下图所示。 在映像中，磁盘在 `sda1` 根目录下装载， `sdb2` 是 `/mnt` 暂存磁盘。 从旧 VM 的快照创建的数据磁盘被标识为 `sdc1` ，但尚未提供，因为在缺少装载位置时出现。 你的结果可能具有不同的标识符，但你应看到类似的模式。
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="显示 Azure 门户和搜索栏的屏幕截图，其中突出显示了 * * 快照 * *&quot;:::

1. 选择 &quot; **添加**&quot;，这会转到 &quot; **创建快照** &quot; 页。 选择虚拟机的订阅和资源组。 对于 ":::
    
3. 若要访问数据驱动器，请为其创建一个位置并装载它。 替换 `/dev/sdc1` 为返回的适当值 `lsblk` ：

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. 现在， `/datadrive` 包含旧 Data Science Virtual Machine 的目录和文件。 根据需要将所需的目录或文件从数据驱动器移动或复制到新 VM。

有关详细信息，请参阅 [使用门户将数据磁盘附加到 LINUX VM](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)。

## <a name="connect-and-confirm-version-upgrade"></a>连接并确认版本升级

无论是就地迁移还是并行迁移，确认是否已成功升级。 在终端会话中，运行： 

```bash
cat /etc/os-release
```

你应该会看到你正在运行 Ubuntu 18.04。

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="显示 Azure 门户和搜索栏的屏幕截图，其中突出显示了 * * 快照 * *&quot;:::

1. 选择 &quot; **添加**&quot;，这会转到 &quot; **创建快照** &quot; 页。 选择虚拟机的订阅和资源组。 对于 ":::

版本的更改也会显示在 Azure 门户中。

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="显示 Azure 门户和搜索栏的屏幕截图，其中突出显示了 * * 快照 * *&quot;:::

1. 选择 &quot; **添加**&quot;，这会转到 &quot; **创建快照** &quot; 页。 选择虚拟机的订阅和资源组。 对于 ":::

## <a name="next-steps"></a>后续步骤

- [使用 Azure 中的 Ubuntu 数据科学计算机进行数据科学](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/linux-dsvm-walkthrough)
- [哪些工具包含在 Azure Data Science Virtual Machine 中？](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/tools-included)
