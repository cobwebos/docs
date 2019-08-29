---
title: 在 Azure 中创建 Linux SQL Server 2017 VM | Microsoft Docs
description: 本教程介绍如何在 Azure 门户中创建 Linux SQL Server 2017 虚拟机。
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 12/5/2018
ms.topic: conceptual
tags: azure-service-management
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cd87477da15d5c18f94b66cac855672b4a2a3523
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091354"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>在 Azure 门户中预配 Linux SQL Server 虚拟机

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

在本快速入门教程中，你将使用 Azure 门户创建装有 SQL Server 2017 的 Linux 虚拟机。

本教程介绍如何执行下列操作：

* [从库创建 Linux SQL VM](#create)
* [使用 ssh 连接到新的 VM](#connect)
* [更改 SA 密码](#password)
* [针对远程连接进行配置](#remote)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a id="create"></a> 创建安装了 SQL Server 的 Linux VM

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在左窗格中，选择“创建资源”。

1. 在“创建资源”窗格中选择“计算”。

1. 选择“特色”标题旁边的“全部查看”。

   ![查看所有 VM 映像](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. 在搜索框中键入 **SQL Server 2017**，然后按 Enter 开始搜索。

1. 选择“操作系统” > “Redhat”来限制搜索结果。 然后，在“发行商”下面选择“Microsoft”。

    ![针对 SQL Server 2017 VM 映像的搜索筛选器](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. 从搜索结果中选择 SQL Server 2017 Linux 映像。 本教程使用“免费 SQL Server 许可证:SQL Server 2017 Developer on Red Hat Enterprise Linux 7.4”。

   > [!TIP]
   > Developer 版允许使用 Enterprise 版的功能进行测试或开发，但没有 SQL Server 许可费用。 只需支付运行 Linux VM 的费用。

1. 在“选择部署模型”下，选择适合工作负荷需求的部署模型。

    > [!Note]
    > 对于新工作负荷，请使用“资源管理器”。 若要连接到现有虚拟网络，请为工作负荷选择虚拟网络的部署方法。 有关部署模型的详细信息，请参阅 [Azure 资源管理器和经典部署模型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)。

1. 选择“创建”。

### <a name="set-up-your-linux-vm"></a>设置 Linux VM

1. 在“基本信息”选项卡中，选择自己的**订阅**和**资源组**。 

    ![“基本信息”窗口](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. 在“虚拟机名称”中，输入新 Linux VM 的名称。
1. 然后键入或选择以下值：
   * **区域**：选择合适的 Azure 区域。
   * **可用性选项**：选择最适合应用和数据的可用性与冗余选项。
   * **更改大小**：选择此选项以选择计算机大小，完成后，选择“选择”。 有关 VM 计算机大小的详细信息，请参阅 [Linux VM 大小](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes)。

     ![选择 VM 大小](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > 对于开发和功能测试，请使用 **DS2** 或更大的 VM 大小。 若要进行性能测试，则至少使用“DS13”。

   * **身份验证类型**：选择“SSH 公钥”。

     > [!Note]
     > 可以选择使用“SSH 公钥”或“密码”进行身份验证。 SSH 更安全。 有关如何生成 SSH 密钥的说明，请参阅[在 Linux 和 Mac 上为 Azure 中的 Linux VM 创建 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys)。

   * **用户名**：输入 VM 的管理员名称。
   * **SSH 公钥**：输入 RSA 公钥。
   * **公共入站端口**：选择“允许所选的端口”，然后在“选择公共入站端口”列表中选择“SSH (22)”端口。 在本快速入门中，必须执行此步骤才能建立连接并完成 SQL Server 配置。 如果想要远程连接到 SQL Server，还选择“MS SQL (1433)”以通过 Internet 打开端口 1433 进行连接。

   ![入站端口](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. 在后面的其他选项卡中对设置进行任何所需的更改，或保留默认设置。
    * **磁盘**
    * **网络**
    * **管理**
    * **来宾配置**
    * **标记**

1. 选择“查看 + 创建”。
1. 在“查看 + 创建”窗格中，选择“创建”。

## <a id="connect"></a> 连接到 Linux VM

如果已使用 BASH shell，请通过 ssh 命令连接到 Azure VM。 在以下命令中，替换连接到 Linux VM 所需的 VM 用户名和 IP 地址。

```bash
ssh azureadmin@40.55.55.555
```

可以在 Azure 门户中找到 VM 的 IP 地址。

![Azure 门户中的 IP 地址](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

如果在 Windows 上运行且没有 BASH shell，请安装 SSH 客户端，例如 PuTTY。

1. [下载并安装 PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. 运行 PuTTY。

1. 在 PuTTY 配置屏幕上，输入 VM 的公共 IP 地址。

1. 选择“打开”，并根据提示输入用户名和密码。

若要详细了解如何连接到 Linux VM，请参阅[使用门户在 Azure 上创建 Linux VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal)。

> [!Note]
> 如果出现有关不会在注册表中缓存服务器主机密钥的 PuTTY 安全警报，请从以下选项中进行选择。 如果你信任此主机，请选择“是”将密钥添加到 PuTTy 缓存并继续进行连接。 如果你只想建立连接一次，而无需将密钥添加到缓存，请选择“否”。 如果你不信任此主机，请选择“取消”以放弃连接。

## <a id="password"></a> 更改 SA 密码

新的虚拟机使用随机 SA 密码安装 SQL Server。 重置此密码，然后使用 SA 登录名连接到 SQL Server。

1. 连接到 Linux VM 以后，请打开新的命令终端。

1. 使用以下命令更改 SA 密码：

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   出现提示时，输入新的 SA 密码和密码确认。

1. 重新启动 SQL Server 服务。

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>将工具添加到路径（可选）

默认安装了多个 SQL Server [包](sql-server-linux-virtual-machines-overview.md#packages)，包括 SQL Server 命令行工具包。 工具包包含sqlcmd 和bcp 工具。 为了方便，可以选择将工具路径 `/opt/mssql-tools/bin/` 添加到PATH 环境变量。

1. 运行以下命令，修改登录会话和交互式/非登录会话的PATH：

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> 针对远程连接进行配置

如果需要远程连接到 Azure VM 上的 SQL Server，必须在网络安全组上配置入站规则。 该规则允许 SQL Server 所侦听的端口（默认为 1433）上的流量。 以下步骤演示如何使用此步骤所对应的 Azure 门户。

> [!TIP]
> 如果在预配过程中已在设置中选择了入站端口“MS SQL (1433)”，则已进行这些更改。 有关如何配置防火墙，可以转到下一部分。

1. 在门户中选择“虚拟机”，并选择 SQL Server VM。
1. 在左侧窗格中的“设置”下，选择“网络”。
1. 在“网络”窗口中，选择“入站端口规则”下的“添加入站端口”。

   ![入站端口规则](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. 在“服务”列表中，选择“MS SQL”。

    ![MS SQL 安全组规则](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. 单击“确定”保存 VM 的规则。

### <a name="open-the-firewall-on-rhel"></a>打开 RHEL 上的防火墙

本教程介绍了如何创建 Red Hat Enterprise Linux (RHEL) VM。 若要远程连接到 RHEL VM，则还需在 Linux 防火墙上打开端口 1433。

1. [连接](#connect)到 RHEL VM。

1. 在 BASH shell 中运行以下命令：

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>后续步骤

在 Azure 中有了 SQL Server 2017 虚拟机以后，即可使用 sqlcmd 进行本地连接，以便运行 Transact-SQL 查询。

如果已将 Azure VM 配置为建立远程 SQL Server 连接，则应可建立远程连接。 若要通过示例来了解如何从 Windows 远程连接到 Linux 上的 SQL Server，请参阅[使用 Windows 上的 SSMS 连接到 Linux 上的 SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms)。 若要通过 Visual Studio Code 进行连接，请参阅[使用 Visual Studio Code 创建和运行 SQL Server 的 Transact-SQL 脚本](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

有关 Linux 上的 SQL Server 的其他一般信息，请参阅 [Linux 上的 SQL Server 2017 概述](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)。 若要详细了解如何使用 SQL Server 2017 Linux 虚拟机，请参阅 [Azure 上的 SQL Server 2017 虚拟机概述](sql-server-linux-virtual-machines-overview.md)。
