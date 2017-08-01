---
title: "如何在 Azure 门户中创建、管理或删除存储帐户 | Microsoft 文档"
description: "在 Azure 门户中创建新的存储帐户、管理帐户访问密钥或删除存储帐户。 了解标准和高级存储帐户。"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 87c37da0-6cc6-4d88-a330-ef2896a1531d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
f1_keywords:
- sql13.swb.windowsazurestorage.connect.f1
ms.date: 01/23/2017
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 16d9fa8a7b7593f222976897bddf615c28109540
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017


---
# <a name="about-azure-storage-accounts"></a>关于 Azure 存储帐户
[!INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>概述
Azure 存储帐户提供唯一的命名空间来存储和访问你的 Azure 存储数据对象。 存储帐户中的所有对象会作为组共同计费。 默认情况下，只有你，即帐户所有者，才能使用你的帐户中的数据。

[!INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>存储帐户计费
[!INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [!NOTE]
> 当你创建 Azure 虚拟机时，如果在部署位置中还没有存储帐户，则会在该位置自动创建一个存储帐户。 因此，没有必要按照下面的步骤来创建虚拟机磁盘的存储帐户。 存储帐户名称将基于虚拟机名称。 请参阅 [Azure 虚拟机文档](https://azure.microsoft.com/documentation/services/virtual-machines/) 以了解更多详细信息。
> 
> 

## <a name="storage-account-endpoints"></a>存储帐户终结点
存储在 Azure 存储中的每个对象都有唯一的 URL 地址。 存储帐户名称构成该地址的子域。 特定于每个服务的子域和域名的组合构成你的存储帐户的 *终结点* 。

例如，如果你的存储帐户名为 *mystorageaccount*，则你的存储帐户的默认终结点为：

* Blob 服务：http://*mystorageaccount*.blob.core.windows.net
* 表服务：http://*mystorageaccount*.table.core.windows.net
* 队列服务：http://*mystorageaccount*.queue.core.windows.net
* 文件服务：http://*mystorageaccount*.file.core.windows.net

> [!NOTE]
> Blob 存储帐户仅公开 Blob 服务终结点。
> 
> 

用于访问存储帐户中某个对象的 URL 是通过将存储帐户中对象的位置附加到终结点而构建的。 例如，Blob 地址可能具有以下格式：http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

此外还可以配置用于存储帐户的自定义域名称。 有关经典存储帐户的详细信息，请参阅 [为 Blob 存储终结点配置自定义域名](storage-custom-domain-name.md) 。 对于 Resource Manager 存储帐户，此功能尚未添加到 [Azure 门户](https://portal.azure.com) ，但你可使用 PowerShell 配置它。 有关详细信息，请参阅 [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) cmdlet。  

## <a name="create-a-storage-account"></a>创建存储帐户
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“新建” -> “存储” -> “存储帐户”。
3. 输入你的存储帐户的名称。 有关如何使用存储帐户名称在 Azure 存储中定位你的对象的详细信息，请参阅 [存储帐户终结点](#storage-account-endpoints) 。
   
   > [!NOTE]
   > 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。
   > 
   > 你的存储帐户名称在 Azure 中必须是唯一的。 Azure 门户将指出你选择的存储帐户名称是否已被使用。
   > 
   > 
4. 指定要使用的部署模型：**Resource Manager** 或**经典**。  建议使用“Resource Manager”部署模型。 有关详细信息，请参阅[了解 Resource Manager 部署和经典部署](../azure-resource-manager/resource-manager-deployment-model.md)。
   
   > [!NOTE]
   > 仅可使用 Resource Manager 部署模型来创建 Blob 存储帐户。
   > 
   > 
5. 选择存储帐户的类型：“常规用途”或“Blob 存储”。 “常规用途”是默认值。
   
    如果已选择“常规用途”，则指定性能层：“标准”或“高级”。 默认值为“标准”。 有关标准和高级存储帐户的更多详细信息，请参阅 [Microsoft Azure 存储简介](storage-introduction.md)和[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](storage-premium-storage.md)。
   
    如果已选择“Blob 存储”，则指定访问层：“经常访问”或“不常访问”。 默认值为“经常访问”。 有关如何使用存储帐户名称在 Azure 存储中定位你的对象的详细信息，请参阅 [Azure Blob 存储：不常访问和经常访问的层](storage-blob-storage-tiers.md) 以了解更多详细信息。
6. 选择存储帐户的复制选项：“LRS”、“GRS”、“RA-GRS”或“ZRS”。 默认值为“RA-GRS”。 有关 Azure 存储复制选项的更多详细信息，请参阅 [Azure 存储复制](storage-redundancy.md)。
7. 选择想在其中创建新存储帐户的订阅。
8. 指定新资源组或选择现有资源组。 有关资源组的详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。
9. 选择存储帐户的地理区域。 有关哪些服务在哪个区域中可用的详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/#services) 。
10. 单击“创建”以创建存储帐户。

## <a name="manage-your-storage-account"></a>管理存储帐户
### <a name="change-your-account-configuration"></a>更改帐户配置
创建存储帐户之后，可以修改其配置，例如更改帐户所用的复制选项，或更改 Blob 存储帐户的访问层。 在 [Azure 门户](https://portal.azure.com)中，导航到存储帐户，查找并单击“设置”下的“配置”以查看和/或更改帐户配置。

> [!NOTE]
> 视你在创建存储帐户时选择的性能层而定，可能无法使用某些复制选项。
> 
> 

更改复制选项将更改你的定价。 有关更多详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/) 页。

对于 Blob 存储帐户，更改访问层除了会更改你的定价之外，可能还会产生更改费用。 有关更多详细信息，请参阅 [Blob 存储帐户 — 定价和计费](storage-blob-storage-tiers.md#pricing-and-billing) 。

### <a name="manage-your-storage-access-keys"></a>管理存储访问密钥
当你创建存储帐户时，Azure 将生成两个 512 位存储访问密钥，用于在用户访问该存储帐户时对其进行身份验证。 通过提供两个存储访问密钥，Azure 使你能够在不中断存储服务的情况下重新生成用于访问该服务的密钥。

> [!NOTE]
> 我们建议你避免与其他人共享你的存储访问密钥。 若要允许不提供你的访问密钥即可访问存储空间资源，可使用 *共享访问签名*。 共享访问签名可用于访问你的帐户中的资源，访问时间间隔由你定义，访问权限由你指定。 有关详细信息，请参阅 [使用共享访问签名 (SAS)](storage-dotnet-shared-access-signature-part-1.md) 。
> 
> 
<a id="view-and-copy-storage-access-keys"/></a>
#### <a name="view-and-copy-storage-access-keys"></a>查看和复制存储访问密钥
在 [Azure 门户](https://portal.azure.com)中，导航到你的存储帐户，单击“所有设置”，然后单击“配置”以查看和/或更改帐户配置。 “访问密钥”边栏选项卡还包含使用主密钥和辅助密钥预配置的连接字符串，可复制到应用程序中使用。

#### <a name="regenerate-storage-access-keys"></a>重新生成存储访问密钥
我们建议你定期更改存储帐户的访问密钥，以确保存储连接安全。 分配了两个访问密钥，以便在你重新生成其中一个访问密钥时，始终能够使用另一个访问密钥连接到存储帐户。

> [!WARNING]
> 重新生成访问密钥会影响 Azure 中的服务以及你自己的依赖于存储帐户的应用程序。 必须更新使用访问密钥访问存储帐户的所有客户端，以使用新密钥。
> 
> 

**媒体服务** - 如果你的媒体服务依赖于存储帐户，则必须在重新生成密钥后将访问密钥与媒体服务重新同步。

**应用程序** - 如果你拥有使用存储帐户的 Web 应用程序或云服务，则重新生成密钥将失去连接，除非你滚动使用密钥。

**存储资源管理器** - 如果你使用任何 [存储资源管理器应用程序](storage-explorers.md)，可能需要更新这些应用程序所使用的存储密钥。

下面是轮换存储访问密钥的过程：

1. 更新应用程序代码中的连接字符串以引用存储帐户的辅助访问密钥。
2. 为你的存储帐户重新生成主访问密钥。 在“访问密钥”边栏选项卡上，单击“重新生成密钥 1”，然后单击“是”以确认要生成新密钥。
3. 更新代码中的连接字符串以引用新的主访问密钥。
4. 以相同方式重新生成辅助访问密钥。

## <a name="delete-a-storage-account"></a>删除存储帐户
若要删除不再使用的存储帐户，请在 [Azure 门户](https://portal.azure.com)中导航到该存储帐户，然后单击“删除”。 删除存储帐户将删除整个帐户，包括该帐户中的所有数据。

> [!WARNING]
> 无法恢复已删除的存储帐户，也无法检索删除之前该存储帐户包含的任何内容。 请在删除帐户之前务必备份你想要保存的任何内容。 对于帐户中的任务资源也是如此 — 一旦你删除了一个 Blob、表、队列或文件 ，则它将被永久删除。
> 
> 

若要删除与 Azure 虚拟机相关联的存储帐户，必须首先确保已删除所有虚拟机磁盘。 如果不先删除虚拟机磁盘，则当你尝试删除存储帐户时，将看到如下错误消息：

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

如果存储帐户使用经典部署模型，你可以通过在 [Azure 门户](https://manage.windowsazure.com)中执行以下步骤来删除虚拟机磁盘：

1. 导航到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 导航到“虚拟机”选项卡。
3. 单击“磁盘”选项卡。
4. 选择你的数据磁盘，然后单击“删除磁盘”。
5. 若要删除磁盘映像，请导航到“映像”选项卡，然后删除存储在帐户中的任何映像。

有关详细信息，请参阅 [Azure 虚拟机文档](http://azure.microsoft.com/documentation/services/virtual-machines/)。

## <a name="next-steps"></a>后续步骤
* [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
* [Azure Blob 存储：不常访问和经常访问的层](storage-blob-storage-tiers.md)
* [Azure 存储复制](storage-redundancy.md)
* [配置 Azure 存储连接字符串](storage-configure-connection-string.md)
* [使用 AzCopy 命令行实用程序传输数据](storage-use-azcopy.md)
* 访问 [Azure 存储团队博客](http://blogs.msdn.com/b/windowsazurestorage/)。


