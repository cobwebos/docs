---
title: 将 Azure 事件网格域移到另一个区域
description: 本文介绍如何将 Azure 事件网格域从一个区域移到另一个区域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083634"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>将 Azure 事件网格域移到另一个区域
出于多种原因，你可能需要将资源移到另一个区域。 例如，若要充分利用新的 Azure 区域，以满足内部策略和调控要求，或者响应容量规划要求。 

下面是本文中所述的高级步骤： 

- 将**域资源导出**到 Azure 资源管理器模板。 

    > [!IMPORTANT]
    > 域中的域资源和主题将导出到模板。 不会导出对域主题的订阅。 
- **使用模板将域部署** 到目标区域。 
- 在目标区域中**手动创建域主题的订阅**。 将域导出到当前区域中的模板时，不会导出域主题的订阅。 因此，在目标区域中创建域和域主题后，创建它们。 
- **验证部署**。 向域中的域主题发送事件，并验证是否调用与该订阅关联的事件处理程序。 
- 若要 **完成移动**，请从源区域中删除域。 

## <a name="prerequisites"></a>必备条件
- 确保事件网格服务在目标区域中可用。 参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>准备
若要开始，请导出域的资源管理器模板。 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在搜索栏中，键入 " **事件网格域**"，然后从 "结果" 列表中选择 " **事件网格域** "。 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="搜索和选择事件网格域":::
3. 选择要导出到资源管理器模板的 **域** 。 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="搜索和选择事件网格域" 下的 "**导出模板**"，然后在工具栏上选择 "**下载**"。 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="搜索和选择事件网格域" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > 将导出域和域主题。 不会导出域主题的订阅。 因此，在移动域主题后，需要为域主题创建订阅。 
5. 找到从门户下载的 **.zip** 文件，并将该文件解压缩到所选的文件夹。 此 zip 文件包含模板和参数 JSON 文件。 
1. 在所选编辑器中打开 **template.js** 。 
8. 将 `location` **域** 资源更新到目标区域或位置。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **保存** 模板。 

## <a name="recreate"></a>重新创建 
部署模板以在目标区域中创建域和域主题。 

1. 在 Azure 门户中，选择“创建资源”。
2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 
3. 选择“模板部署”。
4. 选择“创建” 。
5. 选择“在编辑器中生成自己的模板”。
6. 选择“加载文件”，然后按说明加载在上一部分下载的 **template.json** 文件。
7. 选择“保存”以保存该模板。 
8. 在“自定义部署”页上执行以下步骤：
    1. 选择 Azure 订阅。 
    1. 选择目标区域中的现有 **资源组** ，或者创建一个。 
    1. 对于 " **区域**"，请选择目标区域。 如果选择了现有资源组，则此设置为只读。 
    1. 对于 " **域名**"，请输入域的新名称。 
    1. 选择“查看 + 创建”  。 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="搜索和选择事件网格域" 页。 确认资源组中有域。 选择域。 确认域中有域主题。 

## <a name="discard-or-clean-up"></a>丢弃或清理
若要完成移动，请在源区域中删除域。  

如果要重新开始，请在目标区域中删除域，并重复本文的 " [准备](#prepare) 并 [重新创建](#recreate) " 部分中的步骤。

使用 Azure 门户删除域：

1. 在 "Azure 门户顶部的" 搜索 "窗口中，键入" **事件网格域**"，然后从搜索结果中选择" **事件网格域** "。 
2. 选择要删除的域，并从工具栏中选择 " **删除** "。 
3. 在 "确认" 页上，输入资源组的名称，然后选择 " **删除**"。  

使用 Azure 门户删除包含域的资源组：

1. 在 "Azure 门户顶部的" 搜索 "窗口中，键入" **资源组**"，然后从搜索结果中选择" **资源组** "。 
2. 选择要删除的资源组，并在工具栏中选择 " **删除** "。 
3. 在 "确认" 页上，输入资源组的名称，然后选择 " **删除**"。  

## <a name="next-steps"></a>后续步骤
您学习了如何将事件网格域从一个区域移到另一个区域。 请参阅以下文章，了解跨区域移动系统主题、自定义主题和合作伙伴命名空间。

- [跨区域移动系统主题](move-system-topics-across-regions.md)。 
- [跨区域移动自定义主题](move-custom-topics-across-regions.md)。 
- [跨区域移动伙伴命名空间](move-partner-namespaces-across-regions.md)。

若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅以下文章： [将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。