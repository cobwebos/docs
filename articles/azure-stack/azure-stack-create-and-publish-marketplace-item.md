---
title: "创建和发布 Azure 堆栈中的应用商店项 |Microsoft 文档"
description: "创建和发布 Azure 堆栈中的应用商店项。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 77e5f60c-a86e-4d54-aa8d-288e9a889386
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: erikje
ms.openlocfilehash: 64203ce186665aada98fbe8daed971164a650399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-publish-a-marketplace-item"></a>创建和发布应用商店项目

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

## <a name="create-a-marketplace-item"></a>创建 Marketplace 项目
1. [下载](http://www.aka.ms/azurestackmarketplaceitem)Azure 库 Packager 工具和示例 Azure 堆栈应用商店项。
2. 打开示例应用商店项，然后重命名**SimpleVMTemplate**文件夹。 (例如，为你的应用商店项-使用相同的名称**Contoso.TodoList**。)此文件夹包含：
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [创建 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)或从 GitHub 中选择模板。 应用商店项使用此模板创建资源。
4. 若要确保可以成功部署资源，测试与 Microsoft Azure 堆栈 Api 模板。
5. 如果你的模板依赖于虚拟机映像，请按照说明[将虚拟机映像添加到 Azure 堆栈](azure-stack-add-vm-image.md)。
6. 保存你的 Azure 资源管理器模板中**/Contoso.TodoList/DeploymentTemplates/**文件夹。
7. 选择图标和应用商店项的文本。 添加图标添加到**图标**文件夹，并将文本添加到**资源**文件中**字符串**文件夹。 使用图标 Small、 Medium、 Large 和范围的命名约定。 请参阅[应用商店项目用户界面参考](#reference-marketplace-item-ui)有关的详细说明。
   
   > [!NOTE]
   > 所有四个图标大小 （小、 中、 大，范围） 所需的正确构建应用商店项。
   > 
   > 
8. 在**manifest.json**文件中，更改**名称**到应用商店项的名称。 此外更改**发布服务器**到你的名称或公司。
9. 下**项目**，更改**名称**和**路径**到包含你的 Azure 资源管理器模板的正确信息。
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. 替换**我的应用商店项**的应用商店项应出现的位置的类别的列表。
    
             "categories":[
                 "My Marketplace Items"
              ],
11. 有关 manifest.json 任何进一步的编辑，请参阅[引用： 应用商店项 manifest.json](#reference-marketplace-item-manifestjson)。
12. 若要打包到.azpkg 文件的文件夹，打开命令提示符并运行以下命令：
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > 输出文件包的完整路径必须存在。 例如，如果输出路径，C:\MarketPlaceItem\yourpackage.azpkg 文件夹 C:\MarketPlaceItem 必须存在。
    > 
    > 

## <a name="publish-a-marketplace-item"></a>发布 Marketplace 项目
1. 使用 PowerShell 或 Azure 存储资源管理器来将你的应用商店项 (.azpkg) 上载到 Azure Blob 存储。 你可以将上载到 Azure 堆栈的本地存储或上载到 Azure 存储。 （它是包的临时位置。）请确保 blob 是可公开访问。
2. 在客户端虚拟机在 Microsoft Azure 堆栈环境中，确保使用你的服务管理员凭据设置你的 PowerShell 会话。 你可以找到有关如何对 Azure 中的堆栈中的 PowerShell 进行身份验证的说明[部署具有 PowerShell 的模板](user/azure-stack-deploy-template-powershell.md)。
3. 使用**添加 AzureRMGalleryItem** PowerShell cmdlet 以将应用商店项目发布到 Azure 堆栈。 例如：
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | 参数 | 说明 |
   | --- | --- |
   | 订阅 ID |管理员的订阅 id。 你可以使用 PowerShell 进行检索。 如果你想要获取门户中，转到提供程序订阅和复制订阅 id。 |
   | GalleryItemUri |为你已上载到存储空间的库包中的 blob URI。 |
   | Apiversion |将设置为**2015年-04-01**。 |
4. 转到门户。 作为一个操作员或用户，你现在可以看到在门户中的应用商店项。
   
   > [!NOTE]
   > 包可能需要几分钟才能显示。
   > 
   > 
5. 现在已到 Azure 堆栈 Marketplace 保存你的应用商店项。 你可以选择删除 Blob 存储位置。
6. 你可以使用删除的应用商店项**删除 AzureRMGalleryItem** cmdlet。 示例：
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > Marketplace UI 可能会显示错误后移除一个项。 若要修复此错误，请单击**设置**在门户中。 然后，选择**放弃所做的修改**下**门户自定义**。
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>参考： 应用商店项 manifest.json
### <a name="identity-information"></a>标识信息
| 名称 | 必选 | 类型 | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| 名称 |X |String |[A-Za-z0-9] + | |
| 发布者 |X |String |[A-Za-z0-9] + | |
| 版本 |X |String |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadata
| 名称 | 必选 | 类型 | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |80 个字符的建议 |如果它的长度大于 80 个字符门户不可能正常显示项目名称。 |
| PublisherDisplayName |X |String |建议的 30 个字符 |如果它的长度超过 30 个字符门户不可能正常显示你的发布服务器名称。 |
| PublisherLegalName |X |String |最多 256 个字符 | |
| 摘要 |X |String |60 到 100 个字符 | |
| LongSummary |X |String |140 到 256 个字符 |不尚未 Azure 堆栈中适用。 |
| 说明 |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |至 5,000 台 500 个字符 | |

### <a name="images"></a>映像
应用商店使用以下图标：

| 名称 | 宽度 | 高度 | 说明 |
| --- | --- | --- | --- |
| 宽 |255 px |115 px |始终需要 |
| 大型 |115 px |115 px |始终需要 |
| 中型 |90 px |90 px |始终需要 |
| 小型 |40 px |40 px |始终需要 |
| 屏幕快照 |533 px |32 px |可选 |

### <a name="categories"></a>Categories
应使用标识在门户 UI 上出现的项的类别标记每个应用商店项。 你可以选择现有类别之一中 Azure 堆栈 (计算、 数据 + 存储，等) 或选择新的密码。

### <a name="links"></a>链接
每个应用商店项目可以包含各种指向其他内容。 链接指定为名称和 Uri 的列表。

| 名称 | 必选 | 类型 | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最多为 64 个字符 | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>其他属性
除了前面的元数据，应用商店作者可以提供自定义的键/值对数据采用以下形式：

| 名称 | 必选 | 类型 | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最大值为 25 个字符 | |
| 值 |X |String |最多 30 个字符 | |

### <a name="html-sanitization"></a>HTML 进程清理
对于允许 HTML 任何字段，请允许以下元素和属性：

h1，h2，h3，h4，h5，p、 ol、 ul、 li，[目标 | href]，巴西，强，em，b，我

## <a name="reference-marketplace-item-ui"></a>参考： 应用商店项 UI
图标和应用商店项 Azure 堆栈门户中所示的文本如下所示。

### <a name="create-blade"></a>“创建”边栏选项卡
![“创建”边栏选项卡](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>应用商店项详细信息边栏选项卡
![应用商店项详细信息边栏选项卡](media/azure-stack-marketplace-item-ui-reference/image3.png)

