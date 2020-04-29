---
title: 使用 Azure 门户部署资源
description: 使用 Azure 门户和 Azure 资源管理器将资源部署到订阅中的资源组。
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80153431"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>利用 ARM 模板和 Azure 门户部署资源

了解如何使用[azure 资源管理器（ARM）模板](overview.md) [Azure 门户](https://portal.azure.com)来部署 azure 资源。 若要了解有关管理资源的信息，请参阅[通过 Azure 门户管理 Azure 资源](../management/manage-resources-portal.md)。

使用 Azure 门户部署 Azure 资源通常涉及两个步骤：

- 创建资源组。
- 将资源部署到资源组。

此外，还可以部署 ARM 模板来创建 Azure 资源。

本文介绍了这两种方法。

## <a name="create-a-resource-group"></a>创建资源组

1. 若要创建新资源组，请从 [Azure 门户](https://portal.azure.com)中选择“资源组”  。

   ![选择资源组](./media/deploy-portal/select-resource-groups.png)

1. 在“资源组”下，选择“添加”  。

   ![添加资源组](./media/deploy-portal/add-resource-group.png)

1. 选择或输入以下属性值：

    - **订阅**：选择 Azure 订阅。
    - **资源组**：为资源组指定名称。
    - **区域**：指定 Azure 位置。 资源组在此处存储有关资源的元数据。 出于合规性原因，你可能会想要指定该元数据的存储位置。 一般情况下，建议指定大部分资源的驻留位置。 使用相同位置可简化模板。

   ![设置组的值](./media/deploy-portal/set-group-properties.png)

1. 选择“查看 + 创建”  。
1. 查看值，然后选择“创建”  。
1. 选择“刷新”  ，然后才能在列表中看到新资源组。

## <a name="deploy-resources-to-a-resource-group"></a>将资源部署到资源组

创建资源组后，可以从市场将资源部署到该组。 市场为常见方案提供预定义的解决方案。

1. 若要开始部署，请从 [Azure 门户](https://portal.azure.com)中选择“创建资源”  。

   ![新建资源](./media/deploy-portal/new-resources.png)

1. 查找希望部署的资源类型。 资源将按类别进行组织。 如果看不到想要部署的特定解决方案，可以在市场中搜索。 以下屏幕截图显示已选择“Ubuntu Server”。

   ![选择资源类型](./media/deploy-portal/select-resource-type.png)

1. 根据所选资源的类型，需要在部署之前设置相关属性的集合。 对于所有类型，必须选择目标资源组。 下图显示了如何创建 Linux 虚拟机并将其部署到所创建的资源组。

   ![创建资源组](./media/deploy-portal/select-existing-group.png)

   也可以在部署资源时创建资源组。 选择“新建”  并为资源组指定名称。

1. 部署开始。 部署可能需要几分钟的时间。 有些资源比其他资源需要更长的时间。 完成部署后，会看到一条通知。 选择“转到资源”  以将其打开

   ![查看通知](./media/deploy-portal/view-notification.png)

1. 部署资源后，可以通过选择“添加”  将更多资源添加到资源组。

   ![添加资源](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>从自定义模板部署资源

如果想要执行部署，但不使用市场中的任何模板，可以创建自定义模板来针对解决方案定义基础结构。 若要了解有关创建模板的信息，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。

> [!NOTE]
> 门户界面不支持引用[来自 Key Vault 的密码](key-vault-parameter.md)。 请改用 [PowerShell](deploy-powershell.md) 或 [Azure CLI](deploy-cli.md)，从本地或从外部 URI 部署模板。

1. 若要通过门户部署自定义模板，请选择“创建资源”  ，搜索“模板”  ， 然后选择“模板部署”  。

   ![搜索模板部署](./media/deploy-portal/search-template.png)

1. 选择“创建”  。
1. 此时，会看到用于创建模板的几个选项：

    - **在编辑器中生成自己的模板**：使用门户模板编辑器创建一个模板。  编辑器能够添加源模板架构。
    - **常用模板**：有四种常用模板，分别用于创建 Linux 虚拟机、Windows 虚拟机、Web 应用程序、Azure SQL 数据库。
    - **加载 GitHub 快速入门模板**：使用现有的[快速入门模板](https://azure.microsoft.com/resources/templates/)。

   ![视图选项](./media/deploy-portal/see-options.png)

    本教程提供了加载快速入门模板的说明。

1. 在“加载 GitHub 快速入门模板”下，键入或选择 **101-storage-account-create**。 

    可以使用两个选项：

    - **选择模板**：部署模板。
    - **编辑模板**：在部署快速入门模板之前对其进行编辑。

1. 选择“编辑模板”，浏览门户模板编辑器。  模板会加载到编辑器中。 请注意，有两个参数：**storageAccountType** 和 **location**。

   ![创建模板](./media/deploy-portal/show-json.png)

1. 对模板进行小的更改。 例如，将 **storageAccountName** 变量更新为：

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. 选择“保存”  。 现在会看到门户模板部署界面。 请注意在模板中定义的两个参数。
1. 输入或选择属性值：

    - **订阅**：选择 Azure 订阅。
    - **资源组**：选择 "**新建**" 并指定一个名称。
    - **位置**：选择 Azure 位置。
    - **存储帐户类型**：使用默认值。
    - **位置**：使用默认值。
    - **我同意上述条款和条件**：（选中）

1. 选择“购买”。 

## <a name="next-steps"></a>后续步骤

- 若要查看审核日志，请参阅[使用资源管理器审核操作](../management/view-activity-logs.md)。
- 若要排查部署错误，请参阅[查看部署操作](deployment-history.md)。
- 若要从部署或资源组中导出模板，请参阅[导出 ARM 模板](export-template-portal.md)。
- 若要跨多个区域安全地推出服务，请参阅[Azure 部署管理器](deployment-manager-overview.md)。
