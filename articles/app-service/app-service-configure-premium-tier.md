---
title: 为 Azure 应用服务配置 PremiumV2 层 |Microsoft Docs
description: 了解如何通过扩展到新的 PremiumV2 定价层提高 Azure 应用服务中的 Web、移动和 API 应用的性能。
keywords: 应用服务, azure 应用服务, 缩放, 可缩放, 应用服务计划, 应用服务成本
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 4c157ed905b7dc48c886b26987c164ef9a47f3c3
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714555"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>为 Azure 应用服务配置 PremiumV2 层

与现有定价层相比，新的 PremiumV2 定价层提供更快的处理器和 SSD 存储以及双倍的内存-内核比。 借助性能优势，可以通过在更少的实例上运行应用来节省资金。 在本文中，你将了解如何在 **PremiumV2** 层中创建应用或者将应用纵向扩展到 **PremiumV2** 层。

## <a name="prerequisites"></a>先决条件

若要将 Web 应用纵向扩展到 **PremiumV2**，需要在 Azure 应用服务中有一个在低于 **PremiumV2** 的定价层中运行的 Web 应用。

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 可用性

PremiumV2 层当前仅可供 _Windows_ 上的应用服务使用。 目前尚不支持 Linux 容器。

PremiumV2 已在大多数 Azure 区域中可用，并且在不断扩展。 若要查看它在你所在的区域是否可用，请在 [Azure Cloud Shell](../cloud-shell/overview.md) 中运行以下 Azure CLI 命令：

```azurecli-interactive
az appservice list-locations --sku P1V2
```

如果在创建应用或创建应用服务计划期间收到错误，则 **PremiumV2** 很可能不可用于你选择的区域。

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>在 PremiumV2 层中创建应用

应用服务应用的定价层是在运行它的[应用服务计划](azure-web-sites-web-hosting-plans-in-depth-overview.md)中定义的。 应用服务计划可以独立创建，也可以在创建 Web 应用的过程中创建。

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中配置应用服务计划时，选择“定价层”。 

选择“生产”，然后选择 **P1V2**、**P2V2** 或 **P3V2**，然后单击“应用”。

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> 如果没有看到 **P1V2**、**P2V2** 和 **P3V2** 作为选项列出，则 **PremiumV2** 在所选区域中不可用，或者所配置的 Linux 应用服务计划不支持 **PremiumV2**。

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>将现有应用纵向扩展到 PremiumV2 层

在将现有应用扩展到 **PremiumV2** 层之前，请确保 **PremiumV2** 在你的区域中可用。 有关信息，请参阅 [PremiumV2 可用性](#availability)。 如果它在你的区域中不可用，请参阅[从不受支持的区域纵向扩展](#unsupported)。

根据托管环境，纵向扩展可能需要执行额外的步骤。 

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中，打开你的应用服务应用页面。

在应用服务应用页面的左侧导航栏中，选择“纵向扩展(应用服务计划)”。

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

选择“生产”，然后选择 **P1V2**、**P2V2** 或 **P3V2**，然后单击“应用”。

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

如果操作成功完成，则应用的概述页会显示它目前处于 **PremiumV2** 层中。

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>如果遇到错误

某些应用服务计划无法纵向扩展到 PremiumV2 层。 如果纵向扩展操作遇到错误，则需要为应用使用新的应用服务计划。

在与现有应用服务应用相同的区域和资源组中创建一个 _Windows_ 应用服务计划。 根据[在 PremiumV2 层中创建应用](#create)中的步骤将其设置到 **PremiumV2** 层。 如果需要，请使用与现有应用服务计划相同的横向扩展配置（实例数、自动缩放，等等）。

再次打开你的应用服务应用页面。 在应用服务的左侧导航栏中，选择“更改应用服务计划”。

![](media/app-service-configure-premium-tier/change-plan.png)

选择所创建的应用服务计划。

![](media/app-service-configure-premium-tier/select-plan.png)

在更改操作完成后，应用在 **PremiumV2** 层中运行。

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>从不受支持的区域纵向扩展

如果应用在尚不可使用 **PremiumV2** 的区域中运行，则可以将应用移动到其他区域来利用 **PremiumV2**。 可以使用两个选项：

- 在新的 **PremiumV2** 计划中创建应用，然后重新部署应用程序代码。 根据[在 PremiumV2 层中创建应用](#create)中的步骤将其设置到 **PremiumV2** 层。 如果需要，请使用与现有应用服务计划相同的横向扩展配置（实例数、自动缩放，等等）。
- 如果应用已在现有的**高级**层中运行，则可以克隆应用连同所有应用设置、连接字符串和部署配置。

    ![](media/app-service-configure-premium-tier/clone-app.png)

    在“克隆应用”页中，可以在所需的区域中创建应用服务计划，并指定要克隆的设置。

## <a name="automate-with-scripts"></a>使用脚本自动化

可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview) 通过脚本在 **PremiumV2** 层中自动执行应用创建。

### <a name="azure-cli"></a>Azure CLI

以下命令在 _P1V2_ 中创建应用服务计划。 可以在 Cloud Shell 中运行此命令。 用于 `--sku` 的选项有 P1V2、_P2V2_ 和 _P3V2_。

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

以下命令在 _P1V2_ 中创建应用服务计划。 用于 `-WorkerSize` 的选项有 _Small_、_Medium_ 和 _Large_。

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>更多资源

[纵向扩展 Azure 中的应用](web-sites-scale.md)  
[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md)