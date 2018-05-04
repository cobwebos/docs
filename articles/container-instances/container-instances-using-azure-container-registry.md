---
title: 从 Azure 容器注册表部署到 Azure 容器实例
description: 了解如何使用容器映像在 Azure 容器注册表中部署 Azure 容器实例中的容器。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 7a7d2aa61f25bc4782c6a1a6744e329935477f8c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>从 Azure 容器注册表部署到 Azure 容器实例

Azure 容器注册表是用于 Docker 容器映像的基于 Azure 的专用注册表。 本文介绍如何将存储在 Azure 容器注册表中的容器映像部署到 Azure 容器实例。

## <a name="prerequisites"></a>先决条件

**Azure 容器注册表**：需要一个 Azure 容器注册表（注册表中的至少一个容器映像）才能完成本文中的步骤。 如果需要注册表，请参阅[使用 Azure CLI 创建容器注册表](../container-registry/container-registry-get-started-azure-cli.md)。

**Azure CLI**：本文中的命令行示例使用 [Azure CLI](/cli/azure/)，并采用适用于 Bash shell 的格式。 可在本地[安装 Azure CLI](/cli/azure/install-azure-cli)，或使用 [Azure Cloud Shell][cloud-shell-bash]。

## <a name="configure-registry-authentication"></a>配置注册表身份验证

在任意生产方案中，应使用[服务主体](../container-registry/container-registry-auth-service-principal.md)提供对 Azure 容器注册表的访问权限。 使用服务主体可以提供对容器映像的基于角色的访问控制。 例如，可将服务主体配置为拥有注册表的仅限提取的访问权限。

在本部分，我们将创建一个 Azure Key Vault 和一个服务主体，并将服务主体的凭据存储在保管库中。

### <a name="create-key-vault"></a>创建 Key Vault

如果 [Azure Key Vault](/azure/key-vault/) 中没有保管库，请在 Azure CLI 中使用以下命令创建一个保管库。

将 `RES_GROUP` 变量更新为要在其中创建 Key Vault 的资源组的名称，将 `ACR_NAME` 更新为容器注册表的名称。 在 `AKV_NAME` 中指定新 Key Vault 的名称。 保管库名称必须在 Azure 中唯一、长度必须为 3-24 个字母数字字符、以字母开头、以字母或数字结尾，并且不能包含连续的连字符。

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>创建服务主体并存储凭据

现在需要创建服务主体，并将其凭据存储在 Key Vault 中。

以下命令使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 创建服务主体，使用 [az keyvault secret set][az-keyvault-secret-set] 将服务主体的**密码**存储在保管库中。

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

上述命令中的 `--role` 参数配置具有“读取者”角色的服务主体，该服务主体对注册表拥有仅限提取的访问权限。 若要授予推送和提取访问权限，请将 `--role` 参数更改为 *contributor*。

接下来，将服务主体的 *appId*（传递给 Azure 容器注册表用于身份验证的**用户名**）存储在保管库中。

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

现已创建 Azure Key Vault 并在其中存储了两个机密：

* `$ACR_NAME-pull-usr`：用作容器注册表**用户名**的服务主体 ID。
* `$ACR_NAME-pull-pwd`：用作容器注册表**密码**的服务主体密码。

现在，当你或你的应用程序和服务从注册表提取映像时，可以按名称引用这些机密。

## <a name="deploy-container-with-azure-cli"></a>使用 Azure CLI 部署容器

将服务主体凭据存储到 Azure Key Vault 机密中后，应用程序和服务可以使用它们来访问专用注册表。

执行以下 [az container create][az-container-create] 命令来部署容器实例。 该命令使用 Azure Key Vault 中存储的服务主体凭据对容器注册表进行身份验证，并假设事先已将 [aci-helloworld](container-instances-quickstart.md) 映像推送到注册表。 如果想要使用注册表中的不同映像，请更新 `--image` 值。

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_NAME.azurecr.io/aci-helloworld:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

`--dns-name-label` 值必须在 Azure 中唯一，因此，上述命令会将一个随机数字追加到容器的 DNS 名称标签。 该命令的输出显示容器的完全限定域名 (FQDN)，例如：

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

成功启动容器后，可在浏览器导航到容器的 FQDN，以验证应用程序是否成功运行。

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

有关在资源管理器模板中引用 Azure Key Vault 机密的详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/resource-manager-keyvault-parameter.md)。

## <a name="deploy-with-azure-portal"></a>使用 Azure 门户仅部署

如果在 Azure 容器注册表中维护容器映像，可通过 Azure 门户在 Azure 容器实例中轻松创建容器。

1. 在 Azure 门户中，导航到容器注册表。

1. 选择“存储库”，然后选择想要从中进行部署的存储库，右键单击想要部署的容器映像的标记，然后选择“运行实例”。

    ![Azure 门户中 Azure 容器注册表中的“运行实例”][acr-runinstance-contextmenu]

1. 输入容器和资源组的名称。 也可根据需要更改默认值。

    ![Azure 容器实例的创建菜单][acr-create-deeplink]

1. 部署完成后，可从通知窗格导航至容器组，查找其 IP 地址和其他属性。

    ![Azure 容器实例容器组的详细信息视图][aci-detailsview]

## <a name="next-steps"></a>后续步骤

有关 Azure 容器注册表身份验证的详细信息，请参阅[使用 Azure 容器注册表进行身份验证](../container-registry/container-registry-authentication.md)。

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set