---
title: "使用 Azure CLI 创建 Azure 托管应用程序 | Microsoft Docs"
description: "演示如何创建适用于组织中成员的 Azure 托管应用程序。"
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 35059603096279f7d58da1c1b40dd2ab3f1b5c38
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>使用 Azure CLI 创建和部署 Azure 托管应用程序

本文简单介绍了如何使用托管应用程序。 请先向组织中用户的内部目录添加托管应用程序定义， 然后将该托管应用程序部署到订阅。 为简单起见，我们已为托管应用程序生成了文件。 一个文件定义解决方案的基础结构。 另一个文件定义用户界面，以便通过门户部署托管应用程序。 这些文件可通过 GitHub 获取。 可以在[创建服务目录应用程序](publish-service-catalog-app.md)教程中了解如何生成这些文件。

完成后会有三个资源组，包含托管应用程序的不同部件。

| 资源组 | Contains | 说明 |
| -------------- | -------- | ----------- |
| appDefinitionGroup | 托管应用程序定义。 | 发布者创建此资源组和托管应用程序定义。 只要有权访问托管应用程序定义，任何人均可进行部署。 |
| applicationGroup | 托管应用程序实例。 | 使用者创建此资源组和托管应用程序实例。 使用者可以通过此实例更新托管应用程序。 |
| infrastructureGroup | 托管应用程序的资源。 | 此资源组是在创建托管应用程序时自动创建的。 发布者有权访问此资源组来管理应用程序。 使用者访问此资源组的权限有限。 |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>为定义创建资源组

托管应用程序定义存在于资源组中。 该资源组是在其中部署和管理 Azure 资源的逻辑集合。

若要创建资源组，请使用以下命令：

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>创建托管应用程序定义

定义托管应用程序时，请选择代表使用者管理资源的用户、组或应用程序。 此标识对托管资源组的权限与所分配的角色相对应。 通常创建 Azure Active Directory 组来管理资源。 但在本文中，请使用自己的标识。

若要获取标识的对象 ID，请在以下命令中提供用户主体名称：

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

接下来，需要获取希望将其访问权限授予用户的 RBAC 内置角色的角色定义 ID。 以下命令展示了如何获取“Owner”角色的角色定义 ID：

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

现在，创建托管应用程序定义资源。 托管应用程序只包含存储帐户。

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

命令完成后，资源组中会有一个托管应用程序定义。 

前述示例中使用的部分参数包括：

* **resource-group**：在其中创建托管应用程序定义的资源组的名称。
* lock-level：在托管资源组上放置的锁的类型。 它防止客户对此资源组执行不良操作。 当前，ReadOnly 是唯一受支持的锁级别。 当指定了 ReadOnly 时，客户只能读取托管资源组中存在的资源。
* authorizations：描述用于授予对托管资源组权限的主体 ID 和角色定义 ID。 它是以 `<principalId>:<roleDefinitionId>` 格式指定的。 另外还可以为此属性指定多个值。 如果需要多个值，则应当以 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 形式指定它们。 请以空格分隔多个值。
* **package-file-uri**：包含所需文件的 .zip 包的位置。 该包至少包含 mainTemplate.json 和 createUiDefinition.json 文件。 mainTemplate.json 定义作为托管应用程序的一部分预配的 Azure 资源。 该模板与常规资源管理器模板并没有不同。 createUiDefinition.json：生成用户界面，供用户通过门户创建托管应用程序。

## <a name="create-resource-group-for-managed-application"></a>为托管应用程序创建资源组

此时可以部署托管应用程序了。 

需要一个资源组来包含已部署的托管应用程序。 使用 westcentralus 作为位置。

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>部署托管应用程序

请使用以下命令部署应用程序：

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

前述示例中使用的部分参数包括：

* **managedapp-definition-id**：此前在本文中创建的定义的 ID。
* **managed-rg-id**：与托管应用程序关联的资源所在资源组的 ID。 该命令创建此资源组。 它不得在运行该命令之前存在。 此资源组是由发布者管理的。 
* **resource-group**：在其中创建托管应用程序资源的资源组。
* **parameters**：与托管应用程序关联的资源所需的参数。

部署成功完成后，即可在 applicationGroup 中看到创建的托管应用程序。 在 infrastructureGroup 中创建了 storageAccount 资源。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅[托管应用程序概述](overview.md)。
* 有关文件示例，请参阅[托管应用程序示例](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)。
* 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
