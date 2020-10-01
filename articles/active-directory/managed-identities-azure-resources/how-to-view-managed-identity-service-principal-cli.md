---
title: 查看托管标识的服务主体-Azure CLI-Azure AD
description: 使用 Azure CLI 查看托管标识的服务主体的分步说明。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c0f167a36ceff6a9b62e2a3ccf24ca6e13e6294
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611905"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>使用 Azure CLI 查看托管标识的服务主体

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何使用 Azure CLI 查看托管标识的服务主体。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。
- 如果还没有 Azure 帐户，请[注册免费帐户](https://azure.microsoft.com/free/)。
- 在[虚拟机](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)或[应用程序](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)上启用系统分配的标识。
- 若要运行示例脚本，有两个选项：
    - 使用 [Azure Cloud Shell](../../cloud-shell/overview.md)，你可使用代码块右上角的“试用”按钮打开它。
    - 通过安装最新版的 [Azure CLI](/cli/azure/install-azure-cli) 在本地运行脚本，然后使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。 使用与要在其中创建资源的 Azure 订阅关联的帐户。   

## <a name="view-the-service-principal"></a>查看服务主体

以下命令演示如何查看启用了托管标识的 VM 或应用程序的服务主体。 将 `<Azure resource name>` 替换为自己的值。

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>后续步骤

有关使用 Azure CLI 管理 Azure AD 服务主体的详细信息，请参阅 [az ad sp](/cli/azure/ad/sp)。
