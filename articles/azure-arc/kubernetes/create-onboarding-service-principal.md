---
title: 创建支持 Azure Arc 的加入服务主体（预览）
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: '创建支持 Azure Arc 的加入服务主体 '
keywords: Kubernetes、Arc、Azure、容器
ms.openlocfilehash: 02689dba32c8cc91e4a4a4de4dee98bc990b4dd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050071"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>创建支持 Azure Arc 的加入服务主体（预览）

## <a name="overview"></a>概述

可以使用具有有限权限的角色分配的服务主体，将 Kubernetes 群集加入到 Azure Arc。此方法在持续集成和持续部署 (CI/CD) 管道（如 Azure Pipelines 和 GitHub Actions）中非常有用。

以下步骤提供了使用服务主体将 Kubernetes 群集加入 Azure Arc 的相关演练。

## <a name="create-a-new-service-principal"></a>创建新服务主体

使用信息性名称创建新服务主体。 请注意，该名称必须在 Azure Active Directory 租户中是唯一的：

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**输出：**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>分配权限

创建新的服务主体后，将 "Kubernetes Cluster-Azure Arc 载入" 角色分配给新创建的主体。 这是具有有限权限的内置 Azure 角色，只允许主体向 Azure 注册群集。 主体无法更新、删除或修改订阅中的任何其他群集或资源。

由于功能有限，客户可以轻松地将此主体重用于加入多个群集。

可以在分配角色时传入相应的 `--scope` 参数，从而进一步限制权限。 这使得客户可以限制群集注册。 各类 `--scope` 参数支持以下场景：

| 资源  | `scope` 参数| 效果 |
| ------------- | ------------- | ------------- |
| 订阅 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | 服务主体可以在给定订阅的现有资源组内注册任何群集 |
| 资源组 | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | 服务主体只能在资源组 `myGroup` 中注册群集 |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**输出：**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>在 Azure CLI 中使用服务主体

引用新创建的服务主体：

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>后续步骤

* [使用 Azure Policy 控制群集配置](./use-azure-policy.md)
