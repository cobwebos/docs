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
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: aa1c4ea379c10dff246e2f924a345f9fa444aa64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>从 Azure 容器注册表部署到 Azure 容器实例

Azure 容器注册表是用于 Docker 容器映像的基于 Azure 的专用注册表。 本文介绍如何将存储在 Azure 容器注册表中的容器映像部署到 Azure 容器实例。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

Azure CLI 包括用于创建和管理 Azure 容器实例中的容器的命令。 如果在 `create` 命令中指定专用映像，还可以指定使用容器注册表进行身份验证所需的映像注册表密码。

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

`create` 命令还支持指定 `registry-login-server` 和 `registry-username`。 但是，Azure 容器注册表的登录服务器都是 registryname.azurecr.io，默认用户名是 registryname，因此这些值是从映像名称推断的（如果未显式提供）。

## <a name="using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

通过将 `imageRegistryCredentials` 属性包含到以下容器组义中，可以在 Azure 资源管理器模板中指定 Azure 容器注册表的属性：

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

## <a name="using-the-azure-portal"></a>使用 Azure 门户

如果在 Azure 容器注册表中维护容器映像，可通过 Azure 门户在 Azure 容器实例中轻松创建容器。

1. 在 Azure 门户中，导航到容器注册表。

2. 选择存储库。

    ![Azure 门户中的“Azure 容器注册表”菜单][acr-menu]

3. 选择希望从中部署的存储库。

4. 右键单击想要部署的容器映像的标记。

    ![用于启动包含 Azure 容器实例的容器的上下文菜单][acr-runinstance-contextmenu]

5. 输入容器和资源组的名称。 也可根据需要更改默认值。

    ![Azure 容器实例的创建菜单][acr-create-deeplink]

6. 部署完成后，可从通知窗格导航至容器组，查找其 IP 地址和其他属性。

    ![Azure 容器实例容器组的详细信息视图][aci-detailsview]

## <a name="next-steps"></a>后续步骤

通过[完成此教程](container-instances-tutorial-prepare-app.md)了解如何生成容器，将这些容器推送到专用容器注册表，并将这些容器部署到 Azure 容器实例。

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
