---
title: 创建 Azure HPC 缓存
description: 如何创建 Azure HPC 缓存实例
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: bed158fb99654bd48184073b1266ae630255558b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613163"
---
# <a name="create-an-azure-hpc-cache"></a>创建 Azure HPC 缓存

使用 Azure 门户或 Azure CLI 创建缓存。

![Azure 门户中的缓存概述的屏幕截图，底部带有“创建”按钮](media/hpc-cache-home-page.png)

单击下面的图像，观看有关创建缓存和添加存储目标的 [视频演示](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) 。

[![视频缩略图： Azure HPC 缓存：安装程序 (单击以访问视频页面) ](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[门户](#tab/azure-portal)

## <a name="define-basic-details"></a>定义基本详细信息

![Azure 门户中的“项目详细信息”页的屏幕截图](media/hpc-cache-create-basics.png)

在“项目详细信息”**** 中，选择将托管缓存的订阅和资源组。

在“服务详细信息”**** 中，设置缓存名称和其他属性：

* 位置 - 选择其中一个[支持的区域](hpc-cache-overview.md#region-availability)。
* 虚拟网络 - 可选择现有虚拟网络或创建新的虚拟网络。
* 子网-选择或创建至少具有 64 IP 地址 (/24) 的子网。 此子网必须仅用于此 Azure HPC 缓存实例。

## <a name="set-cache-capacity"></a>设置缓存容量
<!-- referenced from GUI - update aka.ms link if you change this header text -->

在“缓存”**** 页上，必须设置缓存的容量。 此处设置的值确定缓存可以容纳多少数据，以及为客户端请求提供服务的速度。

容量还会影响缓存的成本。

通过设置以下两个值选择容量：

* 缓存的最大数据传输速率（吞吐量）（GB/秒）
* 为缓存数据分配的存储量 (TB)

选择可用吞吐量值和缓存存储大小之一。

请记住，实际的数据传输速率取决于工作负荷、网络速度和存储目标的类型。 你选择的值设置整个缓存系统的最大吞吐量，但其中一些用于开销任务。 例如，如果客户端请求尚未存储在缓存中的文件，或者该文件被标记为过时，则缓存将使用其某些吞吐量从后端存储提取该文件。

Azure HPC 缓存会管理缓存和预加载哪些文件，以最大限度地提高缓存命中率。 会持续评估缓存内容，并在不经常访问文件的情况下将文件移动到长期存储。 选择可轻松保留活动的工作文件集的缓存存储大小，以及用于元数据和其他开销的额外空间。

![缓存大小调整页的屏幕截图](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>启用 Azure Key Vault 加密 (可选) 

如果缓存位于支持客户管理的加密密钥的区域，则 " **磁盘加密密钥** " 页将出现在 " **缓存** " 和 " **标记** " 选项卡之间。 阅读 [地区可用性](hpc-cache-overview.md#region-availability) ，了解有关区域支持的详细信息。

如果要管理用于缓存存储的加密密钥，请在 " **磁盘加密密钥** " 页上提供 Azure Key Vault 信息。 密钥保管库必须位于与缓存相同的区域和订阅中。

如果您不需要客户管理的密钥，则可以跳过此部分。 默认情况下，Azure 使用 Microsoft 托管的密钥加密数据。 有关详细信息，请参阅 [Azure 存储加密](../storage/common/storage-service-encryption.md) 。

> [!NOTE]
>
> * 创建缓存后，不能在 Microsoft 管理的密钥和客户托管的密钥之间进行更改。
> * 创建缓存后，你必须对其进行授权才能访问密钥保管库。 单击缓存的 "**概述**" 页中的 "**启用加密**" 按钮，以启用加密。 请在创建缓存的90分钟内执行此步骤。
> * 缓存磁盘是在此授权之后创建的。 这意味着，初始缓存创建时间较短，但在授权访问后，缓存将不会准备好使用十分钟或更多时间。

有关客户托管的密钥加密过程的完整说明，请参阅 [将客户托管的加密密钥用于 AZURE HPC 缓存](customer-keys.md)。

!["加密密钥" 页的屏幕截图，其中显示了 "客户管理"，并显示了 key vault 字段](media/create-encryption.png)

选择 " **客户管理** " 以选择客户托管的密钥加密。 将显示密钥保管库规范字段。 选择要使用的 Azure Key Vault，然后选择要用于此缓存的密钥和版本。 密钥必须是2048位的 RSA 密钥。 你可以从此页面创建新的密钥保管库、密钥或密钥版本。

创建缓存后，你必须对其进行授权才能使用 key vault 服务。 有关详细信息，请参阅 [从缓存授权 Azure Key Vault 加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) 。

## <a name="add-resource-tags-optional"></a>添加资源标记（可选）

在“标记”**** 页上，可以向 Azure HPC 缓存实例添加[资源标记](https://go.microsoft.com/fwlink/?linkid=873112)。

## <a name="finish-creating-the-cache"></a>完成缓存创建

配置新缓存后，请单击 " **查看** " 和 "创建" 选项卡。门户将验证你的选择，并允许你查看你的选择。 如果所有内容都正确，请单击“创建”****。

缓存创建花费大约 10 分钟。 可在 Azure 门户的通知面板中跟踪进度。

![门户中缓存创建“正在部署”和“通知”页的屏幕截图](media/hpc-cache-deploy-status.png)

创建完成后，将显示一条通知，其中包含指向新的 Azure HPC 缓存实例的链接，并且缓存将显示在订阅的**资源**列表中。

![Azure 门户中的 Azure HPC 缓存实例的屏幕截图](media/hpc-cache-new-overview.png)

> [!NOTE]
> 如果缓存使用客户托管的加密密钥，则在部署状态更改为 "完成" 之前，缓存可能会出现在 "资源" 列表中。 一旦缓存的状态为 " **正在等待密钥** "，就可以 [授权它](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) 使用密钥保管库。

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>创建缓存并 Azure CLI

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

> [!NOTE]
> Azure CLI 当前不支持使用客户托管的加密密钥创建缓存。 使用 Azure 门户。

使用 [az hpc-cache create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) 命令创建新的 Azure hpc 缓存。

提供以下值：

* 缓存资源组名称
* 缓存名称
* Azure 区域
* 缓存子网，格式如下：

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  缓存子网需要 (/24) 至少64个 IP 地址，并且不能容纳其他任何资源。

* 缓存容量。 以下两个值设置 Azure HPC 缓存的最大吞吐量：

  * 缓存大小 (GB) 
  * 缓存基础结构中使用的虚拟机的 SKU

  [az hpc-缓存 sku 列表](/cli/azure/ext/hpc-cache/hpc-cache/skus) 显示了可用的 sku 以及每个 sku 的有效缓存大小选项。 缓存大小选项的范围为 3 TB 到 48 TB，但仅支持某些值。

  此图显示在 (7 月 2020) 准备此文档时，哪些缓存大小和 SKU 组合有效。

  | 缓存大小 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | 是         | 否          | 否          |
  | 6144 GB    | 是         | 是         | 否          |
  | 12288 GB   | 是         | 是         | 是         |
  | 24576 GB   | 否          | 是         | 是         |
  | 49152 GB   | 否          | 否          | 是         |

  阅读 "门户说明" 选项卡上的 " **设置缓存容量** " 部分，了解有关定价、吞吐量以及如何为工作流适当调整缓存大小的重要信息。

缓存创建示例：

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

创建缓存花费几分钟时间。 成功后，create 命令返回如下所示的输出：

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

该消息包含一些有用的信息，包括以下项：

* 客户端装入地址-准备好将客户端连接到缓存时，请使用这些 IP 地址。 有关详细信息，请参阅 [安装 AZURE HPC 缓存](hpc-cache-mount.md) 。
* 升级状态-发布软件更新时，此消息将会更改。 你可以在方便的时间手动 [升级缓存软件](hpc-cache-manage.md#upgrade-cache-software) ，或在数天后自动将其应用。

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> HPCCache PowerShell 模块目前为公共预览版。 此预览版本未提供服务级别协议。 对于生产型工作负荷，不建议使用该预览版。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="requirements"></a>要求

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 如果你选择使用 Cloud Shell，请参阅 [Azure Cloud Shell 概述](https://docs.microsoft.com/azure/cloud-shell/overview)来了解详细信息。

> [!IMPORTANT]
> **HPCCache** PowerShell 模块为预览版时，必须使用 cmdlet 单独安装它 `Install-Module` 。 此 PowerShell 模块公开上市后，它将成为未来 Az PowerShell 模块版本的一部分，并从 Azure Cloud Shell 中的本机提供。

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>创建缓存并 Azure PowerShell

> [!NOTE]
> Azure PowerShell 当前不支持使用客户托管的加密密钥创建缓存。 使用 Azure 门户。

使用 [AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) cmdlet 创建新的 Azure HPC 缓存。

提供以下值：

* 缓存资源组名称
* 缓存名称
* Azure 区域
* 缓存子网，格式如下：

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  缓存子网需要 (/24) 至少64个 IP 地址，并且不能容纳其他任何资源。

* 缓存容量。 以下两个值设置 Azure HPC 缓存的最大吞吐量：

  * 缓存大小 (GB) 
  * 缓存基础结构中使用的虚拟机的 SKU

  [AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) 显示可用的 sku 以及每个 sku 的有效缓存大小选项。 缓存大小选项的范围为 3 TB 到 48 TB，但仅支持某些值。

  此图显示在 (7 月 2020) 准备此文档时，哪些缓存大小和 SKU 组合有效。

  | 缓存大小 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | 是         | 否          | 否          |
  | 6144 GB    | 是         | 是         | 否          |
  | 12288 GB   | 是         | 是         | 是         |
  | 24576 GB   | 否          | 是         | 是         |
  | 49152 GB   | 否          | 否          | 是         |

  阅读 "门户说明" 选项卡上的 " **设置缓存容量** " 部分，了解有关定价、吞吐量以及如何为工作流适当调整缓存大小的重要信息。

缓存创建示例：

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

创建缓存花费几分钟时间。 成功后，create 命令返回以下输出：

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

该消息包含一些有用的信息，包括以下项：

* 客户端装入地址-准备好将客户端连接到缓存时，请使用这些 IP 地址。 有关详细信息，请参阅 [安装 AZURE HPC 缓存](hpc-cache-mount.md) 。
* 升级状态-发布软件更新时，此消息会更改。 你可以在方便的时间手动 [升级缓存软件](hpc-cache-manage.md#upgrade-cache-software) ，或在数天后自动将其应用。

---

## <a name="next-steps"></a>后续步骤

缓存出现在 **资源列表** 中后，您可以转到下一步。

* [定义存储目标](hpc-cache-add-storage.md) 以允许缓存访问数据源。
* 如果使用客户托管的加密密钥，则需要从缓存的 "概述" 页 [授权 Azure Key Vault 加密](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) ，以完成缓存设置。 在添加存储之前，必须执行此步骤。 有关详细信息， [请参阅使用客户管理的加密密钥](customer-keys.md) 。
