---
title: 使用 Azure 专用终结点将专用连接到存储帐户
description: 了解如何使用专用终结点将专用连接到 Azure 中的存储帐户。
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: bb0c6e9d20c12df3532a52df1fe4d9574344d4b3
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104715"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>使用 Azure 专用终结点将专用连接到存储帐户
Azure 专用终结点是 Azure 中专用链接的基本构建基块。 它使 Azure 资源（例如虚拟机）能够与专用链接资源进行私下通信。

在本快速入门中，你将了解如何在 Azure 虚拟网络上创建 VM，这是一个具有使用 Azure 门户的专用终结点的存储帐户。 然后，可以从 VM 安全地访问存储帐户。


## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-vm"></a>创建 VM
在本部分中，你将创建虚拟网络和子网来托管用于访问专用链接资源的 VM （本示例中为存储帐户）。

### <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分中，你将创建虚拟网络和子网来托管用于访问专用链接资源的 VM。

1. 在屏幕的左上方，选择“创建资源” > “网络” > “虚拟网络”。
1. 在“创建虚拟网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 姓名 | 输入*MyVirtualNetwork*。 |
    | 地址空间 | 输入 10.1.0.0/16。 |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”，输入 myResourceGroup，然后选择“确定”。 |
    | Location | 选择**WestCentralUS**。|
    | 子网 - 名称 | 输入*mySubnet*。 |
    | 子网 - 地址范围 | 输入 10.1.0.0/24。 |
    |||
1. 将其余的设置保留默认值，然后选择“创建”。


### <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择 "**创建资源** > " "**计算** > " "**虚拟机**"。

1. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

    | 设置 | ReplTest1 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入*myVm*。 |
    | 地区 | 选择**WestCentralUS**。 |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”。 |
    | 图像 | 选择**Windows Server 2019 Datacenter**。 |
    | Size | 保留默认值“标准 DS1 v2”。 |
    | **管理员帐户** |  |
    | 用户名 | 输入所选用户名。 |
    | 密码 | 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |  |
    | 公共入站端口 | 保留默认值“无”。 |
    | **节省资金** |  |
    | 已有 Windows 许可证？ | 保留默认值“否”。 |
    |||

1. 在完成时选择“下一步:**磁盘”** 。

1. 在 "**创建虚拟机-磁盘**" 中，保留默认值**并选择 "下一步"：网络”** 。

1. 在“创建虚拟机 - 基本信息”中，选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 虚拟网络 | 保留默认**MyVirtualNetwork**。  |
    | 地址空间 | 保留默认的 " **10.1.0.0/24**"。|
    | Subnet | 保留默认**mySubnet （10.1.0.0/24）** 。|
    | 公共 IP | 保留默认值“(new) myVm-ip”。 |
    | 公共入站端口 | 选择“允许所选端口”。 |
    | 选择入站端口 | 选择“HTTP”和“RDP”。|
    ||

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

1. 看到“验证通过”消息时，选择“创建”。

## <a name="create-your-private-endpoint"></a>创建专用终结点
在本部分中，你将使用专用终结点创建一个专用存储帐户。 

1. 在 Azure 门户屏幕的左上方，选择 "**创建资源** > " "**存储** > " "存储**帐户**"。

1. 在 "**创建存储帐户-基础知识**" 中，输入或选择以下信息：

    | 设置 | ReplTest1 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | 存储帐户名称  | 输入*mystorageaccount*。 如果此名称已被使用，请创建唯一的名称。 |
    | 地区 | 选择**WestCentralUS**。 |
    | 性能| 保留默认值“标准”。 |
    | 帐户类型 | 保留默认**存储（常规用途 v2）** 。 |
    | 复制 | 选择 "**读取访问异地冗余存储（GRS）** "。 |
    |||
  
3. 选择 **下一步：** 网络”。
4. 在 " **创建存储帐户-网络**、连接方法" 中，选择 "**专用终结点**"。
5. 在 " **创建存储帐户-网络**" 中，选择 "**添加专用终结点**"。 
6. 在 " **创建专用终结点**" 中，输入或选择以下信息：

    | 设置 | ReplTest1 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    |Location|选择**WestCentralUS**。|
    |姓名|输入 *myPrivateEndpoint*。  |
    |存储子资源|保留默认 **Blob**。 |
    | **上网** |  |
    | 虚拟网络  | 从资源组*myResourceGroup*中选择 " *MyVirtualNetwork* "。 |
    | Subnet | 选择 *mySubnet*。 |
    | **专用 DNS 集成**|  |
    | 与专用 DNS 区域集成  | 保留默认值 **"是"** 。 |
    | 专用 DNS 区域  | 保留默认值 ** "privatelink.blob.core.windows.net**"。 |
    |||
7. 选择 **"确定"** 。 
8. 选择 " **查看 + 创建**"。 你将转到 " **查看** " "查看" "创建" 页，其中 Azure 验证你的配置。 
9. 看到 " **验证通过** " 消息后，选择 " **创建**"。 
10. 浏览到刚刚创建的存储帐户资源。
11. 从左侧内容菜单中选择 " **访问密钥**"。
12. 对于 key1 的连接字符串，选择 "**复制**"。
 
## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 internet 连接到 VM *myVm* ，如下所示：

1. 在门户的搜索栏中，输入 " *myVm*"。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开。

1. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开下载的 .rdp * 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-storage-account-privately-from-the-vm"></a>从 VM 专用访问存储帐户

在本部分中，将使用专用终结点将专用连接到存储帐户。

> [!IMPORTANT]
> 用于存储的 DNS 配置需要对主机文件进行手动修改以包含特定帐户的 FQDN。请在 Windows 上使用管理员权限修改以下文件： c:\Windows\System32\Drivers\etc\hosts 或 Linux/etc/hosts按以下格式包括上一步中的帐户的 DNS 信息 [专用 IP 地址] myaccount.blob.core.windows.net

1. 在 *myVM*的远程桌面中，打开 PowerShell。
2. 输入 `nslookup mystorageaccount.blob.core.windows.net`将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
