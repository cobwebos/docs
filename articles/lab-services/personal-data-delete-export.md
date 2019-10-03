---
title: 如何从 Azure 开发测试实验室中删除和导出个人数据 | Microsoft Docs
description: 了解如何从 Azure 开发测试实验室服务中删除和导出个人数据，以帮助履行一般数据保护条例 (GDPR) 中规定的义务。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 82ab8ef2e444b71f41fbbd87e4e9f8669e83e508
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371170"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>从 Azure 开发测试实验室中导出或删除个人数据
本文提供从 Azure 开发测试实验室服务中删除和导出个人数据的步骤。 

## <a name="what-personal-data-does-devtest-labs-collect"></a>开发测试实验室收集哪些个人数据？
开发测试实验室主要从用户收集两项个人数据， 分别是：用户电子邮件地址和用户对象 ID。 此信息至关重要，使该服务能够为实验室管理员和实验室用户提供服务内功能。

### <a name="user-email-address"></a>用户电子邮件地址
开发测试实验室使用用户电子邮件地址向实验室用户发送自动关机电子邮件通知。 该电子邮件告知用户其计算机即将关闭。 如果需要，用户可以推迟或跳过关机。 在实验室级别或 VM 级别配置电子邮件地址。

**在实验室级别设置电子邮件：**

![在实验室级别设置电子邮件](./media/personal-data-delete-export/lab-user-email.png)

**在 VM 级别设置电子邮件：**

![在 VM 级别设置电子邮件](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>用户对象 ID
开发测试实验室使用用户对象 ID 向实验室管理员显示每月成本趋势和资源成本信息。 这样，管理员便可以跟踪成本并管理其实验室的阈值。 

**当前日历月份的估计成本趋势：** 
![当前日历月份的估计成本趋势](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**资源的估计本月至今成本：** 
![资源的估计本月至今成本](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>我们为何需要这些个人数据？
开发测试实验室服务需要使用个人数据才能正常运行。 该服务必须使用这些数据来提供关键功能。 如果针对用户电子邮件地址设置了保留策略，则从我们的系统中删除实验室用户的电子邮件地址后，他们不会及时收到自动关机电子邮件通知。 同样，如果根据保留策略删除了用户对象 ID，实验室管理员将无法查看其实验室中计算机的每月成本趋势和资源成本。 因此，只要用户的资源在实验室中保持活动状态，就需要一直保留此数据。

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>如何让系统忘记我的个人数据？
如果实验室用户想要删除这些个人数据，可以删除实验室中的相应资源。 在用户删除个人数据后，开发测试实验室服务会将已删除的数据匿名化 30 天。

例如，如果你删除了 VM 或电子邮件地址，则删除该资源后，开发测试实验室服务会将此数据匿名化 30 天。 删除后保留 30 天的策略旨在确保向实验室管理员提供准确的每月成本预测。

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>如何请求导出个人数据？
你可以使用 Azure 门户或 PowerShell 导出个人和实验室使用情况数据。 数据将导出为两个不同的 CSV 文件:

- **磁盘 .csv** -包含有关不同 vm 正在使用的磁盘的信息
- **virtualmachines** -包含有关实验室中的 vm 的信息。

### <a name="azure-portal"></a>Azure 门户
实验室用户可以请求导出开发测试实验室服务存储的个人数据。 若要请求导出，请在实验室的“概述”页上导航到“个人数据”选项。 选择“请求导出”按钮，开始在实验室管理员的存储帐户中创建可下载的 Excel 文件。 然后，可以联系实验室管理员查看此数据。

1. 在左侧菜单中选择“个人数据”。 

    ![“个人数据”页](./media/personal-data-delete-export/personal-data-page.png)
2. 选择包含实验室的**资源组**。

    ![选择资源组](./media/personal-data-delete-export/select-resource-group.png)
3. 在该资源组中选择**存储帐户**。
4. 在“存储帐户”页上，选择“Blob”。

    ![选择“Blob”磁贴](./media/personal-data-delete-export/select-blobs-tile.png)
5. 在容器列表中选择名为 **labresourceusage** 的容器。

    ![选择 Blob 容器](./media/personal-data-delete-export/select-blob-container.png)
6. 选择与实验室同名的**文件夹**。 在此文件夹中可以看到与实验室中的**磁盘**和**虚拟机**对应的 **csv** 文件。 可以下载这些 csv 文件，筛选请求访问的实验室用户的内容，然后与他们共享这些内容。

    ![下载 CSV 文件](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

上述示例中的关键组件是:

- AzureRmResourceAction 命令。
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- 两个操作参数
    - **blobStorageAbsoluteSasUri** -具有共享访问签名 (SAS) 令牌的存储帐户 URI。 在 PowerShell 脚本中, 可以传递此值, 而不是存储密钥。
    - **usageStartDate** -要提取数据的开始日期, 结束日期是执行操作的当前日期。 粒度为日级别, 因此即使添加了时间信息, 它也会被忽略。

### <a name="exported-data---a-closer-look"></a>导出的数据-详细了解
现在, 让我们详细了解导出的数据。 如前所述, 数据成功导出后, 将有两个 CSV 文件。 

**Virtualmachines**包含下列数据列:

| 列名 | 描述 |
| ----------- | ----------- | 
| SubscriptionId | 实验室所在的订阅标识符。 |
| LabUId | 实验室的唯一 GUID 标识符。 |
| LabName | 实验室的名称。 |
| LabResourceId | 完全限定的实验室资源 ID。 |
| ResourceGroupName | 包含 VM 的资源组的名称 | 
| resourceId | VM 的完全限定的资源 ID。 |
| ResourceUId | VM 的 GUID |
| 名称 | 虚拟机名称。 |
| CreatedTime | VM 的创建日期时间。 |
| DeletedDate | VM 的删除日期时间。 如果此为空, 则尚未执行删除操作。 |
| ResourceOwner | VM 的所有者。 如果该值为空, 则它可以是可认领 VM, 也可以由服务主体创建。 |
| PricingTier | VM 的定价层 |
| ResourceStatus | VM 的可用性状态。 处于活动状态, 如果已删除 VM, 则为; 如果仍存在, 则为非活动状态。 |
| ComputeResourceId | 完全限定的虚拟机计算资源标识符。 |
| 可认领 | 如果 VM 是可认领 VM, 则设置为 true | 
| EnvironmentId | 在其中创建虚拟机的环境资源标识符。 如果未将 VM 创建为环境资源的一部分, 则为空。 |
| ExpirationDate | VM 的到期日期。 如果尚未设置过期日期, 则将其设置为空。
| GalleryImageReferenceVersion |  VM 基本映像的版本。 |
| GalleryImageReferenceOffer | VM 基本映像的产品/服务。 |
| GalleryImageReferencePublisher | VM 基本映像的发布者。 |
| GalleryImageReferenceSku | VM 基本映像的 Sku |
| GalleryImageReferenceOsType | VM 基本映像的 OS 类型 |
| CustomImageId | VM 基本自定义映像的完全限定 ID。 |

下面列出了在 node.js**中包含**的数据列:

| 列名 | 描述 | 
| ----------- | ----------- | 
| SubscriptionId | 包含实验室的订阅的 ID |
| LabUId | 实验室的 GUID |
| LabName | 实验室的名称 | 
| LabResourceId | 实验室的完全限定的资源 ID | 
| ResourceGroupName | 包含实验室的资源组的名称 | 
| resourceId | VM 的完全限定的资源 ID。 |
| ResourceUId | VM 的 GUID |
 |名称 | 附加的磁盘的名称 |
| CreatedTime |数据磁盘的创建日期和时间。 |
| DeletedDate | 数据磁盘的删除日期和时间。 |
| ResourceStatus | 资源的状态。 如果资源存在, 则为 "活动"。 非活动 (删除时)。 |
| DiskBlobName | 数据磁盘的 Blob 名称。 |
| DiskSizeGB | 数据磁盘的大小。 |
| DiskType | 数据磁盘的类型。 对于标准, 1 表示高级。 |
| LeasedByVmId | 数据磁盘附加到的 VM 的资源 ID。 |


> [!NOTE]
> 如果要处理多个实验室并想要获取整体信息, 两个关键列是**LabUID**和**ResourceUId**, 它们是订阅之间的唯一 id。

导出的数据可以使用工具 (如 SQL Server、Power BI 等) 进行操作和可视化。当你想要将实验室的使用情况报告给你的管理团队时, 此功能特别有用。

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [设置实验室的策略](devtest-lab-get-started-with-lab-policies.md)
- [常见问题](devtest-lab-faq.md)
