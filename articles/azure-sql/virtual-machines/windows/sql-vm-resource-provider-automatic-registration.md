---
title: 自动注册 SQL VM 资源提供程序
description: 了解如何启用自动注册功能，以使用 Azure 门户自动向 SQL VM 资源提供程序注册过去和以后 SQL Server Vm。
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: 45285f2f26f1f17408f97bfede2b97e4c4752a5c
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762440"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>自动注册 SQL VM 资源提供程序
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 门户中启用自动注册功能，以便在轻型模式下使用 SQL VM 资源提供程序在 Azure Vm 上自动注册所有当前和将来 SQL Server。

本文介绍如何启用自动注册功能。 或者，可以 [注册单个 VM](sql-vm-resource-provider-register.md)，也可以使用 SQL VM 资源提供程序 [批量注册 vm](sql-vm-resource-provider-bulk-register.md) 。 

## <a name="overview"></a>概述

[SQL VM 资源提供程序](sql-vm-resource-provider-register.md#overview)允许您从 Azure 门户管理您的 SQL Server VM。 此外，资源提供程序还启用了一组强大的功能，包括 [自动修补](automated-patching.md)、 [自动备份](automated-backup.md)以及监视和可管理性功能。 它还会解锁[许可](licensing-model-azure-hybrid-benefit-ahb-change.md)和[编辑](change-sql-server-edition.md)灵活性。 以前，这些功能只适用于从 Azure 市场部署的 SQL Server VM 映像。 

利用自动注册功能，客户可以通过 SQL VM 资源提供程序在其 Azure 订阅中自动注册所有当前和未来的 SQL Server Vm。 这与手动注册不同，后者仅侧重于当前 SQL Server Vm。 

自动注册将以轻型模式注册 SQL Server Vm。 你仍需要 [手动升级到完整的可管理性模式](sql-vm-resource-provider-register.md#upgrade-to-full) ，才能利用完整的功能集。 

## <a name="prerequisites"></a>先决条件

若要将 SQL Server VM 注册到资源提供程序，需要： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 将[SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)部署到公共或 Azure 政府云的 Azure 资源模型[Windows 虚拟机](../../../virtual-machines/windows/quick-create-portal.md)。 


## <a name="enable"></a>启用

若要启用 Azure 门户中的 SQL Server Vm 的自动注册，请按照以下步骤操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到 [**SQL 虚拟机**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 资源页。 
1. 选择 " **自动 SQL Server VM 注册** " 打开 **自动注册** 页面。 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="选择 &quot;自动 SQL Server VM 注册&quot; 打开自动注册页面&quot;:::

1. 从下拉项中选择你的订阅。 
1. 通读条款，如果同意，请选择 &quot; **我接受**&quot;。 
1. 选择 &quot; **注册** " 以启用此功能，并使用 SQL VM 资源提供程序自动注册所有当前和未来 SQL Server vm。 这不会重新启动任何 Vm 上的 SQL Server 服务。 

## <a name="disable"></a>禁用

使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps) 来禁用自动注册功能。 禁用自动注册功能后，需要向 SQL VM 资源提供程序手动注册添加到订阅 SQL Server Vm。 这不会注销已注册的现有 SQL Server Vm。



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 禁用自动注册，请运行以下命令： 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 禁用自动注册，请运行以下命令： 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---


## <a name="next-steps"></a>后续步骤

将可管理性模式升级到 " [完全](sql-vm-resource-provider-register.md#upgrade-to-full) "，以利用 SQL VM 资源提供程序提供的完整功能集。 
