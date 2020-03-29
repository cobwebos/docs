---
title: 如何从 Azure 开发人员测试实验室删除和导出个人数据
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169684"
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

**当前日历月的估计成本趋势：**
![当前日历月的估计成本趋势](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**按资源估算的月至今成本：**
![按资源估算的月至今成本](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>我们为何需要这些个人数据？
开发测试实验室服务需要使用个人数据才能正常运行。 该服务必须使用这些数据来提供关键功能。 如果针对用户电子邮件地址设置了保留策略，则从我们的系统中删除实验室用户的电子邮件地址后，他们不会及时收到自动关机电子邮件通知。 同样，如果根据保留策略删除了用户对象 ID，实验室管理员将无法查看其实验室中计算机的每月成本趋势和资源成本。 因此，只要用户的资源在实验室中保持活动状态，就需要一直保留此数据。

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>如何让系统忘记我的个人数据？
如果实验室用户想要删除这些个人数据，可以删除实验室中的相应资源。 在用户删除个人数据后，开发测试实验室服务会将已删除的数据匿名化 30 天。

例如，如果你删除了 VM 或电子邮件地址，则删除该资源后，开发测试实验室服务会将此数据匿名化 30 天。 删除后保留 30 天的策略旨在确保向实验室管理员提供准确的每月成本预测。

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>如何请求导出个人数据？
您可以使用 Azure 门户或 PowerShell 导出个人和实验室使用情况数据。 数据导出为两个不同的 CSV 文件：

- **disks.csv** - 包含有关不同 VM 正在使用的磁盘的信息
- **虚拟机.csv** - 包含有关实验室中 VM 的信息。

### <a name="azure-portal"></a>Azure 门户
实验室用户可以请求导出开发测试实验室服务存储的个人数据。 若要请求导出，请在实验室的“概述”页上导航到“个人数据”选项。******** 选择“请求导出”按钮，开始在实验室管理员的存储帐户中创建可下载的 Excel 文件。**** 然后，可以联系实验室管理员查看此数据。

1. 在左侧菜单中选择“个人数据”。**** 

    ![“个人数据”页](./media/personal-data-delete-export/personal-data-page.png)
2. 选择包含实验室的**资源组**。

    ![选择资源组](./media/personal-data-delete-export/select-resource-group.png)
3. 在该资源组中选择**存储帐户**。
4. 在“存储帐户”页上，选择“Blob”。********

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

上述示例中的关键组件包括：

- 调用 AzureRm 资源操作命令。
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- 两个操作参数
    - **blob 存储绝对Sasuri** - 具有共享访问签名 （SAS） 令牌的存储帐户 URI。 在 PowerShell 脚本中，可以传入此值，而不是存储密钥。
    - **使用开始日期**- 提取数据的开始日期，结束日期为执行操作的当前日期。 粒度在日级，因此即使您添加时间信息，也会忽略该信息。

### <a name="exported-data---a-closer-look"></a>导出的数据 - 仔细观察
现在，让我们仔细看看导出的数据。 如前所述，一旦数据成功导出，将有两个 CSV 文件。 

**虚拟机.csv**包含以下数据列：

| 列名称 | 描述 |
| ----------- | ----------- | 
| SubscriptionId | 实验室存在的订阅标识符。 |
| 拉布伊德 | 实验室的唯一 GUID 标识符。 |
| 实验室名称 | 实验室的名称。 |
| 实验室资源Id | 完全合格的实验室资源 ID。 |
| ResourceGroupName | 包含 VM 的资源组的名称 | 
| ResourceId | VM 完全限定的资源 ID。 |
| 资源库ID | VM 的 GUID |
| “属性” | 虚拟机名称。 |
| CreatedTime | 创建 VM 的日期和时间。 |
| DeletedDate | 删除 VM 的日期和时间。 如果为空，则尚未发生删除。 |
| 资源所有者 | VM 的所有者。 如果该值为空，则该值要么是可声明 VM，要么是由服务主体创建的。 |
| 定价层 | VM 的定价层 |
| 资源状态 | VM 的可用性状态。 如果 VM 已删除，则处于活动状态（如果仍然存在或处于非活动状态）。 |
| 计算资源 Id | 完全合格的虚拟机计算资源标识符。 |
| 可索赔 | 如果 VM 是可声明的 VM，则设置为 true | 
| 环境Id | 在其中创建虚拟机的环境资源标识符。 当 VM 不是作为环境资源的一部分创建时，它是空的。 |
| ExpirationDate | VM 的到期日期。 如果尚未设置到期日期，则将其设置为空。
| 库图像参考版本 |  VM 基本映像的版本。 |
| 画廊图片参考提供 | 提供 VM 基本映像。 |
| 画廊图片参考发布者 | VM 基本映像的发布者。 |
| 画廊图片参考库 | VM 基本映像的 Sku |
| 库图像参考类型 | VM 基本映像的操作系统类型 |
| 自定义图像 Id | VM 基本自定义映像的完全限定 ID。 |

**磁盘.csv**中包含的数据列如下所示：

| 列名称 | 描述 | 
| ----------- | ----------- | 
| SubscriptionId | 包含实验室的订阅 ID |
| 拉布伊德 | 实验室 GUID |
| 实验室名称 | 实验室名称 | 
| 实验室资源Id | 实验室完全合格的资源 ID | 
| ResourceGroupName | 包含实验室的资源组的名称 | 
| ResourceId | VM 完全限定的资源 ID。 |
| 资源库ID | VM 的 GUID |
 |“属性” | 连接磁盘的名称 |
| CreatedTime |创建数据磁盘的日期和时间。 |
| DeletedDate | 删除数据磁盘的日期和时间。 |
| 资源状态 | 资源的状态。 如果资源存在，则处于活动状态。 删除时处于非活动状态。 |
| 磁盘Blob名称 | 数据磁盘的 Blob 名称。 |
| 磁盘大小GB | 数据磁盘的大小。 |
| DiskType | 数据磁盘的类型。 0 表示标准，1 表示高级。 |
| 租赁ByVmId | 已附加到数据磁盘的 VM 的资源 ID。 |


> [!NOTE]
> 如果您正在处理多个实验室，并且想要获取总体信息，则两个关键列是**LabUID**和**ResourceUID，** 它们是跨订阅的唯一 ID。

可以使用 SQL Server、Power BI 等工具操作和可视化导出的数据。当您要向可能使用与 Azure 订阅相同的 Azure 订阅的管理团队报告实验室的使用情况时，此功能特别有用。

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [设置实验室的策略](devtest-lab-get-started-with-lab-policies.md)
- [常见问题](devtest-lab-faq.md)
