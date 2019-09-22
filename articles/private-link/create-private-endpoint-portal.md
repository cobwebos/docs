---
title: 管理 Azure 中的专用终结点
description: 了解如何使用 Azure 门户创建专用终结点
services: private-link
author: KumudD
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 2b7ad95dc652f2099af75308f05ddd0684e088aa
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104732"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>使用 Azure 门户创建专用终结点

专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。 本快速入门介绍如何使用 Azure PowerShell 在 Azure 虚拟网络中创建一个 VM，以及一个包含 Azure 专用终结点的 SQL 数据库服务器。 然后，你可以从该 VM 安全访问该 SQL 数据库服务器。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-vm"></a>创建 VM
在本部分，你将创建虚拟网络和子网来托管用于访问专用链接资源（在本示例中为 Azure 中的某个 SQL 服务器）的 VM。

### <a name="create-the-virtual-network"></a>创建虚拟网络


在本部分，你将创建虚拟网络和子网来托管用于访问专用链接资源的 VM。

1. 在屏幕的左上方，选择“创建资源” > “网络” > “虚拟网络”    。
1. 在“创建虚拟网络”  中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | Name | 输入 *MyVirtualNetwork*。 |
    | 地址空间 | 输入 10.1.0.0/16  。 |
    | Subscription | 选择订阅。|
    | Resource group | 选择“新建”，输入 myResourceGroup，然后选择“确定”    。 |
    | 位置 | 选择“WestCentralUS”。 |
    | 子网 - 名称 | 输入 *mySubnet*。 |
    | 子网 - 地址范围 | 输入 10.1.0.0/24  。 |
    |||
1. 将其余的设置保留默认值，然后选择“创建”  。


### <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “计算” > “虚拟机”。   

1. 在“创建虚拟机 - 基本信息”  中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | Subscription | 选择订阅。 |
    | Resource group | 选择“myResourceGroup”。  已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 *myVm*。 |
    | 区域 | 选择“WestCentralUS”。  |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”  。 |
    | 映像 | 选择“Windows Server 2019 Datacenter”。  |
    | 大小 | 保留默认值“标准 DS1 v2”  。 |
    | **管理员帐户** |  |
    | 用户名 | 输入所选用户名。 |
    | 密码 | 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |  |
    | 公共入站端口 | 保留默认值“无”  。 |
    | **节省资金** |  |
    | 已有 Windows 许可证？ | 保留默认值“否”  。 |
    |||

1. 在完成时选择“下一步:**磁盘”** 。

1. 在“创建虚拟机 - 磁盘”中保留默认值，然后选择“下一步:  **网络”** 。

1. 在“创建虚拟机 - 基本信息”  中，选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 虚拟网络 | 保留默认值“MyVirtualNetwork”  。  |
    | 地址空间 | 保留默认值“10.1.0.0/24”。 |
    | 子网 | 保留默认值“mySubnet (10.1.0.0/24)”。 |
    | 公共 IP | 保留默认值“(new) myVm-ip”  。 |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 选择“HTTP”和“RDP”。  |
    |||


1. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置  。

1. 看到“验证通过”消息时，选择“创建”   。

## <a name="create-a-sql-database-server"></a>创建 SQL 数据库服务器
在本部分，你将在 Azure 中创建一个 SQL 数据库服务器。 

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “数据库” > “SQL 数据库”。   

1. 在“创建 SQL 数据库 - 基本信息”中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    | **数据库详细信息** | |
    | Subscription | 选择订阅。 |
    | Resource group | 选择“myResourceGroup”。  已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | 数据库名称  | 输入 *mydatabase*。 如果此名称已被使用，请创建唯一的名称。 |
    |||
5. 在“服务器”中选择“新建”。 ****  
6. 在“新建服务器”中，输入或选择以下信息 **** ：

    | 设置 | 值 |
    | ------- | ----- |
    |服务器名称  | 输入 *myserver*。 如果此名称已被使用，请创建唯一的名称。|
    | 服务器管理员登录名| 输入所选的管理员名称。 |
    | 密码 | 输入所选密码。 密码长度必须至少为 8 个字符，且符合定义的要求。 |
    | 位置 | 选择要将 SQL 服务器放到的 Azure 区域。 |
    
7. 选择“确定”。 **** 
8. 选择“查看 + 创建”。 **** 随后你会转到“查看 + 创建”页，Azure 将在其中验证配置 **** 。 
9. 看到“验证通过”消息时，选择“创建” **** 。 
10. 看到“验证通过”消息时选择“创建”。 

## <a name="create-a-private-endpoint"></a>创建专用终结点

在本部分，你将创建一个 SQL 服务器并在其中添加专用终结点。 

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “网络” > “专用链接中心(预览版)”。   
2. 在“专用链接中心 - 概述”中的“与服务建立专用连接”选项的旁边，选择“启动”。 ****  ****  ****
1. 在“创建专用终结点(预览版) - 基本信息”中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | Subscription | 选择订阅。 |
    | Resource group | 选择“myResourceGroup”。  已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | Name | 输入 * myPrivateEndpoint*。 如果此名称已被使用，请创建唯一的名称。 |
    |区域|选择“WestCentralUS”。 |
    |||
5. 选择“下一步: **** 资源”。
6. 在“创建专用终结点 - 资源”中，输入或选择以下信息： ****

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 选择“连接到我的目录中的 Azure 资源”。|
    | Subscription| 选择订阅。 |
    | 资源类型 | 选择“Microsoft.Sql/servers”。 **** |
    | 资源 |选择“myServer” |
    |目标子资源 |选择“sqlServer” |
    |||
7. 选择“下一步: **** 配置”。
8. 在“创建专用终结点(预览版) - 配置”中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    |**网络**| |
    | 虚拟网络| 选择“MyVirtualNetwork”。  |
    | 子网 | 选择“mySubnet”。 ** |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |选择“是”。 **** |
    |专用 DNS 区域 |选择“(New)privatelink.database.windows.net”  |
    |||

1. 选择“查看 + 创建”。 **** 随后你会转到“查看 + 创建”页，Azure 将在其中验证配置 **** 。 
2. 看到“验证通过”消息时，选择“创建”。 ****   **** 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>使用远程桌面 (RDP) 连接到 VM


创建 **myVm* 后，按如下所述从 Internet 连接到该 VM： 

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。  选择“连接”按钮后，“连接到虚拟机”随即打开   。

1. 选择“下载 RDP 文件”  。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开下载的 .rdp* 文件。

    1. 出现提示时，选择“连接”  。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据   。

1. 选择“确定”  。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”   。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>以私密方式从 VM 访问 SQL 数据库服务器

1. 在  *myVM* 的远程桌面中打开 PowerShell。
2. 输入  `nslookup myserver.database.windows.net`。 

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **Database Engine**.|
    | Server name| Select *myserver.database.windows.net* |
    | User name | Enter a password provided during the SQL server creation. |
    |Password |Enter a password provided during the SQL server creation. |
    |Remember password|Select **Yes**.|
    |||
1. Select **Connect**.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## Clean up resources 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).

