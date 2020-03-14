---
title: 专用链接-Azure 门户-Azure Database for MariaDB
description: 了解如何从 Azure 门户配置 Azure Database for MariaDB 的专用链接
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 3f421cad64caf91b898bb1ec13dc909b93b7f72d
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370332"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>使用门户创建和管理 Azure Database for MariaDB 的专用链接

专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。  在本文中，你将了解如何使用 Azure 门户在 Azure 虚拟网络中创建 VM，并使用 Azure 私有终结点在 Azure Database for MariaDB 服务器中创建 VM。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 此功能适用于所有 Azure Database for MariaDB 支持常规用途和内存优化定价层的 Azure 区域。

## <a name="sign-in-to-azure"></a>登录 Azure
登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-azure-vm"></a>创建 Azure VM

在本部分中，你将创建虚拟网络和子网来托管用于访问专用链接资源的 VM （Azure 中的 MariaDB 服务器）。

### <a name="create-the-virtual-network"></a>创建虚拟网络
在本部分，你将创建虚拟网络和子网来托管用于访问专用链接资源的 VM。

1. 在屏幕的左上方，选择“创建资源” **“网络”** “虚拟网络” >  > 。
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

1. 在 Azure 门户屏幕的左上方，选择“创建资源” **“计算”** “虚拟机”。 >  > 

2. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 *myVm*。 |
    | 区域 | 选择“西欧”。 |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter”。 |
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

## <a name="create-an-azure-database-for-mariadb"></a>创建 Azure Database for MariaDB

在本部分中，将在 Azure 中创建 Azure Database for MariaDB 服务器。 

1. 在 Azure 门户屏幕的左上方，选择 "**创建资源** > **数据库** > " **Azure Database for MariaDB**"。

1. 在**Azure Database for MariaDB**提供以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | **服务器详细信息** |  |
    |服务器名称  | 输入 *myserver*。 如果此名称已被使用，请创建唯一的名称。|
    | 管理员用户名| 输入所选的管理员名称。 |
    | 密码 | 输入所选密码。 密码长度必须至少为 8 个字符，且符合定义的要求。 |
    | 位置 | 选择要在其中保存 MariaDB 服务器的 Azure 区域。 |
    |版本  | 选择所需的 MariaDB 服务器的数据库版本。|
    | 计算 + 存储| 根据工作负荷选择服务器需要的定价层。 |
    |||
 
7. 选择“确定”。 
8. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。 
9. 当看到 验证通过的 消息时，选择“创建”。 
10. 看到“验证通过”消息时选择“创建”。 

## <a name="create-a-private-endpoint"></a>创建专用终结点

在本部分中，你将创建 MariaDB 服务器到它的专用终结点。 

1. 在 Azure 门户屏幕的左上方，选择 "**创建资源** > **网络** > **专用链接**"。
2. 在“专用链接中心 - 概述”中的“与服务建立专用连接”选项的旁边，选择“启动”。

    ![专用链接概述](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. 在 "**创建专用终结点-基础知识**" 中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | 名称 | 输入“myPrivateEndpoint”。 如果此名称已被使用，请创建唯一的名称。 |
    |区域|选择“西欧”。|
    |||
5. 选择 "**下一步：资源**"。
6. 在“创建专用终结点 - 资源”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 选择“连接到我的目录中的 Azure 资源”。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择 " **DBforMariaDB/服务器**"。 |
    | 资源 |选择“myServer”|
    |目标子资源 |选择*mariadbServer*|
    |||
7. 选择**下一步：配置**。
8. 在 "**创建专用终结点-配置**" 中，输入或选择以下信息：

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

    ![已创建专用链接](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > "Customer DNS" 设置中的 FQDN 不会解析为配置的专用 IP。 需要为配置的 FQDN 设置 DNS 区域[，如下所示。](../dns/dns-operations-recordsets-portal.md)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>使用远程桌面 (RDP) 连接到 VM


创建 **myVm** 后，按如下所述从 Internet 连接到该 VM： 

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开。

1. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开 downloaded.rdp 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” **“使用其他帐户”，以指定在创建 VM 时输入的凭据** > 。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

1. VM 桌面出现后，将其最小化以返回到本地桌面。

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>从 VM 私下访问 MariaDB 服务器

1. 在  *myVM* 的远程桌面中打开 PowerShell。

2. 输入  `nslookup mydemomserver.privatelink.mariadb.database.azure.com`。 

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. 使用任何可用的客户端测试 MariaDB 服务器的专用链接连接。 在下面的示例中，我使用了[MySQL 工作台](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html)来执行该操作。


4. 在 "**新建连接**" 中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 服务器类型| 选择**MariaDB**。|
    | 服务器名称| 选择*mydemoserver.privatelink.mariadb.database.azure.com* |
    | 用户名 | 输入用户名作为在 MariaDB 服务器创建过程中提供的 username@servername。 |
    |密码 |输入在创建 MariaDB 服务器期间提供的密码。 |
    |SSL|选择 "**必需**"。|
    ||

5. 选择 "**测试连接** **" 或 "确定"** 。

6. 同时从左侧菜单浏览数据库，并从 MariaDB 数据库创建或查询信息

7. 关闭与 myVm 的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源
使用完专用终结点、MariaDB 服务器和 VM 后，请删除该资源组及其包含的所有资源：

1. 在门户顶部的“搜索”框中输入“myResourceGroup” **  ，然后从搜索结果中选择“myResourceGroup”。 **  
2. 选择“删除资源组”。
3. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本操作方法中，你在虚拟网络上创建了一个虚拟机、一个 Azure Database for MariaDB 和一个私有终结点用于专用访问。 你从 internet 连接到一个 VM，并使用专用链接安全地传达到 MariaDB 服务器。 若要了解有关专用终结点的详细信息，请参阅[什么是 Azure 专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)。
