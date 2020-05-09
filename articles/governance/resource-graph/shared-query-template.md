---
title: 快速入门：使用模板创建共享查询
description: 在本快速入门中，我们使用资源管理器模板创建 Resource Graph 共享查询，以便按 OS 对虚拟机计数。
ms.date: 04/28/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 050cf26da2054883fceaa08b11f94c6af4c85a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254238"
---
# <a name="quickstart-create-a-shared-query-by-using-a-resource-manager-template"></a>快速入门：使用资源管理器模板创建共享查询

可以将 Resource Graph 查询另存为专用查询或共享查询。   专用查询保存到个人门户配置文件中，不显示给其他人。 共享查询是一个资源管理器对象，可通过权限和基于角色的访问权限与他人共享。 可以通过共享查询以通用且一致的方式执行资源发现操作。 本快速入门使用资源管理器模板来创建共享查询。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="create-a-shared-query"></a>创建共享查询

在本快速入门中，我们创建一个名为“按 OS 进行 VM 计数”的共享查询。  若要使用 Resource Graph 资源管理器在 SDK 或门户中尝试此查询，请参阅[示例 - 按 OS 类型对虚拟机进行计数](./samples/starter.md#count-os)。

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/)。

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

该模板中定义了以下资源：

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>部署模板

> [!NOTE]
> Azure Resource Graph 服务免费。 有关详细信息，请参阅 [Azure Resource Graph 概述](./overview.md)。

1. 选择下图登录到 Azure 门户并打开模板：

   [![将策略模板部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json)

1. 选择或输入以下值：

   | 名称 | 值 |
   |------|-------|
   | 订阅 | 选择 Azure 订阅。 |
   | 资源组 | 选择“新建”，指定名称，然后选择“确定”。   |
   | 位置 | 选择区域。 例如“美国中部”。  |
   | 查询名称 | 保留默认值“按 OS 进行 VM 计数”。  |
   | 查询代码 | 保留默认值 `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | 查询说明 | 保留默认值“此共享查询对所有虚拟机资源计数，并按 OS 类型进行汇总。”  |
   | 我同意上述条款和条件 | （选择） |

1. 选择“购买”。 

其他某些资源：

- 若要查找更多示例模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)。
- 若要查看模板参考，请转到 [Azure 模板参考](/azure/templates/microsoft.resourcegraph/allversions)。
- 若要了解如何开发资源管理器模板，请参阅 [Azure 资源管理器文档](../../azure-resource-manager/management/overview.md)。
- 若要了解订阅级部署，请参阅[在订阅级别创建资源组和资源](../../azure-resource-manager/templates/deploy-to-subscription.md)。

## <a name="validate-the-deployment"></a>验证部署

若要运行新的共享查询，请执行以下步骤：

1. 在门户搜索栏中，搜索“Resource Graph 查询”并将其选中  。

1. 选择名为“按 OS 进行 VM 计数”的共享查询，然后在“概览”页上选择“结果”选项卡。   

也可在 Resource Graph 资源管理器中打开共享查询：

1. 在门户搜索栏中，搜索“Resource Graph 资源管理器”并将其选中  。

1. 选择“打开查询”按钮。 

1. 将“类型”更改为“共享查询”。   如果在列表中看不到“按 OS 进行 VM 计数”，请使用筛选器框来限制结果。  当“按 OS 进行 VM 计数”共享查询可见后，请选择其名称。 

1. 加载查询后，选择“运行查询”按钮。  结果将显示在下面的“结果”选项卡中。 

## <a name="clean-up-resources"></a>清理资源

若要删除创建的共享查询，请执行以下步骤：

1. 在门户搜索栏中，搜索“Resource Graph 查询”并将其选中  。

1. 选中名为“按 OS 进行 VM 计数”的共享查询旁边的复选框  。

1. 选择页面顶部的“删除”按钮。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已创建一个 Resource Graph 共享查询。

若要了解有关共享查询的详细信息，请继续学习以下内容的教程：

> [!div class="nextstepaction"]
> [在 Azure 门户中管理查询](./tutorials/create-share-query.md)