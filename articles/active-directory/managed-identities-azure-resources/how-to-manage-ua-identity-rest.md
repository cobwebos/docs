---
title: 如何使用 REST 管理 Azure 用户分配托管标识
description: 分步说明如何创建、列出和删除用户分配托管标识以进行 REST API 调用。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: 4bf77cd34ba985dfcfa568db0543150c0510c406
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300092"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>使用 REST API 调用创建、列出或删除用户分配托管标识

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure 资源托管标识使 Azure 服务能够向支持 Azure AD 身份验证的服务进行身份验证，而无需在代码中输入凭据。 

本文介绍如何使用 CURL 创建、列出和删除用户分配托管标识以进行 REST API 调用。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 如果使用 Windows，请安装[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)或使用 Azure 门户中的 [Azure Cloud Shell](../../cloud-shell/overview.md)。
- 如果使用[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)或 [Linux 分发版 OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)，请[安装 Azure CLI 本地控制台](/cli/azure/install-azure-cli)。
- 如果使用 Azure CLI 本地控制台，请使用 `az login` 和与要用于部署或检索用户分配托管标识信息的 Azure 订阅关联的帐户登录 Azure。
- 使用 `az account get-access-token` 检索持有者访问令牌，进而执行以下用户分配托管标识操作。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识 

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色分配。

要创建用户分配托管标识，请对 Azure 资源管理器 API 使用以下 CURL 请求。 将 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<USER ASSIGNED IDENTITY NAME>`、`<LOCATION>` 和 `<ACCESS TOKEN>` 的值换为自己的值：

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>列出用户分配的托管标识

若要列出/读取用户分配的托管标识，你的帐户需要[托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)或[托管标识参与者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色分配。

要列出用户分配托管标识，请对 Azure 资源管理器 API 使用以下 CURL 请求。 将 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>` 和 `<ACCESS TOKEN>` 的值替换为自己的值：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>删除用户分配的托管标识

若要删除用户分配的托管标识，你的帐户需要[托管标识参与者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色分配。

要删除用户分配托管标识，请对 Azure 资源管理器 API 使用以下 CURL 请求。 将 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>` 和 `<ACCESS TOKEN>` 参数值替换为自己的值：

> [!NOTE]
> 删除用户分配托管标识不会从将其分配到的任何资源中删除引用。 要使用 CURL 从 VM 中删除用户分配托管标识，请参阅[从 Azure VM 中删除用户分配的标识](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm)。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>后续步骤

要了解如何使用 CURL 将用户分配托管标识分配给 Azure VM/VMSS，请参阅[使用 REST API 调用在 Azure VM 上配置 Azure 资源托管标识](qs-configure-rest-vm.md#user-assigned-managed-identity)和[使用 REST API 调用在虚拟机规模集上配置 Azure 资源托管标识](qs-configure-rest-vmss.md#user-assigned-managed-identity)。


