---
title: "使用 Azure 容器注册表从 Azure 容器服务进行身份验证"
description: "了解如何使用 Azure Active Directory 服务主体从 Azure 容器服务访问专用容器注册表中的映像。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.openlocfilehash: 0cf09b55b8d144e8a58f7b2f73c99a0bacfb252d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-service"></a>使用 Azure 容器注册表从 Azure 容器服务进行身份验证

结合使用 Azure 容器注册表 (ACR) 和 Azure 容器服务 (AKS) 时，需要建立身份验证机制。 本文档详细介绍这两种 Azure 服务之间的建议身份验证配置。

## <a name="grant-aks-access-to-acr"></a>向 ACR 授予 AKS 访问权限

创建 AKS 群集时，还会创建一个服务主体，用于管理对 Azure 资源的群集可操作性。 此服务主体也可用于向 ACR 注册表进行身份验证。 若要实现此目的，需要创建一个角色分配，以向 ACR 资源授予服务主体的读取访问权限。 

可使用以下示例完成此操作。

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
az role assignment create --assignee $CLIENT_ID --role Contributor --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>使用 Kubernetes 机密访问

在某些情况下，AKS 使用的服务主体不适用于 ACR 注册表。 对于这些情况，可创建唯一的服务主体并将其限制为仅限 ACR 注册表。

可使用以下脚本创建服务主体。 

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id. 
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource. 
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

服务主体凭据现在可存储在 Kubernetes [映像请求机密][image-pull-secret]中，当在 AKS 群集中运行容器时，会引用这些凭据。 

以下命令创建 Kubernetes 机密。 将服务器名称替换为 ACR 登录服务器，将用户名替换为服务主体 ID，将密码替换为服务主体密码。

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

可通过使用 `ImagePullSecrets` 参数在 Pod 部署中使用 Kubernetes 机密。 

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
