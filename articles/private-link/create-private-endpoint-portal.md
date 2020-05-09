---
title: 快速入门 - 管理 Azure 中的专用终结点
description: 在本快速入门中了解如何使用 Azure 门户创建专用终结点
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1bdc089bb89a732e329bf7d3ffd3d5b5c09ba408
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80637239"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>快速入门：使用 Azure 门户创建专用终结点

专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。 本快速入门介绍如何使用 Azure 门户在 Azure 虚拟网络中创建一个 VM，以及一个包含 Azure 专用终结点的 SQL 数据库服务器。 然后，可以从该 VM 安全访问该 SQL 数据库服务器。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-vm"></a>创建 VM
在本部分，你将创建虚拟网络和子网来托管用于访问专用链接资源（在本示例中为 Azure 中的某个 SQL 服务器）的 VM。

## <a name="virtual-network-and-parameters"></a>虚拟网络和参数

在本部分，你将创建虚拟网络和子网来托管用于访问专用链接资源的 VM。

在本部分中，你需要将步骤中的以下参数替换为以下信息：

| 参数                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | 美国中西部    |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “计算” > “虚拟机”。   

1. 在“创建虚拟机 - 基本信息”  中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。  已在上一部分创建此内容。  |
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
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。  已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | 数据库名称  | 输入 *mydatabase*。 如果此名称已被使用，请创建唯一的名称。 |
    |||
5. 在“服务器”  中，选择“新建”  。 
6. 在“新建服务器”中，输入或选择以下信息  ：

    | 设置 | 值 |
    | ------- | ----- |
    |服务器名称  | 输入 *myserver*。 如果此名称已被使用，请创建唯一的名称。|
    | 服务器管理员登录名| 输入所选的管理员名称。 |
    | 密码 | 输入所选密码。 密码长度必须至少为 8 个字符，且符合定义的要求。 |
    | 位置 | 选择要将 SQL 服务器放到的 Azure 区域。 |
    
7. 选择“确定”  。 
8. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置  。 
9. 当看到“验证通过”消息时，选择“创建”  。 
10. 当看到“验证通过”消息时，选择“创建”。 

## <a name="create-a-private-endpoint"></a>创建专用终结点

在本部分，你将创建一个 SQL 服务器并在其中添加专用终结点。 

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “网络” > “专用链接中心(预览版)”。   
2. 在“专用链接中心 - 概述”中的“与服务建立专用连接”选项的旁边，选择“启动”。   
1. 在“创建专用终结点(预览版) - 基本信息”中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。  已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | 名称 | 输入“myPrivateEndpoint”  。 如果此名称已被使用，请创建唯一的名称。 |
    |区域|选择“WestCentralUS”。 |
    |||
5. 在完成时选择“下一步:  资源”。
6. 在“创建专用终结点 - 资源”中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 选择“连接到我的目录中的 Azure 资源”。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择“Microsoft.Sql/servers”  。 |
    | 资源 |选择“myServer” |
    |目标子资源 |选择“sqlServer” |
    |||
7. 在完成时选择“下一步:  配置”。
8. 在“创建专用终结点(预览版) - 配置”中，输入或选择以下信息： 

    | 设置 | 值 |
    | ------- | ----- |
    |**网络**| |
    | 虚拟网络| 选择“MyVirtualNetwork”。  |
    | 子网 | 选择“mySubnet”  。 |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。  |
    |专用 DNS 区域 |选择“(New)privatelink.database.windows.net”  |
    |||

1. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置  。 
2. 看到“验证通过”消息时，选择“创建”   。 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>使用远程桌面 (RDP) 连接到 VM


创建 **myVm** 后，按如下所述从 Internet 连接到该 VM： 

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。  选择“连接”按钮后，“连接到虚拟机”随即打开   。

1. 选择“下载 RDP 文件”  。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开 downloaded.rdp  文件。

    1. 出现提示时，选择“连接”  。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据   。

1. 选择“确定”  。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”   。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>以私密方式从 VM 访问 SQL 数据库服务器

1. 在 *myVM* 的远程桌面中，打开 PowerShell。

2. 输入 `nslookup myserver.database.windows.net`。 

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. 安装 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)。

4. 在“连接服务器”中，输入或选择以下信息  ：

    | 设置 | 值 |
    | ------- | ----- |
    | 服务器类型| 选择“数据库引擎”。 |
    | 服务器名称| 选择“myserver.database.windows.net”。  |
    | 用户名 | 以 username@servername 形式输入用户名，这是在 SQL Server 创建过程中提供的。 |
    |密码 |输入创建 SQL 服务器期间提供的密码。 |
    |记住密码|请选择“是”。 |
    |||
1. 选择“连接”  。
2. 浏览左侧菜单中的数据库。
3. （可选）创建或查询 mydatabase 中的信息。
4. 关闭与 *myVm* 的远程桌面连接。 

## <a name="clean-up-resources"></a>清理资源 
用完专用终结点、SQL 服务器和 VM 之后，请删除资源组及其包含的所有资源： 
1. 在门户顶部的“搜索”框中输入“myResourceGroup”，并从搜索结果中选择“myResourceGroup”    。 
2. 选择“删除资源组”  。 
3. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除”   。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已在虚拟网络中创建一个 VM，并创建了一个 SQL 数据库服务器，以及一个用于私密访问的专用终结点。 你已从 Internet 连接到一个 VM，并使用专用链接安全地与 SQL 数据库服务器通信。 若要详细了解专用终结点，请参阅[什么是 Azure 专用终结点？](private-endpoint-overview.md)。
