---
title: Connect privately to a storage account using Azure Private Endpoint
description: Learn how to connect privately to a storage account in Azure using a Private Endpoint.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfe0caaf199821358f8a66ac65ae75c38336c725
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228095"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connect privately to a storage account using Azure Private Endpoint
Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this Quickstart, you will learn how to create a VM on an Azure virtual network, a storage account with a Private Endpoint using the Azure portal. Then, you can securely access the storage account from the VM.


## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-vm"></a>创建 VM
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link Resource (a storage account in this example).

### <a name="create-the-virtual-network"></a>创建虚拟网络

In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource.

1. 在屏幕的左上方，选择“创建资源” > “网络” > “虚拟网络”。
1. 在“创建虚拟网络”中，输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    | 名称 | 输入 *MyVirtualNetwork*。 |
    | 地址空间 | 输入 10.1.0.0/16。 |
    | Subscription | 选择订阅。|
    | Resource group | 选择“新建”，输入 myResourceGroup，然后选择“确定”。 |
    | Location | 选择“WestCentralUS”。|
    | 子网 - 名称 | 输入 *mySubnet*。 |
    | 子网 - 地址范围 | 输入 10.1.0.0/24。 |
    |||
1. 将其余的设置保留默认值，然后选择“创建”。


### <a name="create-virtual-machine"></a>创建虚拟机

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual machine**.

1. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    | **项目详细信息** | |
    | Subscription | 选择订阅。 |
    | Resource group | 选择“myResourceGroup”。 已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 *myVm*。 |
    | 地区 | 选择“WestCentralUS”。 |
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

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. 在“创建虚拟机 - 基本信息”中，选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    | 虚拟网络 | 保留默认值“MyVirtualNetwork”。  |
    | 地址空间 | 保留默认值“10.1.0.0/24”。|
    | 子网 | 保留默认值“mySubnet (10.1.0.0/24)”。|
    | 公共 IP | 保留默认值“(new) myVm-ip”。 |
    | 公共入站端口 | 选择“允许所选端口”。 |
    | 选择入站端口 | 选择 HTTP 和 RDP。|
    ||

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

1. 看到“验证通过”消息时，选择“创建”。

## <a name="create-your-private-endpoint"></a>Create your Private Endpoint
In this section, you will create a private storage account using a Private Endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Storage** > **Storage account**.

1. In **Create storage account - Basics**, enter or select this information:

    | 设置 | Value |
    | ------- | ----- |
    | **项目详细信息** | |
    | Subscription | 选择订阅。 |
    | Resource group | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    | **实例详细信息** |  |
    | 存储帐户名称  | Enter *mystorageaccount*. 如果此名称已被使用，请创建唯一的名称。 |
    | 地区 | 选择“WestCentralUS”。 |
    | 性能| 保留默认值“标准”。 |
    | 帐户类型 | Leave the default **Storage (general purpose v2)** . |
    | 复制 | Select **Read-access geo-redundant storage (RA-GRS)** . |
    |||
  
3. Select **Next: Networking**.
4. In **Create a storage account - Networking**, connectivity method, select **Private Endpoint**.
5. In **Create a storage account - Networking**, select **Add Private Endpoint**. 
6. In **Create Private Endpoint**, enter or select this information:

    | 设置 | Value |
    | ------- | ----- |
    | **项目详细信息** | |
    | Subscription | 选择订阅。 |
    | Resource group | 选择“myResourceGroup”。 已在上一部分创建此内容。|
    |Location|选择“WestCentralUS”。|
    |名称|Enter *myPrivateEndpoint*.  |
    |Storage sub-resource|Leave the default **Blob**. |
    | **网络** |  |
    | 虚拟网络  | Select *MyVirtualNetwork* from resource group *myResourceGroup*. |
    | 子网 | 选择“mySubnet”。 ** |
    | **专用 DNS 集成**|  |
    | 与专用 DNS 区域集成  | Leave the default **Yes**. |
    | 专用 DNS 区域  | Leave the default ** (New) privatelink.blob.core.windows.net**. |
    |||
7. 选择“确定”。 
8. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。 
9. 看到“验证通过”消息时，选择“创建”。 
10. Browse to the storage account resource that you just created.
11. Select **Access Keys** from the left content menu.
12. Select **Copy** on the connection string for key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

Connect to the VM *myVm* from the internet as follows:

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开。

1. 选择“下载 RDP 文件”。 Azure 创建远程桌面协议 (.rdp) 文件，并下载到计算机。

1. 打开下载的 .rdp* 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-storage-account-privately-from-the-vm"></a>Access storage account privately from the VM

In this section, you will connect privately to the storage account using the Private Endpoint.

> [!IMPORTANT]
> DNS configuration for storage needs a manual modification on the hosts file to include the FQDN of the specific account Please modify the following file using administrator permissions on Windows: c:\Windows\System32\Drivers\etc\hosts or Linux /etc/hosts Include the DNS information for the account from previous step in the following format [Private IP Address] myaccount.blob.core.windows.net

1. 在  *myVM* 的远程桌面中打开 PowerShell。
2. Enter `nslookup mystorageaccount.blob.core.windows.net` You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. 安装 [Microsoft Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. 选择“**下一步**”。
8. Enter the connection string by pasting the information previously copied.
9. 选择“**下一步**”。
10. 选择“连接”。
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. 使用 AzCopy 将数据传输到 Blob、文件和表存储或将数据从其中传出。 


## <a name="clean-up-resources"></a>清理资源 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. 在门户顶部的“搜索”框中输入“myResourceGroup” **  ，然后从搜索结果中选择“myResourceGroup”。 **   
2. 选择“删除资源组”。 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## <a name="next-steps"></a>后续步骤
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
