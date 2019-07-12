---
title: 准备迁移到 Azure 中使用 Azure 迁移服务器迁移运行 Linux 的 VMware Vm |Microsoft Docs
description: 介绍如何准备迁移到具有 Azure 迁移服务器迁移的 Azure Linux VM
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811786"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>准备用于迁移到 Azure 的 Linux VMware Vm 

本文介绍如何准备时想要将它们迁移到 Azure 中使用运行 Linux 的 VMware Vm [Azure Migrate](migrate-overview.md)。 

> [!NOTE]
> Azure 迁移服务器迁移当前处于公共预览状态。 可以使用现有的通用版的 Azure Migrate 发现和评估 Vm 的迁移，但在实际迁移中现有的 GA 版本不受支持。

准备好 Linux 计算机，如下所示：

1. 安装的 HYPER-V Linux 集成服务。 较新版本的 Linux 分发版可能有此默认情况下安装）。
2. 重新生成 Linux init 映像，使其包含必要的 HYPER-V 驱动程序，并使 VM 将启动在 Azure （需要某些发行版） 中。
3. 启用串行控制台进行故障排除的日志记录。 [了解详细信息](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)。
4. 更新设备映射文件与卷关联，使用持续性的设备的标识符的设备名称。
5. 更新组的 fstab 条目使用永久性卷的标识符。
6. 删除根据 MAC 地址等的接口名称保留所有的 udev 规则。
7. 更新网络接口，用于接收 DHCP IP 地址。
8. 确保 ssh 已启用。 Sshd 服务设置为在重新启动时自动启动的检查。
9. 确保传入 ssh 连接请求不会阻止通过操作系统防火墙或 IP 表规则。

[了解详细信息](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)最受欢迎的 Linux 分发版上进行这些更改。

## <a name="sample-script"></a>示例脚本

此脚本 (准备-为-azure.sh) 用于准备 Linux 计算机提供的示例。 该脚本可能不适用于所有分布区和环境中，但它是一个有用的起始点。

此脚本显示如何： 

- 重新生成必要的驱动程序需要时使用的 Linux init 映像。
- 更新组的 fstab 条目使用永久性卷的标识符。
- 将控制台日志重定向到串行端口。
- 启用 Azure 串行访问控制台
- 删除网络的 udev 规则
- 注入 VM 启动时运行的启动脚本运行。 它会检查计算机是否正在 Azure 中运行。 如果是，它更新在 VM 上的网络配置，并设置为使用 DHCP 获取 IP 地址的第一个以太网接口。

### <a name="before-you-start"></a>开始之前

- 示例脚本中包含的示例步骤。 它不应在生产系统上运行。 它可能损坏或破坏其运行所在的 VM。
- 我们建议运行测试虚拟机。 在开始之前，需要的 VM 备份或快照，以便根据需要可以还原 VM。 
- 脚本的工作时 VM 正在运行这些 Linux 分发版之一：
    - Red Hat Enterprise Linux 6.5 + 7.1 +
    - %OS 6.5 + 7.1 +
    - SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3
    - Ubuntu 14.04、 16.04、 18.04
    - Debian 7、 8

### <a name="run-the-script"></a>运行脚本

1. 复制到 Linux 脚本测试使用 sftp 或如 FileZilla 或 WinScp scp 客户端 VM。
2. 通过 ssh 登录到 Linux 计算机使用管理员帐户。
3. 导航到脚本目录。
4. 若要为可执行文件使该脚本，运行**sudo chmod 777 准备-为-azure.sh**。
5. 运行脚本时使用 **./prepare-for-azure.sh**。

下面是脚本的运行方式：

![Linux 脚本](./media/how-to-prepare-linux-for-migration/script1.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script2.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script3.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script5.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Linux 脚本](./media/how-to-prepare-linux-for-migration/script7.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script8.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script10.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Linux 脚本](./media/how-to-prepare-linux-for-migration/script12.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script13.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script15.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Linux 脚本](./media/how-to-prepare-linux-for-migration/script17.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script18.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script20.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Linux 脚本](./media/how-to-prepare-linux-for-migration/script22.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script23.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script24.png)
![Linux 脚本](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>后续步骤

- 了解如何使用[计算机依赖项映射](how-to-create-group-machine-dependencies.md)创建高可信度组。
- [详细了解](concepts-assessment-calculation.md)如何计算评估。
