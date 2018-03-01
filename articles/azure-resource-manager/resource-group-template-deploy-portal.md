---
title: "使用 Azure 门户部署 Azure 资源 | Microsoft Docs"
description: "使用 Azure 门户和 Azure Resource Manager 来部署资源。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: 39680d4e70cc47cfa119b3a46e8d51ea87fa618d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>使用 Resource Manager 模板和 Azure 门户部署资源

本主题演示了如何将 [Azure 门户](https://portal.azure.com)与 [Azure 资源管理器](resource-group-overview.md)配合使用，以部署 Azure 资源。 若要了解有关管理资源的信息，请参阅[通过门户管理 Azure 资源](resource-group-portal.md)。

## <a name="create-resource-group"></a>创建资源组

1. 若要创建空资源组，请选择“资源组”。

   ![选择资源组](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. 在“资源组”下，选择“添加”。

   ![添加资源组](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. 为其命名并指定位置，如有必要，选择一个订阅。 需要提供资源组的位置，因为资源组存储与资源有关的元数据。 出于合规目的，你可能需要指定该元数据的存储位置。 一般情况下，建议指定大部分资源将驻留的位置。 使用相同位置可简化模板。

   ![设置组的值](./media/resource-group-template-deploy-portal/set-group-properties.png)

   完成属性设置后，选择“创建”。

1. 若要查看新资源组，选择“刷新”。

   ![刷新资源组](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>从 Marketplace 部署资源

创建资源组后，可以从 Marketplace 将资源部署到资源组。 Marketplace 为常见方案提供预定义的解决方案。

1. 若要开始部署，请选择“创建资源”。

   ![新建资源](./media/resource-group-template-deploy-portal/new-resources.png)

1. 查找希望部署的资源类型。

   ![选择“资源类型”](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. 如果看不到想要部署的特定解决方案，可以在 Marketplace 搜索。 例如，若要查找 Wordpress 解决方案，开始键入 Wordpress 并选择所需选项。

   ![搜索 Marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. 根据所选资源的类型，需要在部署之前设置相关属性的集合。 对于所有类型，必须选择目标资源组。 以下映像演示了如何创建 Web 应用并将其部署到创建的资源组。

   ![创建资源组](./media/resource-group-template-deploy-portal/select-existing-group.png)

   也可以在部署资源时创建资源组。 选择“新建”并为资源组指定名称。

   ![新建资源组](./media/resource-group-template-deploy-portal/select-new-group.png)

1. 部署开始。 部署可能需要几分钟的时间。 完成部署后，会看到一条通知。

   ![查看通知](./media/resource-group-template-deploy-portal/view-notification.png)

1. 部署资源后，可以通过选择“添加”将更多资源添加到资源组。

   ![添加资源](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>从自定义模板部署资源

如果想要执行部署，但不使用 Marketplace 中的任何模板，可以创建自定义模板来针对解决方案定义基础结构。 若要了解如何创建模板，请参阅[了解 Azure 资源管理器模板的结构和语法](resource-group-authoring-templates.md)。

1. 若要通过门户部署自定义模板，请选择“创建资源”，并搜索“模板部署”，直至可以从选项中选择它。

   ![搜索模板部署](./media/resource-group-template-deploy-portal/search-template.png)

1. 选择“创建”。

   ![选择“创建”](./media/resource-group-template-deploy-portal/show-template-option.png)

1. 此时，会看到用于创建模板的几个选项。 选择“在编辑器中生成自己的模板”。

   ![视图选项](./media/resource-group-template-deploy-portal/see-options.png)

1. 你已具有可用于自定义的空白模板。

   ![创建模板](./media/resource-group-template-deploy-portal/blank-template.png)

1. 可以手动编辑 JSON 语法，也可以从[模板库快速入门](https://azure.microsoft.com/resources/templates/)中选择预建模板。 但是，本文使用的是“添加资源”选项。

   ![编辑模板](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. 选择“存储帐户”并为其提供名称。 完成提供值后，选择“确定”。

   ![选择存储帐户](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. 编辑器会自动为资源类型添加 JSON。 请注意，它包含用于定义存储帐户类型的参数。 选择“保存”。

   ![显示模板](./media/resource-group-template-deploy-portal/show-json.png)

1. 现在，可以选择部署模板中定义的资源。 若要部署，请同意条款和条件，然后选择“购买”。

   ![部署模板](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>从保存到帐户中的模板部署资源

通过此门户，可将模板保存到 Azure 帐户，以便以后重新部署它。 有关使用这些已保存模板的详细信息，请参阅 [Azure 门户中的专用模板入门](../marketplace-consumer/mytemplates-getstarted.md)。

1. 若要查找已保存模板，请选择“更多服务”。

   ![其他服务](./media/resource-group-template-deploy-portal/more-services.png)

1. 搜索模板并选择该选项。

   ![搜索模板](./media/resource-group-template-deploy-portal/find-templates.png)

1. 从已保存到帐户的模板列表中，选择要使用的模板。

   ![已保存模板](./media/resource-group-template-deploy-portal/saved-templates.png)

1. 选择“部署”以重新部署该已保存模板。

   ![部署已保存模板](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>后续步骤
* 若要查看审核日志，请参阅[使用 Resource Manager 进行审核操作](resource-group-audit.md)。
* 若要排查部署错误，请参阅[查看部署操作](resource-manager-deployment-operations.md)。
* 若要从部署或资源组中检索模板，请参阅[从现有资源导出 Azure 资源管理器模板](resource-manager-export-template.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。
