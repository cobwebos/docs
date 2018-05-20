---
title: 在 Azure Stack 中创建和发布 Marketplace 项 | Microsoft Docs
description: 在 Azure Stack 中创建和发布 Marketplace 项。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5e0349d6bae9295e7a0ba9f366f84753ebd838c2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="create-and-publish-a-marketplace-item"></a>创建和发布 Marketplace 项目

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="create-a-marketplace-item"></a>创建 Marketplace 项
1. [下载](http://www.aka.ms/azurestackmarketplaceitem) Azure Gallery Packager 工具和示例 Azure Stack Marketplace 项。
2. 打开示例 Marketplace 项并重命名 **SimpleVMTemplate** 文件夹。 （使用与 Marketplace 项相同的名称，例如 **Contoso.TodoList**。）此文件夹包含：
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [创建一个 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)或从 GitHub 中选择一个模板。 Marketplace 项使用此模板来创建资源。
4. 若要确保可以成功部署资源，测试与 Microsoft Azure 堆栈 Api 模板。
5. 如果你的模板依赖于虚拟机映像，请按照说明[向 Azure Stack 中添加虚拟机映像](azure-stack-add-vm-image.md)。
6. 将 Azure Resource Manager 模板保存在 **/Contoso.TodoList/DeploymentTemplates/** 文件夹中。
7. 为 Marketplace 项选择图标和文本。 将图标添加到 **Icons** 文件夹，并向 **Strings** 文件夹中的 **resources** 文件添加文本。 为图标使用 Small、Medium、Large 和 Wide 命名约定。 有关详细说明，请参阅 [Marketplace 项 UI 参考](#reference-marketplace-item-ui)。
   
   > [!NOTE]
   > 为正确生成 Marketplace 项，需要全部四个图标大小（small、medium、large、wide）。
   > 
   > 
8. 在 **manifest.json** 文件中，将 **name** 更改为你的 Marketplace 项的名称。 另外，将 **publisher** 更改为你的公司的名称。
9. 在 **artifacts** 下，将 **name** 和 **path** 更改为你包括的 Azure 资源管理器模板的正确信息。
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. 将 **My Marketplace Items** 替换为你的 Marketplace 项应当显示在其中的类别列表。
    
             "categories":[
                 "My Marketplace Items"
              ],
11. 若要进一步编辑 manifest.json，请参阅[参考：Marketplace item manifest.json](#reference-marketplace-item-manifestjson)。
12. 若要将文件夹打包到 .azpkg 文件，请打开命令提示符并运行以下命令：
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > 输出包的完整路径必须存在。 例如，如果输出路径为 C:\MarketPlaceItem\yourpackage.azpkg，则文件夹 C:\MarketPlaceItem 必须存在。
    > 
    > 

## <a name="publish-a-marketplace-item"></a>发布 Marketplace 项
1. 使用 PowerShell 或 Azure 存储资源管理器将 Marketplace 项 (.azpkg) 上传到 Azure Blob 存储。 可以上传到本地 Azure Stack 存储或上传到 Azure 存储。 （这是包的临时位置。）请确保 blob 可公开访问。
2. 在客户端虚拟机在 Microsoft Azure 堆栈环境中，确保使用你的服务管理员凭据设置你的 PowerShell 会话。 可以在[使用 PowerShell 部署模板](user/azure-stack-deploy-template-powershell.md)中找到有关如何在 Azure Stack 中对 PowerShell 进行身份验证的说明。
3. 使用 **Add-AzureRMGalleryItem** PowerShell cmdlet 将 Marketplace 项发布到 Azure Stack。 例如：
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | 参数 | 说明 |
   | --- | --- |
   | 订阅 ID |管理员订阅 ID。 可以使用 PowerShell 来检索它。 如果希望在门户中获取它，请转到提供商订阅并复制订阅 ID。 |
   | GalleryItemUri |已上传到存储的库包的 Blob URI。 |
   | Apiversion |设置为 **2015-04-01**。 |
4. 转到门户。 作为操作员或用户，现在可以在门户中查看 Marketplace 项。
   
   > [!NOTE]
   > 包可能需要过几分钟才会出现。
   > 
   > 
5. 你的 Marketplace 项现在已保存到 Azure Stack Marketplace。 你可以选择从 Blob 存储位置中将其删除。
6. 可以使用 **Remove-AzureRMGalleryItem** cmdlet 删除 Marketplace 项。 示例：
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > 删除某个项后，Marketplace UI 可能会显示错误。 若要修复此错误，请在门户中单击“设置”。 然后，在“门户自定义”下选择“放弃修改”。
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>参考：Marketplace 项 manifest.json
### <a name="identity-information"></a>标识信息
| 名称 | 需要 | Type | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| 名称 |X |String |[A-Za-z0-9]+ | |
| 发布者 |X |String |[A-Za-z0-9]+ | |
| 版本 |X |String |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>元数据
| 名称 | 需要 | Type | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |建议不要超过 80 个字符 |如果长度超过 80 个字符，门户可能无法美观地显示项名称。 |
| PublisherDisplayName |X |String |建议不要超过 30 个字符 |如果长度超过 30 个字符，门户可能无法美观地显示发布者名称。 |
| PublisherLegalName |X |String |最多 256 个字符 | |
| 摘要 |X |String |60 到 100 个字符 | |
| LongSummary |X |String |140 到 256 个字符 |在 Azure Stack 中尚不适用。 |
| 说明 |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 到 5,000 个字符 | |

### <a name="images"></a>映像
Marketplace 使用以下图标：

| 名称 | 宽度 | 高度 | 说明 |
| --- | --- | --- | --- |
| Wide |255 px |115 px |始终必需 |
| 大型 |115 px |115 px |始终必需 |
| 中型 |90 px |90 px |始终必需 |
| 小型 |40 px |40 px |始终必需 |
| 屏幕快照 |533 px |32 px |可选 |

### <a name="categories"></a>类别
应当为每个 Marketplace 项标记一个类别，该类别标识在门户 UI 中的何处显示该项。 可以选择 Azure Stack 中的现有类别之一（计算、数据 + 存储，等等），也可以选择新建一个。

### <a name="links"></a>链接
每个 Marketplace 项可以包括指向其他内容的各种链接。 链接是作为名称和 URI 的列表指定的。

| 名称 | 需要 | Type | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最多 64 个字符 | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>其他属性
除了前面的元数据之外，Marketplace 作者可以采用以下形式提供自定义键/值对数据：

| 名称 | 需要 | Type | 约束 | 说明 |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |最多 25 个字符 | |
| 值 |X |String |最多 30 个字符 | |

### <a name="html-sanitization"></a>HTML 清理
对于允许使用 HTML 的任何字段，将允许使用以下元素和属性：

h1、h2、h3、h4、h5、p、ol、ul、li、a[target|href]、br、strong、em、b、i

## <a name="reference-marketplace-item-ui"></a>参考：Marketplace 项 UI
在 Azure Stack 门户中显示的 Marketplace 项的图标和文本将如下所示。

### <a name="create-blade"></a>“创建”边栏选项卡
![“创建”边栏选项卡](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Marketplace 项详细信息边栏选项卡
![Marketplace 项详细信息边栏选项卡](media/azure-stack-marketplace-item-ui-reference/image3.png)

