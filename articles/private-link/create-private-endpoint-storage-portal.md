---
title: 使用 Azure 专用终结点以私密方式连接到存储帐户
description: 了解如何使用专用终结点以私密方式连接到存储帐户。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 111e6e2f80c3460f363c496b7b32befdca16250d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81115104"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>使用 Azure 专用终结点以私密方式连接到存储帐户
Azure 专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。

在本快速入门中，你将了解如何使用 Azure 门户在 Azure 虚拟网络中创建一个 VM，以及一个采用专用终结点的存储帐户。 然后，可以从该 VM 安全地访问该存储帐户。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-vm"></a>创建 VM
在本部分中，你将创建虚拟网络和子网来托管用于访问专用链接资源（在本例中为存储帐户）的 VM。

## <a name="virtual-network-and-parameters"></a>虚拟网络和参数

在本部分中，你将创建虚拟网络和子网来托管用于访问专用链接资源的 VM。

在本部分中，你需要将步骤中的以下参数替换为以下信息：

| 参数                   | Value                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | 美国中西部      |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择 "**创建资源** > " "**计算** > " "**虚拟机**"。

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
    ||

1. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置  。

1. 看到“验证通过”消息时，选择“创建”   。

## <a name="create-your-private-endpoint"></a>创建专用终结点
在本部分中，你将使用专用终结点创建一个专用存储帐户。 

1. 在 Azure 门户屏幕的左上方，选择 "**创建资源** > " "**存储** > " "存储**帐户**"。

1. 在“创建存储帐户 - 基本信息”中，输入或选择以下信息****：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。  已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | 存储帐户名称  | 输入 *mystorageaccount*。 如果此名称已被使用，请创建唯一的名称。 |
    | 区域 | 选择“WestCentralUS”。  |
    | 性能| 保留默认值“标准”****。 |
    | 帐户类型 | 保留默认值“存储(常规用途 v2)”****。 |
    | 复制 | 选择“读取访问异地冗余存储 (RA-GRS)”****。 |
    |||
  
3. 在完成时选择“下一步:  网络”。
4. 在“创建存储帐户 - 网络”**** 中，在连接方法中，选择“专用终结点”****。
5. 在“创建存储帐户 - 网络”**** 中，选择“添加专用终结点”****。 
6. 在“创建专用终结点”**** 中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。  已在上一部分创建此内容。|
    |位置|选择“WestCentralUS”。 |
    |名称|输入“myPrivateEndpoint”  。  |
    |存储子资源|保留默认值“Blob”****。 |
    | **网络** |  |
    | 虚拟网络  | 从资源组*myResourceGroup*中选择 " *MyVirtualNetwork* "。 |
    | 子网 | 选择“mySubnet”  。 |
    | **专用 DNS 集成**|  |
    | 与专用 DNS 区域集成  | 保留默认值“是”****。 |
    | 专用 DNS 区域  | 保留默认值 " **privatelink.blob.core.windows.net**"。 |
    |||
7. 选择“确定”  。 
8. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置  。 
9. 看到“验证通过”消息时，选择“创建”   。 
10. 浏览找到你刚才创建的存储帐户资源。
11. 从左侧内容菜单中选择“访问密钥”。****
12. 在 key1 的连接字符串上，选择“复制”****。
 
## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM *myVm*，如下所示：

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

## <a name="access-storage-account-privately-from-the-vm"></a>从 VM 以私密方式访问存储帐户

在本部分中，你将使用专用终结点私密地连接到存储帐户。

1. 在 *myVM* 的远程桌面中，打开 PowerShell。
2. 输入`nslookup mystorageaccount.blob.core.windows.net`将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. 安装 [Microsoft Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。
4. 通过右键单击选择“存储帐户”。****
5. 选择“连接到 Azure 存储”****。
6. 选择“使用连接字符串”****。
7. 选择“下一步”  。
8. 通过粘贴之前复制的信息来输入连接字符串。
9. 选择“下一步”  。
10. 选择“连接”  。
11. 浏览 mystorageaccount 中的 Blob 容器 
12. （可选）创建文件夹并/或将文件上传到 *mystorageaccount*。 
13. 关闭与*myVM*的远程桌面连接。 

用于访问存储帐户的其他选项：
- Microsoft Azure 存储资源管理器是 Microsoft 提供的独立免费应用程序，可用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。 你可以安装应用程序来以私密方式浏览存储帐户内容。 
 
- AzCopy 实用工具是适用于 Azure 存储的高性能脚本数据传输的另一个选项。 使用 AzCopy 将数据传输到 Blob、文件和表存储或将数据从其中传出。 


## <a name="clean-up-resources"></a>清理资源 
用完专用终结点、存储帐户和 VM 之后，请删除资源组及其包含的所有资源： 
1. 在门户顶部的“搜索”框中输入“myResourceGroup”，并从搜索结果中选择“myResourceGroup”    。 
2. 选择“删除资源组”  。 
3. 输入*myResourceGroup*作为 **"资源组名称"** ，然后选择 "**删除**"。 

## <a name="next-steps"></a>后续步骤
在本快速入门中，你在虚拟网络上创建了一个 VM、一个存储帐户和一个专用终结点。 你已从 Internet 连接到一个 VM，并使用专用链接安全地与存储帐户进行通信。 若要了解有关专用终结点的详细信息，请参阅[什么是 Azure 专用终结点？](private-endpoint-overview.md)。
