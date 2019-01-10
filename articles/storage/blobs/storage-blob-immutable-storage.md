---
title: Azure 存储 Blob 的不可变存储 | Microsoft Docs
description: Azure 存储提供对 Blob 对象存储的 WORM（一次写入，多次读取）支持，可让用户根据指定的时间间隔，以不可擦除、不可修改的状态存储数据。
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 11/05/2018
ms.author: hux
ms.component: blobs
ms.openlocfilehash: 53c719fbeb31d605bd9dff170a798b262d44698a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634263"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>在 Azure Blob 存储中存储业务关键型数据

Azure Blob（对象）存储的不可变存储可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据。 此状态可以根据用户指定的时间间隔使数据保持不可擦除且不可修改的状态。 在保留时间间隔期间，可以创建和读取，但不能修改或删除 Blob。

## <a name="overview"></a>概述

不可变存储可以帮助金融机构和相关行业（尤其是经纪人-经销商组织）安全存储数据。 它还可以在任何场景中用于保护关键数据不被删除。  

典型的应用程序包含：

- **法规遵从**：Azure Blob 存储的不可变存储可帮助组织达到 SEC 17a-4(f)、CFTC 1.31(d)、FINRA 和其他法规要求。

- **安全的文档保留**：Blob 存储确保用户（包括具有帐户管理权限的用户）无法修改或删除数据。

- **法定保留**：Azure Blob 存储的不可变存储允许用户以防篡改状态存储对诉讼或刑事调查事项来说很重要的敏感信息，存储时间视需要而定。

不可变存储可以实现：

- **基于时间的保留策略支持**：用户可以设置策略，将数据存储指定的时间间隔。

- **法定保留策略支持**：在保留时间间隔未知的情况下，用户可以设置法定保留，以不变的方式存储数据，直至法定保留清除。  设置法定保留时，Blob 可以创建和读取，但不能修改或删除。 每个法定保留都与一个用户定义的用作标识符字符串（例如案例 ID）的字母数字标记相关联。

- **支持所有 Blob 层**：WORM 策略独立于 Azure Blob 存储层，将应用到所有层：热层、冷层和存档层。 用户可将工作负荷的数据转换为最具成本效益的层，同时保持数据的不可变性。

- **容器级配置**：用户可在容器级别配置基于时间的保留策略和法定保留标记。 通过使用简单的容器级设置，用户可以创建并锁定基于时间的保留策略、扩展保留时间间隔、设置并清除法定保留，等等。 这些策略将应用到容器中的所有 Blob，不管是现有的还是新的 Blob。

- **审核日志记录支持**：每个容器包括一个审核日志。 该日志显示针对锁定的基于时间的保留策略执行的最多五个基于时间的保留命令，最多可以延长三个日志的保留时间间隔。 对于基于时间的保留，日志包含用户 ID、命令类型、时间戳和保留时间间隔。 对于法定保留，日志包含用户 ID、命令类型、时间戳和法定保留标记。 根据 SEC 17a-4(f) 法规准则，此日志的保留时间就是容器的生存时间。 [Azure 活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)显示所有控制平面活动的更全面日志。 由用户负责根据法规要求或其他要求永久存储这些日志。

不可变存储已在所有 Azure 公共区域中启用。

## <a name="how-it-works"></a>工作原理

Azure Blob 存储的不可变存储支持两类 WORM 或不可变策略：基于时间的保留和法定保留。 有关如何创建这些不可变策略的详细信息，请参阅[入门](#getting-started)部分。

在容器上应用基于时间的保留策略或法定保留时，所有现有的 Blob 都会转为不可变（不可写入和删除）状态。 所有上传到容器的新 Blob 也会转为不可变状态。

> [!IMPORTANT]
> 根据 SEC 17a-4(f) 和其他法规符合性要求，基于时间的保留策略必须处于锁定状态才能确保 Blob 不可变（不可写入和删除）。 我们建议将策略锁定合理的时间，通常为 24 小时内。 除非是短时功能试用，否则我们不建议使用非锁定状态。

在容器上应用基于时间的保留策略时，容器中的所有 Blob 都会保持在不可变的状态，其持续时间就是有效保留期。 现有 Blob 的有效保持期就是 Blob 创建时间和用户指定的保留时间间隔之间的差异。

就新 Blob 来说，有效保持期为用户指定的保留时间间隔。 由于用户可以延长保留时间间隔，因此不可变存储使用用户指定的保留时间间隔的最新值来计算有效保留期。

> [!TIP]
> 示例：
>
> 用户创建了一个基于时间的保留策略，将保留时间间隔设置为五年。
>
> 一年前，我们在该容器中创建了一个现有的 Blob (testblob1)。 testblob1 的有效保留期为四年。
>
> 一个新的 Blob (testblob2) 现已上传到容器。 此新 Blob 的有效保留期为五年。

### <a name="legal-holds"></a>法定保留

如果设置了法定保留，所有现有的和新的 Blob 会一直保持在不可变状态，直至法定保留被清除。 有关如何设置和清除法定保留的详细信息，请参阅[入门](#getting-started)部分。

容器可能会同时有法定保留策略和基于时间的保留策略。 该容器中的所有 Blob 会一直保持不可变状态，直至所有法定保留被清除，即使有效保留期已过。 与之相反，即使所有法定保留已被清除，Blob 也会保持不可变状态，直至有效保留期已过。

下表显示了会因各种不可变方案而禁用的 Blob 操作的类型。 有关详细信息，请参阅 [Azure Blob 服务 API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) 文档。

|场景  |Blob 状态  |不允许 Blob 操作  |
|---------|---------|---------|
|Blob 的有效保留时间间隔尚未到期，并且/或者法定保留已设置     |不可变：不可删除和写入         |删除容器、删除 Blob、放置 Blob<sup>1</sup>、放置块<sup>1</sup>、放置块列表<sup>1</sup>、设置 Blob 元数据、放置页、设置 Blob 属性、快照 Blob、增量复制 Blob、追加块         |
|Blob 的有效保留时间间隔尚未到期     |仅仅不可写入（允许删除操作）         |放置 Blob<sup>1</sup>、放置块<sup>1</sup>、放置块列表<sup>1</sup>、设置 Blob 元数据、放置页、设置 Blob 属性、快照 Blob、增量复制 Blob、追加块         |
|清除了所有法定保留，未在容器上设置任何基于时间的保留策略     |可变         |无         |
|未创建任何 WORM 策略（基于时间的保留或法定保留）     |可变         |无         |

<sup>1</sup> 应用程序可以调用一次此操作来创建 Blob。 不允许对 Blob 执行所有后续操作。

> [!NOTE]
>
> 不可变存储仅在常规用途 V2 和 Blob 存储帐户中可用。 必须通过 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)创建帐户。

## <a name="pricing"></a>定价

使用此功能不会产生额外的费用。 不可变数据的定价方式与常规的可变数据相同。 有关 Azure Blob 存储的定价详细信息，请参阅 [Azure 存储定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。


## <a name="getting-started"></a>入门

最新版本的 [Azure 门户](http://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 和预览版本的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) 支持 Azure Blob 存储的不可变存储。

### <a name="azure-portal"></a>Azure 门户

1. 请创建新的容器或选择现有容器，以便存储需要保持不可变状态的 Blob。
 容器必须位于 GPv2 或 Blob 存储帐户中。
2. 在容器设置中选择“访问策略”。 然后选择“不可变 Blob 存储”下的“+ 添加策略”。

    ![门户中的容器设置](media/storage-blob-immutable-storage/portal-image-1.png)

3. 若要启用基于时间的保留，请从下拉菜单中选择“基于时间的保留”。

    ![在“策略类型”下选择“基于时间的保留”](media/storage-blob-immutable-storage/portal-image-2.png)

4. 输入保留时间间隔（以天为单位，至少为一天）。

    ![“将保留期更新为”框](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    在屏幕截图中可以看到，策略的初始状态为“未锁定”。 可以使用较小的保留时间间隔来测试此功能，并在锁定之前对策略进行更改。 必须锁定才能符合 SEC 17a-4 等法规的要求。

5. 锁定策略。 右键单击省略号 (**...**)，此时会显示以下菜单：

    ![菜单中的“锁定策略”](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    选择“锁定策略”，此时策略状态会显示为“已锁定”。 锁定策略后无法将其删除，只允许延长保留时间间隔。

6. 若要启用法定保留，请选择“+ 添加策略”。 从下拉菜单中选择“法定保留”。

    ![菜单中“策略类型”下面的“法定保留”](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. 使用一个或多个标记创建法定保留。

    ![策略类型下面的“标记名称”框](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

8. 若要清除法定保留，只需删除标记。

### <a name="azure-cli"></a>Azure CLI

以下命令组包含该功能：`az storage container immutability-policy` 和 `az storage container legal-hold`。 对这些命令运行 `-h` 可查看命令。

### <a name="powershell"></a>PowerShell

Az.Storage 预览版模块支持不可变存储。  若要启用该功能，请执行以下步骤：

1. 确保已安装最新版本的 PowerShellGet：`Install-Module PowerShellGet –Repository PSGallery –Force`。
2. 删除以前安装的 Azure PowerShell。
3. 安装 Azure PowerShell：`Install-Module Az –Repository PSGallery –AllowClobber`。
4. 安装 Azure PowerShell 存储模块的预览版本：`Install-Module Az.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

本文稍后的[示例 PowerShell 代码](#sample-powershell-code)部分演示了该功能的用法。

## <a name="client-libraries"></a>客户端库

以下客户端库支持 Azure Blob 存储的不可变存储：

- [.NET 客户端库 7.2.0-preview 和更高版本](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node.js 客户端库 4.0.0 和更高版本](https://www.npmjs.com/package/azure-arm-storage)
- [Python 客户端库 2.0.0 候选发布版 2 和更高版本](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java 客户端库](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="supported-values"></a>支持的值

- 最小保留时间间隔为 1 天。 最大为 400 年。
- 对于某个存储帐户而言，使用锁定的不可变策略的最大容器数为 1,000。
- 对于某个存储帐户而言，使用法定保留设置的最大容器数为 1,000。
- 对于某个容器而言，最大法定保留标记数为 10。
- 法定保留标记的最大长度为 23 个字母数字字符。 最小长度为 3 个字符。
- 对于某个容器而言，锁定的不可变策略允许的最大保留时间间隔延长次数为 3。
- 对于使用锁定的不可变策略的容器而言，基于时间的保留策略日志的最大数目为 5，可以在容器生存期保留的法定保留策略日志的最大数目为 10。

## <a name="faq"></a>常见问题解答

**此功能是只适用于块 Blob，还是也适用于页 Blob 和追加 Blob？**

不可变存储可以用于任何 Blob 类型，但我们建议主要将其用于块 Blob。 与块 Blob 不同，页 Blob 和追加 Blob 需先在 WORM 容器外部创建，然后复制到容器中。 将这些 Blob 复制到 WORM 容器中后，就不再允许对追加 Blob 执行追加操作，也不允许对页 Blob 进行更改。

**是否始终需要创建新的存储帐户才能使用此功能？**

可将不可变存储与任何现有或新创建的常规用途 V2 或 Blob 存储帐户配合使用。 此功能仅适用于 Blob 存储。

**如果尝试删除某个容器，而该容器使用锁定的基于时间的保留策略或法定保留，会发生什么情况？**

如果至少存在一个 Blob 使用锁定的基于时间的保留策略或法定保留，则“删除容器”操作会失败。 仅当没有 Blob 存在活动的保留时间间隔，也没有法定保留时，“删除容器”操作才会成功。 必须先删除 Blob，然后才能删除容器。

**如果尝试删除的存储帐户有一个 WORM 容器，而该容器使用锁定的基于时间的保留策略或法定保留，会发生什么情况？**

如果至少有一个 WORM 容器有法定保留，或者有一个 Blob 有活动的保留时间间隔，则存储帐户删除操作会失败。  必须先删除所有 WORM 容器，然后才能删除存储帐户。 有关删除容器的信息，请查看上一个问题。

**当 Blob 处于不可变状态时，是否可以跨不同的 Blob 层（热、凉、冷）来移动数据？**

是的，可以在数据处于不可变状态时使用“设置 Blob 层”命令跨 Blob 层移动数据。 热、冷和存档 Blob 层均支持不可变存储。

**如果我无法付款，但我的保留时间间隔尚未到期，会发生什么情况？**

在未付款的情况下，会根据你与 Microsoft 签署的合同条款与条件应用常规的数据保留策略。

**你们是否为只想试用此功能的用户提供试用期或宽限期？**

是的。 基于时间的保留策略在首先创建时，将处于未锁定状态。 在这种状态下，可以对保留时间间隔进行所需的更改，例如延长或缩短保留时间间隔，甚至可以删除策略。 策略被锁定后，它将保持锁定状态，直到保留时间间隔到期为止。 这可以防止删除和修改保留时间间隔。 我们强烈建议仅在试用的情况下使用未锁定状态，并在 24 小时内锁定策略。 这种做法有助于遵守 SEC 17a-4(f) 及其他法规。

**此功能在国家/地区及政府云中是否可用？**

不可变存储目前仅在 Azure 公共区域中推出。 如果你对特定的国家云感兴趣，请向 azurestoragefeedback@microsoft.com 发送电子邮件。

## <a name="sample-powershell-code"></a>示例 PowerShell 代码

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

以下示例 PowerShell 脚本仅供参考。 此脚本创建新的存储帐户和容器。 然后，它演示如何设置和清除法定保留、创建和锁定基于时间的保留策略（也称为不可变策略），并延长保留时间间隔。

设置并测试 Azure 存储帐户：

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

设置并清除法定保留：

```powershell
# Set a legal hold
Add-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

创建或更新不可变策略：
```powershell
# with an account name or container name
Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

检索不可变策略：
```powershell
# Get an immutability policy
Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzStorageContainerImmutabilityPolicy -Container $containerObject
```

锁定不可变策略（添加 -Force 以关闭提示）：
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

扩展不可变策略：
```powershell

# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

删除不可变策略（添加 -Force 以关闭提示）：
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
