---
title: 使用 Azure 专用链接连接到 Azure Cosmos 帐户
description: 了解如何通过创建专用终结点来安全地从 VM 访问 Azure Cosmos 帐户。
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
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>使用 Azure Private Link 将专用连接到 Azure Cosmos 帐户

Azure 专用终结点是 Azure 中专用链接的基本构建基块。 它使 Azure 资源（例如虚拟机）能够与专用链接资源进行私下通信。

本文介绍如何在 Azure 虚拟网络上创建虚拟机，以及如何使用 Azure 门户的专用终结点创建 azure Cosmos 帐户。 然后，可以从 VM 安全地访问 Azure Cosmos 帐户。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到[Azure 门户。](https://portal.azure.com)

## <a name="create-a-vm"></a>创建 VM

### <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分中，将创建一个虚拟网络和子网来托管用于访问专用链接资源的 VM （本示例中为 Azure Cosmos 帐户）。

1. 在屏幕的左上方，选择“创建资源” **“网络”** “虚拟网络” >  > 。

1. 在“创建虚拟网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 *MyVirtualNetwork*。 |
    | 地址空间 | 输入 10.1.0.0/16。 |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”，输入 myResourceGroup，然后选择“确定”。 |
    | 位置 | 选择“WestCentralUS”。|
    | 子网 - 名称 | 输入 *mySubnet*。 |
    | 子网 - 地址范围 | 输入 10.1.0.0/24。 |
    |||

1. 将其余的设置保留默认值，然后选择“创建”。

### <a name="create-the-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择 "**创建资源** > **计算** > **虚拟机**"。

1. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 已在上一部分创建此内容。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 *myVm*。 |
    | 地区 | 选择“WestCentralUS”。 |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter”。 |
    | 大小 | 保留默认值“标准 DS1 v2”。 |
    | **管理员帐户** |  |
    | 用户名 | 输入所选的用户名。 |
    | 密码 | 输入选择的密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
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
    | Subnet | 保留默认值“mySubnet (10.1.0.0/24)”。|
    | 公共 IP | 保留默认值“(new) myVm-ip”。 |
    | 公共入站端口 | 选择“允许所选端口”。 |
    | 选择入站端口 | 选择 HTTP 和 RDP。|
    ||

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

1. 看到“验证通过”消息时，选择“创建”。

## <a name="create-an-azure-cosmos-account"></a>创建 Azure Cosmos 帐户

创建[Azure COSMOS SQL API 帐户](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)。 为简单起见，可以在与其他资源（即 "WestCentralUS"）相同的区域中创建 Azure Cosmos 帐户。

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>创建 Azure Cosmos 帐户的专用终结点

创建 Azure Cosmos 帐户的专用链接，如使用链接项目的[Azure 门户部分创建专用链接](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal)中所述。

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 internet 连接到 VM *myVm* ，如下所示：

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开。

1. 选择“下载 RDP 文件”。 Azure 创建远程桌面协议 (.rdp) 文件，并下载到计算机。

1. 打开下载的 *.rdp* 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” **“使用其他帐户”，以指定在创建 VM 时输入的凭据** > 。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>从 VM 私下访问 Azure Cosmos 帐户

在本部分中，将使用专用终结点将专用连接到 Azure Cosmos 帐户。 

> [!IMPORTANT]
> Azure Cosmos 帐户的 DNS 配置需要在 hosts 文件上手动修改才能包含特定帐户的 FQDN。 在生产方案中，你要将 DNS 服务器配置为使用专用 IP 地址。 但出于演示目的，可以使用 VM 上的管理员权限，并修改 `c:\Windows\System32\Drivers\etc\hosts` 文件（在 Windows 上）或 `/etc/hosts` 文件（在 Linux 上），以包括 IP 地址和 DNS 映射。

1. 若要包含 IP 地址和 DNS 映射，请登录到虚拟机*myVM*，打开 `c:\Windows\System32\Drivers\etc\hosts` 文件，并按以下格式包括上一步中的 DNS 信息：

   [专用 IP 地址][Account endpoint] .com

   **示例：**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. 在 *myVM*的远程桌面中，安装[Microsoft Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。

1. 选择右键单击 " **Cosmos DB 帐户（预览）** "。

1. 选择 "**连接到 Cosmos DB**"。

1. 选择“API”。

1. 通过粘贴之前复制的信息来输入连接字符串。

1. 选择“下一步”。

1. 选择“连接”。

1. 从*mycosmosaccount*浏览 Azure Cosmos 数据库和容器。

1. （可选）向*mycosmosaccount*添加新项。

1. 关闭与 *myVM*的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源

使用完专用终结点、Azure Cosmos 帐户和 VM 后，请删除资源组及其包含的所有资源： 

1. 在门户顶部的“搜索”框中输入“myResourceGroup” **  ，然后从搜索结果中选择“myResourceGroup”。 **  

1. 选择“删除资源组”。

1. 输入 " *myResourceGroup*  **" 以键入资源组名称**，然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在虚拟网络上创建 VM、Azure Cosmos 帐户和专用终结点。 你从 internet 连接到 VM，并使用专用链接安全地传达给 Azure Cosmos 帐户。

* 若要了解有关专用终结点的详细信息，请参阅 [什么是 Azure 专用终结点？](private-endpoint-overview.md)。

* 若要详细了解使用与 Azure Cosmos DB 时专用终结点的限制，请参阅[Azure Private Link with Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md)文章。