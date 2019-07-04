---
title: 使用 Azure 门户部署 Azure 资源 | Microsoft Docs
description: 使用 Azure 门户和 Azure Resource Manager 来部署资源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460347"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>使用 Resource Manager 模板和 Azure 门户部署资源

了解如何使用[Azure 门户](https://portal.azure.com)与[Azure 资源管理器](resource-group-overview.md)部署 Azure 资源。 若要了解有关管理资源的信息，请参阅[通过 Azure 门户管理 Azure 资源](manage-resources-portal.md)。

通常使用 Azure 门户部署 Azure 资源包括两个步骤：

- 创建资源组。
- 将资源部署到资源组。

此外，您还可以部署 Azure 资源管理器模板来创建 Azure 资源。

本文介绍了这两种方法。

## <a name="create-a-resource-group"></a>创建资源组

1. 若要创建新的资源组，请选择**资源组**从[Azure 门户](https://portal.azure.com)。

   ![选择资源组](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. 在“资源组”下，选择“添加”  。

   ![添加资源组](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. 选择或输入以下属性值：

    - **订阅**：选择 Azure 订阅。
    - **资源组**：为资源组指定名称。
    - **区域**：指定一个 Azure 位置。 这是资源组存储与资源有关的元数据的位置。 出于合规目的，你可能需要指定该元数据的存储位置。 一般情况下，建议指定大部分资源的驻留位置。 使用相同位置可简化模板。

   ![设置组的值](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. 选择“查看 + 创建”  。
1. 检查值，并选择**创建**。
1. 选择**刷新**可以看到列表中的新资源组之前。

## <a name="deploy-resources-to-a-resource-group"></a>将资源部署到资源组

创建资源组后，可以从 Marketplace 将资源部署到的组。 市场为常见方案提供预定义的解决方案。

1. 若要开始部署，请选择**创建资源**从[Azure 门户](https://portal.azure.com)。

   ![新建资源](./media/resource-group-template-deploy-portal/new-resources.png)

1. 查找希望部署的资源类型。 按类别进行组织资源。 如果看不到想要部署的特定解决方案，可以在市场中搜索。 以下屏幕截图显示已选择 Ubuntu 服务器。

   ![选择“资源类型”](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. 根据所选资源的类型，需要在部署之前设置相关属性的集合。 对于所有类型，必须选择目标资源组。 下图显示了如何创建 Linux 虚拟机并将其部署到你创建的资源组。

   ![创建资源组](./media/resource-group-template-deploy-portal/select-existing-group.png)

   也可以在部署资源时创建资源组。 选择“新建”  并为资源组指定名称。

1. 部署开始。 部署可能需要几分钟。 某些资源需要更长时间比其他资源。 完成部署后，会看到一条通知。 选择**转到资源**打开

   ![查看通知](./media/resource-group-template-deploy-portal/view-notification.png)

1. 部署资源后，可以通过选择“添加”  将更多资源添加到资源组。

   ![添加资源](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>从自定义模板部署资源

如果想要执行部署，但不使用市场中的任何模板，可以创建自定义模板来针对解决方案定义基础结构。 若要了解如何创建模板，请参阅[了解 Azure 资源管理器模板的结构和语法](resource-group-authoring-templates.md)。

> [!NOTE]
> 门户界面不支持引用[来自 Key Vault 的密码](resource-manager-keyvault-parameter.md)。 请改用 [PowerShell](resource-group-template-deploy.md) 或 [Azure CLI](resource-group-template-deploy-cli.md)，从本地或从外部 URI 部署模板。

1. 若要部署通过门户自定义的模板，请选择**创建资源**，搜索**模板**。 然后选择**模板部署**。

   ![搜索模板部署](./media/resource-group-template-deploy-portal/search-template.png)

1. 选择“创建”  。
1. 将看到用于创建模板的多个选项：

    - **生成你自己的模板编辑器中**： 创建使用门户模板的模板编辑器。  在编辑器都能够以添加的资源模板架构。
    - **常用模板**:有四个常见 templatess 创建 Linux 虚拟机、 Windows 虚拟机、 web 应用程序和 Azure SQL 数据库。
    - **加载 GitHub 快速入门模板**： 使用现有[快速入门模板](https://azure.microsoft.com/resources/templates/)。

   ![视图选项](./media/resource-group-template-deploy-portal/see-options.png)

    本教程提供了用于加载快速入门模板说明。

1. 下**加载 GitHub 快速入门模板**，键入或选择**101-存储-帐户创建**。

    可以使用两个选项：

    - **选择模板**： 部署模板。
    - **编辑模板**： 在部署之前编辑的快速入门模板。

1. 选择**编辑模板**浏览门户模板编辑器。 在编辑器中加载模板。 请注意，有两个参数： **storageAccountType**并**位置**。

   ![创建模板](./media/resource-group-template-deploy-portal/show-json.png)

1. 对模板进行了少量更改。 例如，更新**storageAccountName**变量：

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. 选择“保存”。  现在，请参阅门户模板部署接口。 请注意，在模板中定义的两个参数。
1. 输入或选择属性值：

    - **订阅**：选择 Azure 订阅。
    - **资源组**：选择**新建**为提供的名称。
    - **位置**：选择 Azure 位置。
    - **存储帐户类型**:使用默认值。
    - **位置**：使用默认值。
    - **我同意上述条款和条件**：（选中）

1. 选择“购买”。 

## <a name="next-steps"></a>后续步骤

- 若要查看审核日志，请参阅[使用资源管理器审核操作](./resource-group-audit.md)。
- 若要排查部署错误，请参阅[查看部署操作](./resource-manager-deployment-operations.md)。
- 若要从部署或资源组中导出模板，请参阅[导出 Azure 资源管理器模板](./manage-resource-groups-portal.md#export-resource-groups-to-templates)。
- 若要安全地将你的服务推出跨多个区域，请参阅[Azure 部署管理器](./deployment-manager-overview.md)。
