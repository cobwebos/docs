---
title: 导出资源管理器模板-Azure 门户
description: 使用 Azure 门户从订阅中的资源导出 Azure 资源管理器模板。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tomfitz
ms.openlocfilehash: 0605e24590fa2d702a1385429a7808a7e1226809
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532340"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Azure 门户中的单个和多个资源导出到模板

若要协助创建 Azure 资源管理器模板，可以从现有资源导出模板。 导出的模板可帮助你了解用于部署资源的 JSON 语法和属性。 若要自动执行将来的部署，请从导出的模板开始，并根据方案修改它。

资源管理器使你可以选择一个或多个资源，以便导出到模板。 你可以在模板中确切地关注所需的资源。

本文介绍如何通过门户导出模板。 你还可以使用[Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates)、 [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)或[REST API](/rest/api/resources/resourcegroups/exporttemplate)。

## <a name="choose-the-right-export-option"></a>选择正确的导出选项

可以通过两种方式来导出模板：

* **从资源组或资源中导出**。 此选项从现有资源生成新的模板。 导出的模板是资源组的当前状态的 "快照"。 可以导出整个资源组或该资源组中的特定资源。

* **在部署之前或从历史记录导出**。 此选项检索用于部署的模板的精确副本。

根据您选择的选项，导出的模板具有不同的质量。

| 从资源组或资源 | 在部署之前或从历史记录 |
| --------------------- | ----------------- |
| 模板是资源的当前状态的快照。 它包括在部署后进行的任何手动更改。 | 模板只在部署时显示资源的状态。 部署后进行的任何手动更改都不包括在内。 |
| 可以从资源组中选择要导出的资源。 | 包含特定部署的所有资源。 不能选取这些资源的子集或添加在不同时间添加的资源。 |
| 模板包括资源的所有属性，包括部署过程中通常不会设置的某些属性。 在重用模板之前，可能需要删除或清除这些属性。 | 模板仅包含部署所需的属性。 模板已准备就绪。 |
| 模板可能不包括重新使用所需的所有参数。 大多数属性值在模板中进行硬编码。 若要在其他环境中重新部署模板，需要添加参数，以提高配置资源的能力。 | 模板包含一些参数，这些参数可让你轻松地在不同的环境中重新部署。 |

从资源组或资源导出模板，时间为：

* 需要捕获对原始部署后所做资源所做的更改。
* 需要选择要导出的资源。

在部署之前或从历史记录导出模板，时间为：

* 你需要一个易于重复使用的模板。
* 不需要包含在原始部署之后所做的更改。

## <a name="export-template-from-a-resource-group"></a>从资源组导出模板

若要从资源组中导出一个或多个资源：

1. 选择包含要导出的资源的资源组。

1. 通过选中相应的复选框选择一个或多个资源。  若要选择 "全部"，请选中 "**名称**" 左侧的复选框。 仅在选择了至少一个资源后，才会启用 "**导出模板**" 菜单项。

   ![导出所有资源](./media/export-template-portal/select-all-resources.png)

    在屏幕截图上，只选择了存储帐户。
1. 选择“导出模板”。

1. 将显示导出的模板，可供下载和部署。

   ![显示模板](./media/export-template-portal/show-template.png)

## <a name="export-template-from-a-resource"></a>从资源中导出模板

导出一个资源：

1. 选择包含要导出的资源的资源组。

1. 选择要导出的资源，以打开资源。

1. 对于该资源，请在左窗格中选择 "**导出模板**"。

   ![导出资源](./media/export-template-portal/export-single-resource.png)

1. 将显示导出的模板，可供下载和部署。 模板只包含单个资源。

## <a name="export-template-before-deployment"></a>部署前导出模板

1. 选择要部署的 Azure 服务。

1. 填写新服务的值。

1. 传递验证后，但在开始部署之前，请选择 "**下载用于自动化的模板**"。

   ![下载模板](./media/export-template-portal/download-before-deployment.png)

1. 模板随即显示，可供下载和部署。


## <a name="export-template-after-deployment"></a>部署后导出模板

可以导出用于部署现有资源的模板。 获取的模板与用于部署的模板完全相同。

1. 选择要导出的资源组。

1. 选择 "**部署**" 下的链接。

   ![选择部署历史记录](./media/export-template-portal/select-deployment-history.png)

1. 从部署历史记录中选择一个部署。

   ![选择部署](./media/export-template-portal/select-details.png)

1. 选择**模板**。 此时将显示用于此部署的模板，可供下载。

   ![选择模板](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>后续步骤

- 了解如何导出[Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates)、 [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)或[REST API](/rest/api/resources/resourcegroups/exporttemplate)的模板。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./resource-group-authoring-templates.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。
