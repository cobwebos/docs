---
title: 使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证
description: 了解如何使用 Azure Active Directory 服务主体从 Azure Kubernetes 服务访问专用容器注册表中的映像。
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/11/2018
ms.author: marsma
ms.openlocfilehash: b56f2a8b2ae8cf04b8c27ab657be3f4d77ee7402
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205385"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证

结合使用 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS) 时，需要建立身份验证机制。 本文详细介绍这两种 Azure 服务之间的建议身份验证配置。

## <a name="grant-aks-access-to-acr"></a>向 ACR 授予 AKS 访问权限

创建 AKS 群集时，Azure 还会创建一个服务主体以支持与其他 Azure 资源的群集可操作性。 可以使用此自动生成的服务主体向 ACR 注册表进行身份验证。 若要执行此操作，需要创建一个 Azure AD [角色分配](../role-based-access-control/overview.md#role-assignment)，它会授予群集的服务主体访问容器注册表的权限。

使用以下脚本授予 AKS 生成的服务主体访问 Azure 容器注册表的权限。 在运行脚本前，修改环境的 `AKS_*` 和 `ACR_*` 变量。

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>使用 Kubernetes 机密访问

在某些情况下，可能无法向自动生成的 AKS 服务主体分配授予其访问 ACR 权限所需的角色。 例如，由于组织的安全模型，你可能在 Azure AD 目录中没有足够的权限将角色分配给 AKS 生成的服务主体。 在这种情况下，可以创建新的服务主体，然后向其授予使用 Kubernetes 映像请求机密访问容器注册表的权限。

使用以下脚本创建新的服务主体（你将使用其凭据来获取 Kubernetes 映像请求机密）。 在运行脚本之前，修改环境的 `ACR_NAME` 变量。

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a 'Reader' role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

现在可以在 Kubernetes [映像请求机密][image-pull-secret]中存储服务主体的凭据，当在 AKS 群集中运行容器时，会引用这些凭据。

使用以下 kubectl 命令创建 Kubernetes 机密。 将 `<acr-login-server>` 替换为 Azure 容器注册表的完全限定名称（它的格式为“acrname.azurecr.io”）。 将 `<service-principal-ID>` 和 `<service-principal-password>` 替换为运行之前的脚本所获得的值。

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

现在可以通过在 `imagePullSecrets` 参数中指定 Kubernetes 机密的名称（在这种情况下，名称为“acr-auth”），以在 pod 部署中使用此机密：

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
