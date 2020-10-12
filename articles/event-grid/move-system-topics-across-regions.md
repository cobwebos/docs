---
title: 将 Azure 事件网格系统主题移到另一个区域
description: 本文介绍如何将 Azure 事件网格系统主题从一个区域移到另一个区域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083622"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>将 Azure 事件网格系统主题移到另一个区域
出于多种原因，你可能需要将资源移到另一个区域。 例如，若要充分利用新的 Azure 区域，以满足内部策略和调控要求，或者响应容量规划要求。 

下面是本文中所述的高级步骤： 

- 将包含 Azure 存储帐户及其关联的系统主题的**资源组导出**到资源管理器的模板。 你还可以仅导出系统主题的模板。 如果转到此路由，请记住移动 Azure 事件源 (在本示例中，将 Azure 存储帐户) 到其他区域，然后再移动系统主题。 然后，在 "系统" 主题的 "导出的模板" 中，更新目标区域中的存储帐户的外部 ID。 
- **修改模板** 以将属性添加 `endpointUrl` 到订阅系统主题的 webhook。 导出系统主题时，其订阅 (在这种情况下，) 它也会导出到模板，但 `endpointUrl` 不包括属性。 因此，您需要将其更新为指向订阅该主题的终结点。 同时，将属性的值更新 `location` 为新位置或区域。 对于其他类型的事件处理程序，只需更新位置。 
- **使用模板将资源部署** 到目标区域。 你需要指定要在目标区域中创建的存储帐户和系统主题的名称。 
- **验证部署**。 验证将文件上载到目标区域中的 blob 存储时是否调用 webhook。 
- 若要 **完成移动**，请从源区域中删除资源 (事件源和系统主题) 。 

## <a name="prerequisites"></a>必备条件
- 完成 [快速入门：将 Blob 存储事件路由到源区域中的 Azure 门户的 web 终结点](blob-event-quickstart-portal.md) 。 此步骤是**可选的**。 请执行此操作来测试本文中的步骤。 在应用服务和应用服务计划的不同资源组中保留存储帐户。 
- 确保事件网格服务在目标区域中可用。 参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>准备
若要开始，请将包含系统事件源的资源组的资源管理器模板导出 (Azure 存储帐户) 及其关联的系统主题。 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择 " **资源组** "。 然后，选择包含为其创建系统主题的事件源的资源组。 在以下示例中，它是 **Azure 存储** 帐户。 资源组包含存储帐户及其关联的系统主题。 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="资源组页&quot;:::        
3. 在左侧菜单中，选择 &quot;**设置**" 下的 "**导出模板**"，然后在工具栏上选择 "**下载**"。 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="资源组页&quot;:::        
3. 在左侧菜单中，选择 &quot;**设置**"
        }
        ```

        > [!NOTE]
        > 对于其他类型的事件处理程序，将所有属性导出到模板。 只需将 `location` 属性更新到目标区域，如下一步所示。 
7. 将 `location` **存储帐户** 资源更新到目标区域或位置。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. 重复此步骤以更新 `location` 模板中的 **系统主题** 资源。 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **保存** 模板。 

## <a name="recreate"></a>重新创建 
部署模板，为目标区域中的存储帐户创建存储帐户和系统主题。 

1. 在 Azure 门户中，选择“创建资源”。
2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 
3. 选择“模板部署”。
4. 选择“创建” 。
5. 选择“在编辑器中生成自己的模板”。
6. 选择“加载文件”，然后按说明加载在上一部分下载的 **template.json** 文件。
7. 选择“保存”以保存该模板。 
8. 在 " **自定义部署** " 页上，执行以下步骤。 
    1. 选择 Azure 订阅。 
    1. 选择目标区域中的现有 **资源组** ，或者创建一个。 
    1. 对于 " **区域**"，请选择目标区域。 如果选择了现有资源组，则此设置为只读。
    1. 对于 " **系统主题名称**"，请输入将与存储帐户关联的系统主题的名称。  
    1. 对于 " **存储帐户名称**"，请输入要在目标区域中创建的存储帐户的名称。 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="资源组页&quot;:::        
3. 在左侧菜单中，选择 &quot;**设置**" **创建**"。 

## <a name="verify"></a>验证
1. 部署成功后，选择 " **转到资源组**"。 
1. 在 " **资源组** " 页上，验证事件源 (在此示例中，"Azure 存储帐户") 和 "系统" 主题是否已创建。 
1. 将文件上传到 Azure Blob 存储中的容器，并验证 webhook 是否已收到该事件。 有关详细信息，请参阅 [将事件发送到终结点](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint)。

## <a name="discard-or-clean-up"></a>丢弃或清理
若要完成移动，请在源区域中删除包含存储帐户及其关联的系统主题的资源组。  

如果要重新开始，请删除目标区域中的资源组，并重复本文的 " [准备](#prepare) 并 [重新创建](#recreate) " 部分中的步骤。

若要使用 Azure 门户 (源或目标) 删除资源组，请执行以下操作：

1. 在 "Azure 门户顶部的" 搜索 "窗口中，键入" **资源组**"，然后从搜索结果中选择" **资源组** "。 
2. 选择要删除的资源组，并在工具栏中选择 " **删除** "。 

    删除资源组
3. 在 "确认" 页上，输入资源组的名称，然后选择 " **删除**"。  

## <a name="next-steps"></a>后续步骤
已了解如何将 Azure 事件源及其关联的系统主题从一个区域移到另一个区域。 请参阅以下文章，了解如何跨区域移动自定义主题、域和合作伙伴命名空间。

- [跨区域移动自定义主题](move-custom-topics-across-regions.md)。 
- [跨区域移动域](move-domains-across-regions.md)。 
- [跨区域移动伙伴命名空间](move-partner-namespaces-across-regions.md)。 

若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅以下文章： [将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
