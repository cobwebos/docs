---
title: 将 Azure 存储帐户移到另一个区域 | Microsoft Docs
description: 介绍如何将 Azure 存储帐户移到另一个区域。
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838707"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>将 Azure 存储帐户移到另一个区域

若要移动某个存储帐户，请在另一个区域中创建该存储帐户的副本。 然后，使用 AzCopy 或其他所选工具将数据移到该帐户。

本文将介绍如何执行以下操作：

> [!div class="checklist"]
> 
> * 导出模板。
> * 通过添加目标区域和存储帐户名称来修改模板。
> * 部署该模板以创建新的存储帐户。
> * 配置新的存储帐户。
> * 将数据移到新的存储帐户。
> * 删除源区域中的资源。

## <a name="prerequisites"></a>先决条件

- 确保帐户使用的服务和功能在目标区域中受支持。

- 对于预览版功能，请确保你的订阅已列入目标区域的白名单。

<a id="prepare" />

## <a name="prepare"></a>准备

若要开始，请导出然后修改某个资源管理器模板。 

### <a name="export-a-template"></a>导出模板

此模板包含描述存储帐户的设置。 

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

若要使用 Azure 门户导出模板：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“所有资源”，然后选择你的存储帐户。

3. 选择“设置” **“导出模板”。**  > 

4. 选择“导出模板”边栏选项卡中的“下载”。

5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。

   此 zip 文件包含构成模板的 .json 文件以及用于部署模板的脚本。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 导出模板：

1. 使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅，然后按屏幕说明操作：

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. 如果你的标识已关联到多个订阅，请将活动订阅设置为要移动的存储帐户的订阅。

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 导出源存储帐户的模板。 这些命令会将某个 JSON 模板保存到当前目录。

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>修改模板 

通过更改存储帐户名称和区域来修改模板。

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

若要使用 Azure 门户部署模板：

1. 在 Azure 门户中，选择“创建资源”。

2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。

3. 选择“模板部署”。

    ![Azure 资源管理器模板库](./media/storage-account-move/azure-resource-manager-template-library.png)

4. 选择“创建”。

5. 选择“在编辑器中生成自己的模板”。

6. 选择“加载文件”，然后按说明加载在上一部分下载的 **template.json** 文件。

7. 在 **template.json** 文件中，通过设置存储帐户名称的默认值来为目标存储帐户命名。 此示例将存储帐户名称的默认值设置为 `mytargetaccount`。
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    若要获取地区位置代码，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域的代码是不带空格的区域名称，"**美国中部** = **centralus**"。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 部署模板：

1. 在 **template.json** 文件中，通过设置存储帐户名称的默认值来为目标存储帐户命名。 此示例将存储帐户名称的默认值设置为 `mytargetaccount`。
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. 将 **template.json** 文件中的 **location** 属性编辑为目标区域。 此示例将目标区域设置为 `eastus`。

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    可以运行 [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 命令获取区域代码。

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>移动

部署该模板，以在目标区域中创建新的存储帐户。 

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

1. 保存 **template.json** 文件。

2. 输入或选择属性值：

- **订阅**：选择一个 Azure 订阅。

- **资源组**：选择“新建”，为资源组指定名称。

- **位置**：选择 Azure 位置。

3. 单击“我同意上述条款和条件”复选框，然后单击“选择购买”按钮。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. 使用 [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) 获取要在其中部署目标公共 IP 的订阅 ID：

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. 使用以下命令部署模板：

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>配置新的存储帐户

某些功能不会导出到模板，因此必须将其添加到新的存储帐户。 

下表列出了这些功能，以及有关将其添加到新存储帐户的指导。

| Feature    | 指南    |
|--------|-----------|
| **生命周期管理策略** | [管理 Azure Blob 存储生命周期](../blobs/storage-lifecycle-management-concepts.md) |
| **静态网站** | [在 Azure 存储中托管静态网站](../blobs/storage-blob-static-website-how-to.md) |
| **事件订阅** | [响应 Blob 存储事件](../blobs/storage-blob-event-overview.md) |
| **警报** | [使用 Azure Monitor 创建、查看和管理活动日志警报](../../azure-monitor/platform/alerts-activity-log.md) |
| **内容交付网络（CDN）** | [使用 Azure CDN 通过 HTTPS 访问包含自定义域的 blob](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> 如果为源存储帐户设置 CDN，只需将现有 CDN 的源更改为新帐户的主 blob 服务终结点（或主静态网站终结点）。 

### <a name="move-data-to-the-new-storage-account"></a>将数据移到新的存储帐户

下面是移动数据的一些方法。

： heavy_check_mark： **Azure 存储资源管理器**

  此工具易于使用，适合用于小型数据集。 可以复制容器和文件共享，然后将其粘贴到目标帐户中。

  请参阅 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)；

： heavy_check_mark： **AzCopy**

  这是首选方法。 此方法已针对性能进行优化。  速度较快的原因之一是数据直接在存储服务器之间复制。因此，AzCopy 不会占用计算机的网络带宽。 可在命令行或自定义脚本中使用 AzCopy。

  请参阅 [AzCopy 入门](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

： heavy_check_mark： **Azure 数据工厂** 

  仅当当前的 AzCopy 版本不支持所需的功能时，才使用此工具。 例如，在当前的 AzCopy 版本中，无法在使用分层命名空间的帐户之间复制 Blob。 另外，AzCopy 不会保留文件访问控制列表或文件时间戳（例如：创建和修改时间戳）。 

  请参阅以下链接：
  - [使用 Azure 数据工厂向/从 Azure Blob 存储复制数据](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [使用 Azure 数据工厂向/从 Azure Data Lake Storage Gen2 复制数据](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [使用 Azure 数据工厂从/向 Azure 文件存储复制数据](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [使用 Azure 数据工厂向/从 Azure 表存储复制数据](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>丢弃或清理

部署后，如果你想要从头开始，可以删除目标存储帐户，然后重复本文中的[准备](#prepare)和[移动](#move)部分所述的步骤。

若要提交更改并完成存储帐户的移动，请删除源存储帐户。

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

若要使用 Azure 门户删除存储帐户：

1. 在 Azure 门户中展开左侧的菜单打开服务菜单，然后选择“存储帐户”以显示存储帐户的列表。

2. 找到要删除的目标存储帐户，并右键单击列表右侧的“更多”按钮 ( **...** )。

3. 选择“删除”并确认。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

若要删除资源组及其关联的资源（包括新的存储帐户），请使用 [Remove-AzStorageAccount](/powershell/module/az.resources/remove-azstorageaccount) 命令：

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>后续步骤

在本教程中，你已将一个 Azure 存储帐户从一个区域移到了另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：


- [将资源移到新资源组或订阅中](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [将 Azure VM 移到另一区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
