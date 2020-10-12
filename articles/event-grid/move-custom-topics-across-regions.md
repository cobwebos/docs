---
title: 将 Azure 事件网格自定义主题移到另一个区域
description: 本文介绍如何将 Azure 事件网格自定义主题从一个区域移到另一个区域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145327"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>将 Azure 事件网格自定义主题移到另一个区域
出于多种原因，你可能需要将资源移到另一个区域。 例如，若要充分利用新的 Azure 区域，以满足内部策略和调控要求，或者响应容量规划要求。 

下面是本文中所述的高级步骤： 

- 将**自定义主题资源导出**到 Azure 资源管理器模板。 

    > [!IMPORTANT]
    > 仅将自定义主题导出到模板。 不会导出该主题的任何订阅。
- **使用模板将自定义主题部署** 到目标区域。 
- 在目标区域中**手动创建订阅**。 将自定义主题导出到当前区域中的模板时，只会导出该主题。 订阅不包括在模板中，因此请在目标区域中创建自定义主题后手动创建。 
- **验证部署**。 验证是否在目标区域中创建了自定义主题。 
- 若要 **完成移动**，请从源区域中删除自定义主题。 

## <a name="prerequisites"></a>必备条件
- 完成 [快速入门：将自定义事件路由到](custom-event-quickstart-portal.md) 源区域中的 web 终结点。 执行此步骤，以便可以测试本文中的步骤。 
- 确保事件网格服务在目标区域中可用。 参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>准备
若要开始，请导出自定义主题的资源管理器模板。 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在搜索栏中，键入 " **事件网格主题**"，然后从 "结果" 列表中选择 " **事件网格主题** "。 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="搜索和选择事件网格主题":::
3. 选择要导出到资源管理器模板的 **主题** 。 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="搜索和选择事件网格主题" 下的 "**导出模板**"，然后在工具栏上选择 "**下载**"。 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="搜索和选择事件网格主题"
    ```
1. **保存** 模板。 

## <a name="recreate"></a>重新创建 
部署模板以在目标区域中创建自定义主题。 

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
    1. 对于 " **主题名称**"，请输入主题的新名称。 
    1. 在页面底部选择“查看 + 创建”。 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="搜索和选择事件网格主题" **创建**"。 

## <a name="verify"></a>验证

1. 部署成功后，选择“转到资源”。 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="搜索和选择事件网格主题":::
1. 确认您看到了该自定义主题的 **事件网格主题** 页。   
1. 按照将 [自定义事件路由到 web 终结点](custom-event-quickstart-portal.md#send-an-event-to-your-topic) 中的步骤将事件发送到主题。 验证是否调用了 webhook 事件处理程序。 

## <a name="discard-or-clean-up"></a>丢弃或清理
若要完成移动，请在源区域中删除自定义主题。  

如果要重新开始，请在目标区域中删除该主题，并重复本文的 " [准备](#prepare) 并 [重新创建](#recreate) " 部分中的步骤。

使用 Azure 门户删除自定义主题：

1. 在 "Azure 门户顶部的" 搜索 "窗口中，键入" **事件网格主题**"，然后从搜索结果中选择" **事件网格主题** "。 
2. 选择要删除的主题，并从工具栏中选择 " **删除** "。 
3. 在 "确认" 页上，输入资源组的名称，然后选择 " **删除**"。  

使用 Azure 门户删除包含自定义主题的资源组：

1. 在 "Azure 门户顶部的" 搜索 "窗口中，键入" **资源组**"，然后从搜索结果中选择" **资源组** "。 
2. 选择要删除的资源组，并在工具栏中选择 " **删除** "。 
3. 在 "确认" 页上，输入资源组的名称，然后选择 " **删除**"。  

## <a name="next-steps"></a>后续步骤
已了解如何将事件网格自定义主题从一个区域移到另一个区域。 请参阅以下文章，了解如何跨区域移动系统主题、域和合作伙伴命名空间。

- [跨区域移动系统主题](move-system-topics-across-regions.md)。 
- [跨区域移动域](move-domains-across-regions.md)。 
- [跨区域移动伙伴命名空间](move-partner-namespaces-across-regions.md)。

若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅以下文章： [将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。