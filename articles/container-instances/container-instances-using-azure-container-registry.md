---
title: "从 Azure 容器注册表部署到 Azure 容器实例 | Azure Docs"
description: "从 Azure 容器注册表部署到 Azure 容器实例"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: fcb21785584c3c5fb41f5ceb70346e3e05a30d93
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>从 Azure 容器注册表部署到 Azure 容器实例

Azure 容器注册表是用于 Docker 容器映像的基于 Azure 的专用注册表。 本文介绍如何将存储在 Azure 容器注册表中的容器映像部署到 Azure 容器实例。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

Azure CLI 包括用于创建和管理 Azure 容器实例中的容器的命令。 如果在 `create` 命令中指定专用映像，还可以指定使用容器注册表进行身份验证所需的映像注册表密码。

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --image-registry-password myRegistryPassword --resource-group myresourcegroup
```

`create` 命令还支持指定 `image-registry-login-server` 和 `image-registry-username`。 但是，默认情况下，Azure 容器注册表的登录服务器只是 *registryname*.azurecr.io，用户名是 *registryname*，因此这些值是从映像名称推断的（如果未显式提供）。

## <a name="using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

可以在 Azure 资源管理器模板中指定 Azure 容器注册表的属性。 直接在容器组的定义中包括 `imageRegistryCredentials` 属性：

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

若要避免直接在模板中存储容器注册表的密码，建议将其存储为 [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) 中的机密，然后按照 [Azure 资源管理器与 Key Vault 之间的本机集成](../azure-resource-manager/resource-manager-keyvault-parameter.md)在模板中引用该机密。


## <a name="next-steps"></a>后续步骤

通过[完成此教程](container-instances-tutorial-prepare-app.md)了解如何生成容器，将这些容器推送到专用容器注册表，并将这些容器部署到 Azure 容器实例。
