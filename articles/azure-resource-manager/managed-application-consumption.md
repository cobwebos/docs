---
title: "使用 Azure 托管应用程序 | Microsoft Docs"
description: "介绍了客户如何基于已发布的文件创建 Azure 托管应用程序。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2763da60fe25f2ca55603ecfcbbcefe3e368c25d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---
# <a name="consume-an-azure-managed-application"></a>使用 Azure 托管应用程序

如[托管应用程序概述](managed-application-overview.md)一文中所述，在端到端体验中有两种应用场景。 一种是发布者或 ISV 希望创建供客户使用的托管应用程序。 第二种是最终客户或使用者使用托管应用程序。 本文涵盖了第二种应用场景并且介绍了最终客户如何使用 ISV 提供的托管应用程序。

当前，可以使用 Azure CLI 或 Azure 门户来使用托管应用程序。 

## <a name="create-the-managed-application-using-cli"></a>使用 CLI 创建托管应用程序 

必须获取要使用的设备的设备定义 ID。

使用 Azure CLI 可采用两种方法来创建托管应用程序。 一种是使用常规模板部署命令，另一种是使用针对此用途提供的新命令。

### <a name="create-using-template-deployment-command"></a>使用模板部署命令创建

你将部署供应商创建的 applianceMainTemplate.json 文件。

你将创建两个资源组。 第一个资源组是在其中创建设备资源的地方 (Microsoft.Solutions/appliances)。 第二个资源组包含 mainTemplate.json 中定义的所有资源。 此资源组是由 ISV 管理的。

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> 请使用 `westcentralus` 作为资源组的位置。
>


接下来，使用以下命令部署 mainResourceGroup 中的 applianceMainTemplate.json：

```azurecli
az group deployment --name managedAppDeployment --resourceGroup mainResourceGroup 
      --templateUri  
```

执行前面的模板时，它会提示你输入模板中定义的参数的值。 除了预配模板中的资源时所需的参数外，你还需要两个关键参数值：

- managedResourceGroupId - 将在其中创建 applianceMainTemplate.json 中定义的资源的资源组的 ID。 ID 采用 `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}` 形式。 在前面的示例中，它是 `managedResourceGroup` 的 ID。
- applianceDefinitionId - 托管应用程序定义资源的 ID。 此值是由 ISV 提供的。 

> [!NOTE] 
> ISV 必须授予对在其中创建设备定义资源的资源组的访问权限。 设备定义资源是在 ISV 订阅中创建的。 因此，客户租户中的用户、用户组或应用程序需要对此资源具有读取访问权限。 

在部署成功完成后，可以看到在 **mainResourceGroup** 中创建了设备资源。 在 **managedResourceGroup** 中创建了 storageAccount 资源。

### <a name="create-the-managed-application-using-create-command"></a>使用 create 命令创建托管应用程序

可以使用 `az managedapp create` 命令基于托管应用程序定义创建托管应用程序。 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
       --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
       --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
       --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
       --debug
```

**appliance-definition-Id** - 在前面的步骤中创建的设备定义的资源 ID。 若要获取此 ID，请运行以下命令：

```azurecli
az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
```

此命令返回设备定义。 你需要 **Id** 属性的值。

**managed-rg-id** - 将在其中创建 applianceMainTemplate.json 中定义的所有资源的资源组的名称。 此资源组是托管资源组，并且是由发布者管理的。 如果它不存在，则会创建一个。

**resource-group** - 将在其中创建设备资源的资源组。 Microsoft.Solutions/appliance 资源位于此资源组中。 

**parameters** - applianceMainTemplate.json 中定义的资源所需的参数。

## <a name="create-the-managed-application-using-portal"></a>使用门户创建托管应用程序

门户中也支持使用 ISV 发布的托管应用程序。 可以按照以下步骤操作：

从 Azure 门户中的“创建”边栏选项卡中选择托管应用程序 -

![](./media/managed-application-consumption/create-blade.png)

接下来，你会看到由各个 ISV/合作伙伴提供的产品的列表。 选择你要创建的产品并单击“创建”

![](./media/managed-application-consumption/select-offer.png)

单击“创建”后，提供在打开的边栏选项卡中预配资源所需的参数。 

![](./media/managed-application-consumption/input-parameters.png)

在提供值后，单击“确定”。 将对照你提供的输入对模板进行验证。 如果验证成功，则会启动模板部署。 部署完成后，会在你提供的托管资源组中预配模板中定义的相应资源。

## <a name="known-issues"></a>已知问题

此预览版本有以下问题：

* 如果在创建设备期间收到一个 500 内部服务器错误，则这可能是一个间歇性问题。 如果遇到此错误，请重试操作。
* 托管资源组需要一个新的资源组。 使用现有资源组会导致部署失败。
* 应当在 **westcentralus** 位置中创建包含 Microsoft.Solutions/appliances 资源的资源组。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](managed-application-overview.md)。
* 若要了解供应商体验，请参阅[创建和发布 Azure 托管应用程序](managed-application-publishing.md)。
