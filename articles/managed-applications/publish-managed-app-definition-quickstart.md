---
title: 创建 Azure 托管应用程序定义 | Microsoft Docs
description: 演示如何创建适用于组织中成员的 Azure 托管应用程序。
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 1f80d7e63d994f0e3eb3733b99afaa1b056f4686
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804900"
---
# <a name="publish-an-azure-managed-application-definition"></a>发布 Azure 托管应用程序定义

本快速入门简单介绍了如何使用托管应用程序。 请先向组织中用户的内部目录添加托管应用程序定义， 为简单起见，我们已为托管应用程序生成了文件。 这些文件可通过 GitHub 获取。 可以在[创建服务目录应用程序](publish-service-catalog-app.md)教程中了解如何生成这些文件。

完成后，你将拥有一个名为 **appDefinitionGroup** 且具有托管应用程序定义的资源组。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>为定义创建资源组

托管应用程序定义存在于资源组中。 该资源组是在其中部署和管理 Azure 资源的逻辑集合。

若要创建资源组，请使用以下命令：

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>创建托管应用程序定义

定义托管应用程序时，选择为使用者管理资源的用户、组或应用程序。 此标识对托管资源组的权限与所分配的角色相对应。 通常创建 Azure Active Directory 组来管理资源。 但在本文中，请使用自己的标识。

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
* lock-level：在托管资源组上放置的锁的类型。 它防止客户对此资源组执行不良操作。 当前，ReadOnly 是唯一受支持的锁级别。 当指定了 ReadOnly 时，客户只能读取托管资源组中存在的资源。 授予对托管资源组的访问权限的发布者标识不受该锁控制。
* authorizations：描述用于授予对托管资源组权限的主体 ID 和角色定义 ID。 它是以 `<principalId>:<roleDefinitionId>` 格式指定的。 如果需要多个值，请以 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 格式指定它们。 请以空格分隔这些值。
* **package-file-uri**：包含所需文件的 .zip 包的位置。 该包必须包含 **mainTemplate.json** 和 **createUiDefinition.json** 文件。 mainTemplate.json 定义作为托管应用程序的一部分创建的 Azure 资源。 该模板与常规资源管理器模板并没有不同。 createUiDefinition.json：生成用户界面，供用户通过门户创建托管应用程序。

## <a name="next-steps"></a>后续步骤

已发布托管应用程序定义。 现在，了解如何部署该定义的实例。

> [!div class="nextstepaction"]
> [快速入门：部署服务目录应用](deploy-service-catalog-quickstart.md)