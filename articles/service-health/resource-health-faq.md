---
title: Azure 资源运行状况常见问题解答 | Microsoft Docs
description: Azure 资源运行状况概述
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/05/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 5155faac6051f5a586255148f77a52a90745673e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-resource-health-faq"></a>Azure 资源运行状况常见问题解答
获取 Azure 资源运行状况常见问题的解答。

## <a name="what-is-azure-resource-health"></a>什么是 Azure 资源运行状况？
资源运行状况在 Azure 问题影响资源时帮助你进行诊断并获得支持。 它通知你有关资源的当前和过去运行状况的信息，并帮助你缓解问题。 在需要有关 Azure 服务问题的帮助时，资源运行状况将提供技术支持。  

## <a name="what-is-the-resource-health-intended-for"></a>资源运行状况的用途是什么？
检测到资源出现问题以后，即可通过资源运行状况诊断根本原因。 它有助于减轻问题，并可在需要 Azure 服务问题的更多帮助时提供技术支持。

## <a name="what-health-checks-are-performed-by-resource-health"></a>资源运行状况执行何种运行状况检查？
资源运行状况根据[资源类型](resource-health-checks-resource-types.md)执行各种检查。 这些检查旨在针对三类问题： 
- 计划外事件，例如主机意外重新启动
- 计划内事件，例如主机 OS 按计划更新
- 用户操作触发的事件，例如用户重新启动虚拟机

## <a name="what-does-each-of-the-health-status-mean"></a>每个运行状态意味着什么？
有三种不同的运行状态：
- 可用：Azure 平台中没有任何已知能够影响该资源的问题
- 不可用：资源运行状况检测到影响资源的问题
- 未知：资源运行状况无法确定资源的运行状况，因为已停止接收有关该资源的信息。 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>未知状态意味着什么？ 我的资源是否出了问题？
当资源运行状况停止接收特定资源的相关信息时，资源运行状态设置为未知。 虽然在出现问题的情况下，此状态并没有确切地指示资源的状态，但仍然表明可能存在 Azure 问题。

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>如果资源不可用，如何获取帮助？
可以从资源运行状况边栏选项卡提交支持请求。 当资源因平台事件而不可用时，提交请求不需与 Microsoft 达成的支持协议。

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>资源运行状况是否会对平台问题导致的不可用与我的操作导致的不可用进行区分？
是的，当资源不可用时，资源运行状况会将根本原因归为以下类别之一： 
-   用户启动的操作
-   计划内事件 
-   计划外事件

在门户中，用户启动的操作用蓝色通知图标显示，而计划内和计划外事件则用红色警告图标显示。 [资源运行状况概述](Resource-health-overview.md)中提供了更多详细信息。  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>能否将资源运行状况与监视工具集成？
资源运行状况是一项服务，旨在帮助你诊断和减轻影响资源的 Azure 服务问题。 虽然可以使用资源运行状况 API 来程序性地获取运行状态，但建议使用指标来监视资源。 一旦检测到问题，资源运行状况就可以帮助确定根本原因，指导完成解决问题所需的操作。 请访问 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)，详细了解如何使用指标来检查资源。

## <a name="where-do-i-find-resource-health"></a>在何处查找资源运行状况？
登录 Azure 门户以后，即可通过多种方式访问资源运行状况：
- 导航到资源。 在左侧导航栏中，选择“资源运行状况”
- 转到 Azure Monitor 边栏选项卡。  在左侧导航栏中，选择“资源运行状况”。
- 通过选择门户右上角的问号，并选择“帮助 + 支持”，打开“帮助 + 支持”边栏选项卡。 该边栏选项卡打开后，选择“资源运行状况”

也可使用资源运行状况 API，获取资源运行状况的相关信息。

## <a name="is-resource-health-available-for-all-resource-types"></a>资源运行状况是否适用于所有资源类型？
如需资源运行状况支持的运行状况检查和资源类型的列表，可单击[此处](resource-health-checks-resource-types.md)。

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>如果我的资源显示可用，但我认为它不可用，该怎么办？
检查资源的运行状况时，可直接在运行状态下单击“报告不正确的运行状态”。 在提交报告之前，可以选择提供更多详细信息，说明为什么你认为当前的运行状态不正确。

## <a name="is-resource-health-available-for-all-azure-regions"></a>资源运行状况是否适用于所有 Azure 区域？ 
资源运行状况适用于除以下区域之外的所有 Azure 地理区域：
- 美国政府弗吉尼亚州
- 美国政府爱荷华州
- 美国 DoD 东部
- 美国 DoD 中部
- 德国中部
- 德国东北部

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>资源运行状况与 Azure 状态或服务运行状况仪表板有何不同？
资源运行状况提供的信息比 Azure 状态或服务运行状况仪表板提供的更具体。

[Azure 状态](https://status.azure.com)和服务运行状况仪表板会告知影响大量客户（例如某个 Azure 区域的客户）的服务问题，而资源运行状况则会公开更细致的事件，这些事件仅与特定的资源相关。 例如，如果某个主机意外重启，资源运行状况仅提醒其虚拟机在该主机上运行的那些客户。

必须注意的是，为了让你全面了解影响资源的事件，资源运行状况还会呈现在服务运行状况仪表板中发布的事件。

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>是否需要激活每个资源的资源运行状况？
否。运行状况信息适用于可以通过资源运行状况获取的所有资源类型。 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>是否需要为组织启用资源运行状况？
不会。  Azure 资源运行状况可以在 Azure 门户中访问，没有任何设置要求。

## <a name="is-resource-health-available-free-of-charge"></a>是否免费提供资源运行状况？
是的。  Azure 资源运行状况免费提供。

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>资源运行状况提供的建议有哪些？
资源运行状况会根据运行状态提供建议，目的是减少进行故障排除的时间。 这些建议针对可用资源，注重于如何解决客户遇到的最常见问题。 如果资源因 Azure 计划外事件而不可用，则会注重于提供恢复过程之中和之后的帮助。 

## <a name="next-steps"></a>后续步骤

了解有关资源运行状况的详细信息：
-  [Azure 资源运行状况概述](Resource-health-overview.md)
-  [可通过 Azure 资源运行状况使用的资源类型和运行状况检查](resource-health-checks-resource-types.md)
