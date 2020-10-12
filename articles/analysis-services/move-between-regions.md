---
title: 将 Azure Analysis Services 移到其他区域 |Microsoft Docs
description: 描述如何将 Azure Analysis Services 资源移到不同的区域。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 06/09/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 4844f3e34a6b49559affbb4d4ed7bc5b5e38e538
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050366"
---
# <a name="move-analysis-services-to-a-different-region"></a>将 Analysis Services 移到不同的区域

本文介绍如何将 Analysis Services 服务器资源移到不同的 Azure 区域。 你可能会出于多种原因而将服务器移动到其他区域，例如，利用更接近用户的 Azure 区域、仅使用特定区域中支持的服务计划，或者满足内部策略和监管要求。 

在此和关联的链接项目中，您将了解如何：

> [!div class="checklist"]
> 
> * 将源服务器模型数据库备份到 [Blob 存储](../storage/blobs/storage-blobs-introduction.md)。
> * 导出源服务器 [资源模板](../azure-resource-manager/templates/overview.md)。
> * 获取存储 [共享访问签名 (SAS) ](../storage/common/storage-sas-overview.md)。
> * 修改资源模板。
> * 部署模板以创建新的目标服务器。
> * 将模型数据库还原到新的目标服务器。
> * 验证新的目标服务器和数据库。
> * 删除源服务器。

本文介绍如何使用资源模板将具有 **基本配置** 的单一 Analysis Services 服务器迁移到同一订阅中的不同区域 *和* 资源组。 使用模板将保留配置的服务器属性，确保使用与源服务器相同的属性（区域和资源组除外）配置目标服务器。 本文未介绍如何移动可能属于相同资源组（如数据源、存储和网关资源）的关联资源。 

将服务器移到其他区域之前，建议创建详细计划。 考虑其他可能需要移动的资源，例如网关和存储。 对于任何计划，在移动生产服务器之前，请务必使用测试服务器完成一个或多个试验性移动操作。

> [!IMPORTANT]
> 客户端应用程序和连接字符串使用完整服务器名称（包括服务器所在的区域的 Uri）连接到 Analysis Services。 例如，`asazure://westcentralus.asazure.windows.net/advworks01`。 将服务器移到其他区域时，会有效地在不同的区域中创建新的服务器资源，这将在服务器名称 Uri 中具有不同的区域。 在脚本中使用的客户端应用程序和连接字符串必须使用新的服务器名称 Uri 连接到新服务器。 使用 [服务器名称别名](analysis-services-server-alias.md) 可以减少服务器名称 Uri 必须更改的位置数，但必须在区域移动之前实现。

> [!IMPORTANT]
> Azure 区域使用不同的 IP 地址范围。 如果为服务器和/或存储帐户所在的区域配置了防火墙例外，则可能需要配置其他 IP 地址范围。 若要了解详细信息，请参阅 [Analysis Services 网络连接](analysis-services-network-faq.md)的常见问题。

> [!NOTE]
> 本文介绍如何通过源服务器区域中的存储容器将数据库备份还原到目标服务器。 在某些情况下，从不同的区域还原备份可能会降低性能，尤其是对于大型数据库。 为了在数据库还原期间获得最佳性能，请在目标服务器区域中迁移或创建一个新的存储容器。 将数据库还原到目标服务器之前，请将 .abf 备份文件从源区域存储容器复制到目标区域存储容器。 在某些情况下（在某些情况下，尤其是对于非常大的数据库）的范围超出范围时，在目标服务器上重新创建数据库，然后在目标服务器上重新创建数据库，然后在目标服务器上进行处理以加载数据库数据可能比使用备份/还原更为经济高效。

> [!NOTE]
> 如果使用本地数据网关连接到数据源，则还必须将网关资源移至目标服务器区域。 若要了解详细信息，请参阅 [安装和配置本地数据网关](analysis-services-gateway-install.md)。

## <a name="prerequisites"></a>必备条件

- **Azure 存储帐户**：存储 .abf 备份文件时需要此文件。
- **SQL Server Management Studio (SSMS) **：备份和还原模型数据库所必需的。
- **Azure PowerShell**。 仅当你选择使用 PowerShell 来完成此任务时才是必需的。

## <a name="prepare"></a>准备

### <a name="backup-model-databases"></a>备份模型数据库

如果尚未为源服务器配置 **存储设置** ，请按照 [配置存储设置](analysis-services-backup.md#configure-storage-settings)中的步骤操作。

配置存储设置后，请按照 [备份](analysis-services-backup.md#backup) 中的步骤在存储容器中创建模型 .abf 备份。 稍后将 .abf 备份还原到新的目标服务器。


### <a name="export-template"></a>导出模板

模板包含源服务器的配置属性。

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 Azure 门户导出模板：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择 " **所有资源**"，然后选择 Analysis Services 服务器。

3. 选择“设置” > “导出模板”。 

4. 选择“导出模板”边栏选项卡中的“下载”。 

5. 找到从门户下载的 .zip 文件，然后将该文件解压缩到文件夹。

   Zip 文件包含包含部署新服务器所需的模板和参数的 json 文件。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 导出模板：

1. 使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登录到 Azure 订阅，然后按屏幕说明操作：

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为要移动的服务器资源的订阅。

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 导出源服务器的模板。 这些命令将 ResourceGroupName as filename 形式的 json 模板保存到当前目录中。

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>获取存储共享访问签名 (SAS) 

从模板部署目标服务器时，用户需要将 SAS 令牌 (为 Uri) 才能指定包含数据库备份的存储容器。

# <a name="portal"></a>[门户](#tab/azure-portal)

使用门户获取共享访问签名：

1. 在门户中，选择用于备份服务器数据库的存储帐户。

2. 选择 " **存储资源管理器**"，然后展开 " **BLOB 容器**"。 

3. 右键单击存储容器，然后选择 " **获取共享访问签名**"。

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="获取 SAS&quot;:::

4. 在 &quot; **共享访问签名**&quot; 中，选择 " **创建**"。 默认情况下，SAS 将在24小时后过期。

5. 复制并保存 **URI**。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 获取共享访问签名，请按照 [使用 Powershell 创建容器或 blob 的用户委托 SAS](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)中的步骤操作。

---

### <a name="modify-the-template"></a>修改模板

使用文本编辑器修改所导出文件的 template.js，并更改区域和 blob 容器属性。 

修改模板：

1. 在文本编辑器的 " **位置** " 属性中，指定新的目标区域。 在 **backupBlobContainerUri** 属性中，粘贴存储容器 URI 和 SAS 密钥。 

    下面的示例将服务器 advworks1 的目标区域设置为 `South Central US` ，并使用共享访问签名指定存储容器 Uri： 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. 保存模板。

#### <a name="regions"></a>区域

若要获取 Azure 区域，请参阅 [azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 若要使用 PowerShell 获取区域，请运行 [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 命令。

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>移动

若要在不同的区域中部署新的服务器资源，你将使用之前部分中导出和修改的文件的 **template.js** 。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在门户中，选择 " **创建资源**"。

2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 

3. 选择“模板部署”。

4. 选择“创建” 。

5. 选择“在编辑器中生成自己的模板”。

6. 选择 " **加载文件**"，然后按照说明在导出和修改的文件 ** 上加载template.js** 。

7. 验证模板编辑器是否显示了您的新目标服务器的正确属性。

8. 选择“保存”。

9. 输入或选择属性值：

    - **订阅**：选择 Azure 订阅。
    
    - **资源组**：选择 " **新建**"，然后输入资源组名称。 如果现有资源组尚未包含具有相同名称的 Analysis Services 服务器，则可以选择该资源组。
    
    - **位置**：选择在模板中指定的相同区域。

10. 选择“查看并创建”。

11. 查看条款和基础知识，然后选择 " **创建**"。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用以下命令部署模板：

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>获取目标服务器 Uri

若要从 SSMS 连接到新的目标服务器以还原 model 数据库，需要获取新的目标服务器 Uri。

# <a name="portal"></a>[门户](#tab/azure-portal)

若要在门户中获取服务器 Uri：

1. 在门户中，切换到新的目标服务器资源。

2. 在 " **概述** " 页上，复制 " **服务器名称** Uri"。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 获取服务器 Uri，请使用以下命令：

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
从输出复制 **ServerFullName** 。

---

### <a name="restore-model-database"></a>还原模型数据库

按照 [restore](analysis-services-backup.md#restore) 中所述的步骤将 model .abf 备份还原到新的目标服务器。

可选：在还原 model 数据库后，处理模型和表以刷新数据源中的数据。 使用 SSMS 处理模型和表：

1. 在 SSMS 中，右键单击模型数据库 > **处理数据库**"。

2. 展开 " **表**"，右键单击表。 在 " **进程" 表中 (s) **，选择 "所有表"，然后选择 **"确定"**。

## <a name="verify"></a>验证

1. 在门户中，切换到新的目标服务器。

2. 在 "概述" 页上 **Analysis Services 服务器上的模型**"中，验证是否显示了还原的模型。

3. 使用 Power BI 或 Excel 之类的客户端应用程序连接到新服务器上的模型。 验证是否显示了模型对象，如表、度量值和层次结构。 

4. 运行任何自动化脚本。 验证它们是否已成功执行。

可选： [ALM 工具包](http://alm-toolkit.com/) 是一个 *开源* 工具，用于比较和管理 Power BI 数据集 *和* Analysis Services 表格模型数据库。 使用工具包连接到源服务器和目标服务器数据库，并进行比较。 如果数据库迁移成功，模型对象将具有相同的定义。 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="获取 SAS&quot;:::

4. 在 &quot; **共享访问签名**&quot; 中，选择 ":::

## <a name="clean-up-resources"></a>清理资源

在验证客户端应用程序可以连接到新服务器并且任何自动化脚本正确执行之后，请删除源服务器。 

# <a name="portal"></a>[门户](#tab/azure-portal)

若要从门户中删除源服务器：

在源服务器的 " **概述** " 页中，选择 " **删除**"。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 删除源服务器，请使用 Remove-AzAnalysisServicesServer 命令。

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> 完成区域移动后，建议新的目标服务器在同一区域中使用存储容器进行备份，而不是使用源服务器区域中的存储容器。 
