---
title: "使用 Azure 托管应用程序 | Microsoft 文档"
description: "介绍客户如何基于已发布的文件创建 Azure 托管应用程序。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.contentlocale: zh-cn
ms.lasthandoff: 08/25/2017

---
# <a name="consume-an-internal-managed-application"></a>使用内部托管应用程序

可使用适用于组织中成员的 Azure [托管应用程序](managed-application-overview.md)。 例如，可从 IT 部门选择确保符合组织标准的托管应用程序。 这些托管应用程序通过服务目录（而不是 Azure Marketplace）提供。

在继续阅读本文之前，请确保订阅的服务目录中具有托管应用程序。 如果组织中有人尚未创建托管应用程序，请参阅[发布托管应用程序供内部使用](managed-application-publishing.md)。

当前，可以使用 Azure CLI 或 Azure 门户来使用托管应用程序。

## <a name="create-the-managed-application-by-using-the-portal"></a>使用门户创建托管应用程序

若要通过门户部署托管应用程序，请执行以下步骤：

1. 转到 Azure 门户。 搜索“服务目录托管应用程序”。

   ![服务目录托管应用程序](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. 选择你想要从可用解决方案列表中创建的托管应用程序。 选择“创建” 。

   ![托管应用程序选择](./media/managed-application-consumption/select-offer.png)

1. 为预配资源所需的参数提供值。 对于位置，选择“美国中西部”。 选择“确定”。

   ![托管应用程序参数](./media/managed-application-consumption/input-parameters.png)

1. 模板会验证你提供的值。 如果验证成功，请选择“确定”启动部署。

   ![托管应用程序验证](./media/managed-application-consumption/validation.png)

部署完成后，会在提供的托管资源组中预配模板中定义的相应资源。

## <a name="create-the-managed-application-by-using-azure-cli"></a>使用 Azure CLI 创建托管应用程序

使用 Azure CLI 创建托管应用程序有两种方法：

* 使用用于创建托管应用程序的命令。
* 使用常规模板部署命令。

### <a name="use-the-template-deployment-command"></a>使用模板部署命令

部署供应商创建的 applianceMainTemplate.json 文件。

然后创建两个资源组。 第一个资源组是在其中创建托管应用程序资源的地方：Microsoft.Solutions/appliances。 第二个资源组包含 mainTemplate.json 中定义的所有资源。 此资源组是由 ISV 管理的。

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> 请使用 `westcentralus` 作为资源组的位置。
>

若要部署 mainResourceGroup 中的 applianceMainTemplate.json，请使用以下命令：

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

在运行前面的模板后，它会提示输入模板中定义的参数的值。 除了预配模板中的资源时所需的参数外，还需要两个关键参数值：

- **managedResourceGroupId**：资源组 ID，该资源组包含 applianceMainTemplate.json 中定义的资源。 ID 采用 `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}` 形式。 在前面的示例中，它是 `managedResourceGroup` 的 ID。
- applianceDefinitionId：托管应用程序定义资源的 ID。 此值是由 ISV 提供的。

> [!NOTE]
> 发布者必须为包含托管应用程序定义的资源组授予访问权限。 定义资源是在发布者订阅中创建的。 因此，客户租户中的用户、用户组或应用程序需要对此资源具有读取访问权限。

部署成功完成后，即可在 mainResourceGroup 中看到创建的托管应用程序。 在 managedResourceGroup 中创建了 storageAccount 资源。

### <a name="use-the-create-command"></a>使用 create 命令

可以使用 `az managedapp create` 命令基于托管应用程序定义创建托管应用程序。

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **appliance-definition-Id**：在前面的步骤中创建的托管应用程序定义的资源 ID。 若要获取此 ID，请运行以下命令：

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  此命令会返回托管应用程序定义。 需要 ID 属性的值。

* **managed-rg-id**：包含 applianceMainTemplate.json 中定义的资源的资源组名称。 此资源组为托管资源组。 它由发布者进行管理。 如果不存在，则会为你创建一个。
* **resource-group**：在其中创建托管应用程序资源的资源组。 Microsoft.Solutions/appliance 资源位于此资源组中。
* parameters：applianceMainTemplate.json 中定义的资源所需的参数。

## <a name="known-issues"></a>已知问题

此预览版本有以下问题：

* 托管应用程序创建期间会出现一个 500 内部服务器错误。 如果你遇到此问题，则很可能是间歇性的。 请重试操作即可。
* 托管资源组需要一个新的资源组。 如果使用现有资源组，则部署将失败。
* 必须在 westcentralus 位置中创建包含 Microsoft.Solutions/appliances 资源的资源组。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅[托管应用程序概述](managed-application-overview.md)。
* 有关发布服务目录托管应用程序的信息，请参阅[创建和发布服务目录托管应用程序](managed-application-publishing.md)。
* 有关将托管应用程序发布到 Azure Marketplace 的信息，请参阅 [Marketplace 中的 Azure 托管应用程序](managed-application-author-marketplace.md)。
* 有关从 Marketplace 使用托管应用程序的信息，请参阅[在 Marketplace 中使用 Azure 托管应用程序](managed-application-consume-marketplace.md)。

