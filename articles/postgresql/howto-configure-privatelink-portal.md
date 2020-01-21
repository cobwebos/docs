---
title: Azure Database for PostgreSQL-单一服务器（预览版）门户安装方法的专用链接
description: 了解如何从 Azure 门户配置 Azure Database for PostgreSQL 单服务器的专用链接
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: f7a796408267fda08d765425a3c529895a251782
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281098"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-preview-using-portal"></a>使用门户创建和管理 Azure Database for PostgreSQL 单一服务器（预览版）的专用链接

专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。  在本文中，你将了解如何使用 Azure 门户在 Azure 虚拟网络中创建 VM 和使用 Azure 专用终结点 Azure Database for PostgreSQL 单一服务器。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 此功能在所有 Azure Database for PostgreSQL 单一服务器支持常规用途和内存优化定价层的 Azure 区域中均可用。

## <a name="sign-in-to-azure"></a>登录 Azure
登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-azure-vm"></a>创建 Azure VM

在本部分中，你将创建虚拟网络和子网来托管用于访问专用链接资源的 VM （Azure 中的 PostgreSQL 服务器）。

### <a name="create-the-virtual-network"></a>创建虚拟网络
在本部分，你将创建虚拟网络和子网来托管用于访问专用链接资源的 VM。

1. 在屏幕的左上方，选择“创建资源” > “网络” > “虚拟网络”。
2. 在“创建虚拟网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 *MyVirtualNetwork*。 |
    | 地址空间 | 输入 10.1.0.0/16。 |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”，输入 myResourceGroup，然后选择“确定”。 |
    | 位置 | 选择“西欧”。|
    | 子网 - 名称 | 输入 *mySubnet*。 |
    | 子网 - 地址范围 | 输入 10.1.0.0/24。 |
    |||
3. 将其余的设置保留默认值，然后选择“创建”。

### <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “计算” > “虚拟机”。

2. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 *myVm*。 |
    | 地区 | 选择“西欧”。 |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”。 |
    | 图像 | 选择“Windows Server 2019 Datacenter”。 |
    | 大小 | 保留默认值“标准 DS1 v2”。 |
    | **管理员帐户** |  |
    | 用户名 | 输入所选用户名。 |
    | 密码 | 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |  |
    | 公共入站端口 | 保留默认值“无”。 |
    | **节省资金** |  |
    | 已有 Windows 许可证？ | 保留默认值“否”。 |
    |||

1. 选择 "**下一步：磁盘**"。

1. 在 "**创建虚拟机-磁盘**" 中，保留默认值并选择 "**下一步：网络**"。

1. 在“创建虚拟机 - 基本信息”中，选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 虚拟网络 | 保留默认值“MyVirtualNetwork”。  |
    | 地址空间 | 保留默认值“10.1.0.0/24”。|
    | 子网 | 保留默认值“mySubnet (10.1.0.0/24)”。|
    | 公共 IP | 保留默认值“(new) myVm-ip”。 |
    | 公共入站端口 | 选择“允许所选端口”。 |
    | 选择入站端口 | 选择“HTTP”和“RDP”。|
    |||


1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

1. 看到“验证通过”消息时，选择“创建”。

## <a name="create-an-azure-database-for-postgresql-single-server"></a>创建 Azure Database for PostgreSQL 单一服务器

在本部分中，将在 Azure 中创建 Azure Database for PostgreSQL 服务器。 

1. 在 Azure 门户屏幕的左上方，选择 "**创建资源** > **数据库** > " **Azure Database for PostgreSQL**"。

1. 在**Azure Database for PostgreSQL 部署 "选项**中，选择"**单一服务器**"并提供以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | **服务器详细信息** |  |
    |服务器名称  | 输入 *myserver*。 如果此名称已被使用，请创建唯一的名称。|
    | 管理员用户名| 输入所选的管理员名称。 |
    | 密码 | 输入所选密码。 密码长度必须至少为 8 个字符，且符合定义的要求。 |
    | 位置 | 选择要在其中保存 PostgreSQL 服务器的 Azure 区域。 |
    |版本  | 选择所需的 PostgreSQL 服务器的数据库版本。|
    | 计算 + 存储| 根据工作负荷选择服务器需要的定价层。 |
    |||
 
7. 选择“确定”。 
8. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。 
9. 当看到 验证通过的 消息时，选择“创建”。 
10. 看到“验证通过”消息时选择“创建”。 

## <a name="create-a-private-endpoint"></a>创建专用终结点

在本部分中，你将创建一个 PostgreSQL 服务器，并向其添加专用终结点。 

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “网络” > “专用链接中心(预览版)”。
2. 在“专用链接中心 - 概述”中的“与服务建立专用连接”选项的旁边，选择“启动”。

    ![专用链接概述](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. 在“创建专用终结点(预览版) - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | 名称 | 输入“myPrivateEndpoint”。 如果此名称已被使用，请创建唯一的名称。 |
    |地区|选择“西欧”。|
    |||
5. 选择 "**下一步：资源**"。
6. 在“创建专用终结点 - 资源”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 选择“连接到我的目录中的 Azure 资源”。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择 " **DBforPostgreSQL/服务器**"。 |
    | 资源 |选择“myServer”|
    |目标子资源 |选择*postgresqlServer*|
    |||
7. 选择**下一步：配置**。
8. 在“创建专用终结点(预览版) - 配置”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |**网络**| |
    | 虚拟网络| 选择“MyVirtualNetwork”。 |
    | 子网 | 选择“mySubnet”。 ** |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。 |
    |专用 DNS 区域 |选择 *（新的） privatelink* |
    |||

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。 
2. 看到“验证通过”消息时，选择“创建”。 

    ![已创建专用链接](media/concepts-data-access-and-security-private-link/show-postgres-private-link.png)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>使用远程桌面 (RDP) 连接到 VM


创建 **myVm** 后，按如下所述从 Internet 连接到该 VM： 

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开。

1. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开 downloaded.rdp 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

1. VM 桌面出现后，将其最小化以返回到本地桌面。

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>从 VM 私下访问 PostgreSQL 服务器

1. 在  *myVM* 的远程桌面中打开 PowerShell。

2. 输入  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`。 

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the PostgreSQL server using any available client. In the example below I have used [Azure Data studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) to do the operation.

4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **PostgreSQL**.|
    | Server name| Select *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | User name | Enter username as username@servername which is provided during the PostgreSQL server creation. |
    |Password |Enter a password provided during the PostgreSQL server creation. |
    |SSL|Select **Required**.|
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the postgreSQL server.

8. Close the remote desktop connection to myVm.

## Clean up resources
When you're done using the private endpoint, PostgreSQL server, and the VM, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results.
2. Select **Delete resource group**.
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this how-to, you created a VM on a virtual network, an Azure Database for PostgreSQL - Single server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the PostgreSQL server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
