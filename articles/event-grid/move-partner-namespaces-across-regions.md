---
title: 将 Azure 事件网格伙伴命名空间移动到另一个区域
description: 本文介绍如何将 Azure 事件网格伙伴命名空间从一个区域移到另一个区域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083631"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>将 Azure 事件网格伙伴命名空间移动到另一个区域
出于多种原因，你可能需要将资源移到另一个区域。 例如，若要充分利用新的 Azure 区域，以满足内部策略和调控要求，或者响应容量规划要求。 

下面是本文中所述的高级步骤： 

- 将**合作伙伴命名空间资源导出**到 Azure 资源管理器模板。 删除模板中事件通道资源的定义。 事件通道可能具有对合作伙伴的 Azure 资源管理器 ID 的引用，该主题由客户所有。 因此，不能使用目标区域中的模板来创建它们。  
- **使用模板将合作伙伴命名空间部署** 到目标区域。 然后，在目标区域的新合作伙伴命名空间中创建事件通道。 
- 若要 **完成移动**，请从源区域中删除伙伴命名空间。 

    > [!NOTE]
    > - 不支持将 **合作伙伴主题** 导出到 Azure 资源管理器模板，因为客户无法直接创建合作伙伴主题。 
    > - **合作伙伴注册** 是全局资源 (未绑定到任何特定区域) ，因此不适用，将其从一个区域移到另一个区域。 

## <a name="prerequisites"></a>必备条件
- 确保事件网格服务在目标区域中可用。 参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>准备
若要开始，请导出合作伙伴命名空间的资源管理器模板。 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在顶部的搜索栏中，键入 " **事件网格伙伴命名空间**"，然后从 "结果" 列表中选择 " **事件网格伙伴命名空间** "。 
3. 选择要导出到资源管理器模板的 **伙伴命名空间** 。 
4. 在 "**事件网格伙伴命名空间**" 页上，选择左侧菜单中的 "**设置**" 下的 "**导出模板**"，然后在工具栏上选择 "**下载**"。 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="导出模板-> 下载" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. 找到从门户下载的 **.zip** 文件，并将该文件解压缩到所选的文件夹。 此 zip 文件包含模板和参数 JSON 文件。 
1. 在所选编辑器中打开 **template.js** 。 
8. 将 `location` **主题** 资源更新到目标区域或位置。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **保存** 模板。 

## <a name="recreate"></a>重新创建 
部署模板以在目标区域中创建合作伙伴命名空间。 

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
    1. 对于 " **位置**"，请选择目标区域。 如果选择了现有资源组，则此设置为只读。 
    1. 对于 " **合作伙伴命名空间名称**"，请输入新合作伙伴命名空间的名称。 
    1. 对于合作伙伴注册的外部 ID，按以下格式输入合作伙伴注册的资源 ID： `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` 。
    1. 选择“我同意上述条款和条件”复选框。     
    1. 选择 "查看" 和 " **创建** " 以启动部署过程。 
    1. 在 " **查看** " 和 "创建" 页上，查看设置，然后选择 " **创建**"。 

## <a name="discard-or-clean-up"></a>丢弃或清理
若要完成移动，请在源区域中删除伙伴命名空间。  

如果要重新开始，请删除目标区域中的合作伙伴命名空间，并重复本文的 " [准备](#prepare) 并 [重新创建](#recreate) " 部分中的步骤。

使用 Azure 门户删除合作伙伴命名空间：

1. 在 "Azure 门户顶部的" 搜索 "窗口中，键入" **事件网格伙伴命名空间**"，然后从搜索结果中选择" **事件网格伙伴命名空间** "。 
2. 选择要删除的合作伙伴命名空间，并在工具栏中选择 " **删除** "。 
3. **确认** 删除以删除伙伴命名空间。 

## <a name="next-steps"></a>后续步骤
已了解如何将事件网格伙伴命名空间从一个区域移到另一个区域。 请参阅以下文章：跨区域移动系统主题、自定义主题和域。

- [跨区域移动系统主题](move-system-topics-across-regions.md)。 
- [跨区域移动自定义主题](move-custom-topics-across-regions.md)。 
- [跨区域移动域](move-domains-across-regions.md)。

若要详细了解如何在 Azure 中的区域和灾难恢复之间移动资源，请参阅以下文章： [将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。