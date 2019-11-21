---
title: Connect to an Azure Cosmos account with Azure Private Link
description: Learn how to securely access the Azure Cosmos account from a VM by creating a Private Endpoint.
author: asudbring
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 90710176ec16d1c565e24ff7df56b0b838f2699e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229421"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Connect privately to an Azure Cosmos account using Azure Private Link

Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this article, you will learn how to create a VM on an Azure virtual network and an Azure Cosmos account with a Private Endpoint using the Azure portal. Then, you can securely access the Azure Cosmos account from the VM.

## <a name="sign-in-to-azure"></a>登录 Azure

Sign in to the [Azure portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>创建 VM

### <a name="create-the-virtual-network"></a>创建虚拟网络

In this section, you will create a virtual network and the subnet to host the VM that is used to access your Private Link resource (an Azure Cosmos account in this example).

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

### <a name="create-the-virtual-machine"></a>创建虚拟机

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
    | 用户名 | Enter a username of your choice. |
    | 密码 | 输入选择的密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 确认密码 | Reenter the password. |
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

## <a name="create-an-azure-cosmos-account"></a>创建 Azure Cosmos 帐户

Create an [Azure Cosmos SQL API account](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). For simplicity, you can create the Azure Cosmos account in the same region as the other resources (that is "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Create a Private Endpoint for your Azure Cosmos account

Create a Private Link for your Azure Cosmos account as described in the [Create a Private Link using the Azure portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) section of the linked article.

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

Connect to the VM *myVm* from the internet as follows:

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开。

1. 选择“下载 RDP 文件”。 Azure 创建远程桌面协议 (.rdp) 文件，并下载到计算机。

1. 打开下载的 *.rdp* 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Access the Azure Cosmos account privately from the VM

In this section, you will connect privately to the Azure Cosmos account using the Private Endpoint. 

> [!IMPORTANT]
> The DNS configuration for the Azure Cosmos account needs a manual modification on the hosts file to include the FQDN of the specific account. In production scenarios you will configure the DNS server to use the private IP addresses. However for the demo purpose, you can use administrator permissions on the VM and modify the `c:\Windows\System32\Drivers\etc\hosts` file (on Windows) or `/etc/hosts` file (on Linux) to include the IP address and DNS mapping.

1. To include the IP address and DNS mapping, sign into your Virtual machine *myVM*, open the `c:\Windows\System32\Drivers\etc\hosts` file and include the DNS information from previous step in the following format:

   [Private IP Address] [Account endpoint].documents.azure.com

   **示例：**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. In the Remote Desktop of *myVM*, install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Select **Cosmos DB Accounts (Preview)** with the right-click.

1. Select **Connect to Cosmos DB**.

1. 选择“API”。

1. Enter the connection string by pasting the information previously copied.

1. 选择“**下一步**”。

1. 选择“连接”。

1. Browse the Azure Cosmos databases and containers from *mycosmosaccount*.

1. (Optionally) add new items to *mycosmosaccount*.

1. Close the remote desktop connection to *myVM*.

## <a name="clean-up-resources"></a>清理资源

When you're done using the Private Endpoint, Azure Cosmos account and the VM, delete the resource group and all of the resources it contains: 

1. 在门户顶部的“搜索”框中输入“myResourceGroup” **  ，然后从搜索结果中选择“myResourceGroup”。 **  

1. 选择“删除资源组”。

1. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## <a name="next-steps"></a>后续步骤

In this article, you created a VM on a virtual network, an Azure Cosmos account and a Private Endpoint. You connected to the VM from the internet and securely communicated to the Azure Cosmos account using Private Link.

* To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).

* To learn more about limitation of Private Endpoint when using with Azure Cosmos DB, see [Azure Private Link with Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) article.