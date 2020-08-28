---
title: 使用 PowerShell 查看托管标识的服务主体 - Azure AD
description: 使用 PowerShell 查看托管标识的服务主体的分步说明。
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
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d958ddf977d0f6926863fa661b25e76fe031822d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009281"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>使用 PowerShell 查看托管标识的服务主体

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 PowerShell 查看托管标识的服务主体。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。
- 如果还没有 Azure 帐户，请[注册免费帐户](https://azure.microsoft.com/free/)。
- 在[虚拟机](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)或[应用程序](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)上启用系统分配的标识。
- 安装最新版本的 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>查看服务主体

以下命令演示了如何查看启用了系统分配的标识的 VM 或应用程序的服务主体。 将 `<VM or application name>` 替换为自己的值。

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>后续步骤

有关使用 PowerShell 查看 Azure AD 服务主体的详细信息，请参阅 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)。


