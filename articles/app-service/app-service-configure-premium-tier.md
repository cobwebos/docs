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
ms.date: 07/25/2018
ms.author: cephalin
ms.openlocfilehash: 04996e772c2989be89ce551bfa45c57154de7b2d
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307783"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>为 Azure 应用服务配置 PremiumV2 层

与现有定价层相比，新的 PremiumV2 定价层提供更快的处理器和 SSD 存储以及双倍的内存-内核比。 借助性能优势，可以通过在更少的实例上运行应用来节省资金。 在本文中，你将了解如何在 **PremiumV2** 层中创建应用或者将应用纵向扩展到 **PremiumV2** 层。

## <a name="prerequisites"></a>先决条件

若要将 Web 应用纵向扩展到 **PremiumV2**，需要在 Azure 应用服务中提供一个 Web 应用，该 Web 应用在低于 **PremiumV2** 的定价层中运行，并且必须在支持 PremiumV2 的应用服务部署中运行。

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 可用性

**PremiumV2** 层适用于 _Windows_ 和 _Linux_ 上的应用服务。

**PremiumV2** 已在大多数 Azure 区域中推出。 若要查看它在你所在的区域是否可用，请在 [Azure Cloud Shell](../cloud-shell/overview.md) 中运行以下 Azure CLI 命令：

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>在 PremiumV2 层中创建应用

应用服务应用的定价层是在运行它的[应用服务计划](azure-web-sites-web-hosting-plans-in-depth-overview.md)中定义的。 应用服务计划可以独立创建，也可以在创建 Web 应用的过程中创建。

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中配置应用服务计划时，选择“定价层”。 

选择“生产”，然后选择 **P1V2**、**P2V2** 或 **P3V2**，然后单击“应用”。

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> 如果未看到 **P1V2**、**P2V2** 和 **P3V2** 作为选项列出，或者这些选项灰显，则 **PremiumV2** 可能在包含应用服务计划的基础应用服务部署中不可用。 有关更多详细信息，请参阅[从不受支持的资源组和区域组合纵向扩展](#unsupported)。

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>将现有应用纵向扩展到 PremiumV2 层

在将现有应用扩展到 **PremiumV2** 层之前，请确保 **PremiumV2** 可用。 有关信息，请参阅 [PremiumV2 可用性](#availability)。 如果 PremiumV2 不可用，请参阅[从不受支持的资源组和区域组合纵向扩展](#unsupported)。

根据托管环境，纵向扩展可能需要执行额外的步骤。 

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中，打开你的应用服务应用页面。

在应用服务应用页面的左侧导航栏中，选择“纵向扩展(应用服务计划)”。

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

选择“生产”，然后选择 **P1V2**、**P2V2** 或 **P3V2**，然后单击“应用”。

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

如果操作成功完成，则应用的概述页会显示它目前处于 **PremiumV2** 层中。

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>如果遇到错误

如果基础应用服务部署不支持 PremiumV2，某些应用服务计划无法纵向扩展到 PremiumV2 层。  有关更多详细信息，请参阅[从不受支持的资源组和区域组合纵向扩展](#unsupported)。

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>从不受支持的资源组和区域组合纵向扩展

如果应用在不支持 **PremiumV2** 的应用服务部署中运行，或者在目前不支持 **PremiumV2** 的区域中运行，则需要重新部署应用才能利用 **PremiumV2**。  可以使用两个选项：

- 创建**新**资源组，然后在**新**资源组中创建**新** Web 应用和应用服务计划，并在创建过程中选择所需的 Azure 区域。  创建新应用服务计划时，**必须**选择 **PremiumV2** 计划。  这可以确保资源组、应用服务计划和 Azure 区域的组合可让你在支持 **PremiumV2** 的应用服务部署中创建应用服务计划。  然后，将应用程序代码重新部署到新建的应用和应用服务计划。 以后可以根据需要，将应用服务计划从 **PremiumV2** 纵向缩减以节省成本，同时仍可使用 **PremiumV2** 成功纵向扩展。
- 如果应用已在现有的**高级**层中运行，则可将应用以及所有应用设置、连接字符串和部署配置克隆到使用 **PremiumV2** 的新应用服务计划。

    ![](media/app-service-configure-premium-tier/clone-app.png)

    在“克隆应用”页中，可以在所需的区域中创建使用 **PremiumV2** 的应用服务计划，并指定要克隆的应用设置和配置。

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