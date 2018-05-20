---
title: 在 Azure 中创建 Linux SQL Server 2017 VM | Microsoft Docs
description: 本教程介绍如何在 Azure 门户中创建 Linux SQL Server 2017 虚拟机。
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 05/11/2018
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: b86dd47c112c38bc65c045158787d19b470899a0
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>在 Azure 门户中预配 Linux SQL Server 虚拟机

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

在本快速入门教程中，请使用 Azure 门户创建安装了 SQL Server 2017 的 Linux 虚拟机。

在本教程中，将：

* [从库创建 Linux SQL VM](#create)
* [使用 ssh 连接到新的 VM](#connect)
* [更改 SA 密码](#password)
* [针对远程连接进行配置](#remote)

## <a name="prerequisites"></a>先决条件

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free)。

## <a id="create"></a> 创建安装了 SQL Server 的 Linux VM

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在左窗格中单击“创建资源”。

1. 在“创建资源”窗格中单击“计算”。

1. 单击“特色”标题旁边的“全部查看”。

   ![查看所有 VM 映像](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. 在搜索框中键入“SQL Server 2017”，然后按 Enter 开始搜索。

1. 单击“筛选器”图标，将搜索限制为“基于 Linux”的 Microsoft 映像，然后单击“完成”。

    ![针对 SQL Server 2017 VM 映像的搜索筛选器](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. 从搜索结果中选择 SQL Server 2017 Linux 映像。 本教程使用“免费 SQL Server 许可证: SQL Server 2017 Developer on Red Hat Enterprise Linux 7.4”。

   > [!TIP]
   > Developer 版允许你使用 Enterprise 版的功能进行测试或开发，但没有 SQL Server 许可费用。 只需支付运行 Linux VM 的费用。

1. 单击“创建”。

1. 在“基本信息”窗口中，填写 Linux VM 的详细信息。 

    ![“基本信息”窗口](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > 可以选择使用“SSH 公钥”或“密码”进行身份验证。 SSH 更安全。 有关如何生成 SSH 密钥的说明，请参阅[在 Linux 和 Mac 上为 Azure 中的 Linux VM 创建 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys)。

1. 单击“确定”。

1. 在“大小”窗口中，选择计算机大小。 有关 VM 计算机大小的详细信息，请参阅 [Linux VM 大小](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes)。

    ![选择 VM 大小](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > 若要进行开发和功能测试，建议至少使用“DS2”VM 大小。 若要进行性能测试，则至少使用“DS13”。

1. 单击“选择”。

1. 在“设置”窗口中，从“选择公共入站端口”列表中选择“SSH (22)”端口。 这是在本快速入门中连接并完成 SQL Server 配置所必需的。 如果想要远程连接到 SQL Server，还选择“MS SQL (1433)”以通过 Internet 打开端口 1433 进行连接。

   ![入站端口](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. 可以对其他设置进行更改，也可以保留默认设置。 然后单击“确定”。

1. 在“摘要”页中，单击“购买”以创建 VM。

## <a id="connect"></a> 连接到 Linux VM

如果已使用 BASH shell，请通过 ssh 命令连接到 Azure VM。 在以下命令中，替换连接到 Linux VM 所需的 VM 用户名和 IP 地址。

```bash
ssh azureadmin@40.55.55.555
```

可以在 Azure 门户中找到 VM 的 IP 地址。

![Azure 门户中的 IP 地址](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

如果是在 Windows 上运行且没有 BASH shell，则可安装 SSH 客户端，例如 PuTTY。

1. [下载并安装 PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. 运行 PuTTY。

1. 在 PuTTY 配置屏幕上，输入 VM 的公共 IP 地址。

1. 单击“打开”，在系统提示时输入用户名和密码。

若要详细了解如何连接到 Linux VM，请参阅[使用门户在 Azure 上创建 Linux VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm)。

## <a id="password"></a> 更改 SA 密码

新的虚拟机使用随机 SA 密码安装 SQL Server。 必须先重置该密码，然后才能使用 SA 登录名连接到 SQL Server。

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

默认安装了多个 SQL Server [包](sql-server-linux-virtual-machines-overview.md#packages)，包括 SQL Server 命令行工具包。 工具包包含 sqlcmd 和 bcp 工具。 为了方便，可以选择将工具路径 `/opt/mssql-tools/bin/` 添加到 PATH 环境变量。

1. 运行以下命令，修改登录会话和交互式/非登录会话的 PATH：

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

1. 在属性列表中，选择“网络”。

1. 在“网络”窗口中，单击“入站端口规则”下的“添加”按钮。

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

如果已将 Azure VM 配置为进行远程 SQL Server 连接，则还应能够进行远程连接。 若要通过示例来了解如何从 Windows 远程连接到 Linux 上的 SQL Server，请参阅[使用 Windows 上的 SSMS 连接到 Linux 上的 SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms)。 若要通过 Visual Studio Code 进行连接，请参阅[使用 Visual Studio Code 创建和运行 SQL Server 的 Transact-SQL 脚本](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

有关 Linux 上的 SQL Server 的更多常规信息，请参阅 [Linux 上的 SQL Server 2017 概述](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)。 若要详细了解如何使用 SQL Server 2017 Linux 虚拟机，请参阅 [Azure 上的 SQL Server 2017 虚拟机概述](sql-server-linux-virtual-machines-overview.md)。
