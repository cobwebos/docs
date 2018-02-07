---
title: "从 Azure 容器注册表部署到 Azure 容器实例"
description: "了解如何使用容器映像在 Azure 容器注册表中部署 Azure 容器实例中的容器。"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/24/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c69b95f66bf2eaf4975961da5b25f5ac6172798c
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>从 Azure 容器注册表部署到 Azure 容器实例

Azure 容器注册表是用于 Docker 容器映像的基于 Azure 的专用注册表。 本文介绍如何将存储在 Azure 容器注册表中的容器映像部署到 Azure 容器实例。

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

Azure CLI 包括用于创建和管理 Azure 容器实例中的容器的命令。 如果在 [az container create][az-container-create] 命令中指定专用映像，还可以指定使用容器注册表进行身份验证所需的映像注册表密码。

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

[az container create][az-container-create] 命令还支持指定 `--registry-login-server` 和 `--registry-username`。 但是，Azure 容器注册表的登录服务器都是 registryname.azurecr.io，默认用户名是 registryname，因此这些值是从映像名称推断的（如果未显式提供）。

## <a name="deploy-with-azure-resource-manager-template"></a>使用 Azure 资源管理器模板进行部署

通过将 `imageRegistryCredentials` 属性包含到以下容器组义中，可以在 Azure 资源管理器模板中指定 Azure 容器注册表的属性：

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

若要避免直接在模板中存储容器注册表的密码，建议将其存储为 [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) 中的机密，然后按照 [Azure 资源管理器与 Key Vault 之间的本机集成](../azure-resource-manager/resource-manager-keyvault-parameter.md)在模板中引用该机密。

## <a name="deploy-with-azure-portal"></a>使用 Azure 门户仅部署

如果在 Azure 容器注册表中维护容器映像，可通过 Azure 门户在 Azure 容器实例中轻松创建容器。

1. 在 Azure 门户中，导航到容器注册表。

1. 选择“存储库”，然后选择想要从中进行部署的存储库，右键单击想要部署的容器映像的标记，然后选择“运行实例”。

    ![Azure 门户中 Azure 容器注册表中的“运行实例”][acr-runinstance-contextmenu]

1. 输入容器和资源组的名称。 也可根据需要更改默认值。

    ![Azure 容器实例的创建菜单][acr-create-deeplink]

1. 部署完成后，可从通知窗格导航至容器组，查找其 IP 地址和其他属性。

    ![Azure 容器实例容器组的详细信息视图][aci-detailsview]

## <a name="service-principal-authentication"></a>服务主体身份验证

如果禁用了 Azure 容器注册表的管理员用户，创建容器实例时可以使用 Azure Active Directory [服务主体](../container-registry/container-registry-auth-service-principal.md)对注册表进行身份验证。 在无外设的方案中也建议使用服务主体进行身份验证，如以无人参与方式创建容器实例的脚本或应用程序。

有关详细信息，请参阅[使用 Azure 容器注册表从 Azure 容器实例进行身份验证](../container-registry/container-registry-auth-aci.md)。

## <a name="next-steps"></a>后续步骤

通过[完成此教程](container-instances-tutorial-prepare-app.md)了解如何生成容器，将这些容器推送到专用容器注册表，并将这些容器部署到 Azure 容器实例。

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create