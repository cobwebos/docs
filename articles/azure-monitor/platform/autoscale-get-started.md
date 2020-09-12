---
title: Azure 中的自动缩放入门
description: 了解如何在 Azure 中缩放资源：Web 应用、云服务、虚拟机或虚拟机规模集。
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: d37b1bad397e6170e2a7992a0a9671d6ca9c25ef
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651712"
---
# <a name="get-started-with-autoscale-in-azure"></a>Azure 中的自动缩放入门
本文介绍如何在 Microsoft Azure 门户中为资源指定自动缩放设置。

Azure Monitor 自动缩放仅适用于[虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[云服务](https://azure.microsoft.com/services/cloud-services/)、[应用服务 - Web 应用](https://azure.microsoft.com/services/app-service/web/)和 [API 管理服务](../../api-management/api-management-key-concepts.md)。

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>了解订阅中的自动缩放设置

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

可在 Azure Monitor 中查找自动缩放功能适用的所有资源。 按下列步骤进行分步演练：

1. 打开 [Azure 门户][1]。
1. 单击左窗格中的“Azure Monitor”图标。
  ![打开 Azure Monitor][2]
1. 单击“自动缩放”以查看自动缩放适用的所有资源及其当前的自动缩放状态。
  ![了解 Azure Monitor 中的自动缩放功能][3]

可使用顶部的筛选器窗格缩小列表的范围，以选择特定资源组中的资源、特定的资源类型或特定资源。

对于每个资源，将会看到其当前实例计数和自动缩放状态。 自动缩放状态可以是：

- **未配置**：尚未对此资源启用自动缩放功能。
- **已启用**：已对此资源启用自动缩放功能。
- **Disabled**：已对此资源禁用自动缩放功能。

## <a name="create-your-first-autoscale-setting"></a>创建第一个自动缩放设置

现在，让我们完成一个简单的分步演练，以创建第一个自动缩放设置。

1. 在 Azure Monitor 中打开“自动缩放”边栏选项卡，然后选择要缩放的资源。 （以下步骤使用与某 Web 应用关联的应用服务计划。 [仅需 5 分钟，就可在 Azure 中创建首个 ASP.NET Web 应用。][4]）
1. 请注意当前实例计数为 1。 单击“启用自动缩放”。
  ![新 Web 应用的缩放设置][5]
1. 提供缩放设置的名称，然后单击“添加规则”。 请注意右侧以上下文窗格形式打开的缩放规则选项。 默认情况下，这将选项设置为当资源的 CPU 百分比超过 70% 时，将实例计数缩放 1 个单位。 请将此选项保留默认值，并单击“添加”。
  ![为 Web 应用创建缩放设置][6]
1. 现已创建第一个缩放规则。 请注意，UX 建议了最佳做法，并指出“建议至少在规则中包含一个缩放设置”。 为此，请执行以下操作：

    a. 单击“添加规则”。

    b. 将“运算符”设置为“小于”。 

    c. 将“阈值”设置为 20。 

    d. 将“操作”设置为“按以下值递减计数”。 

   现在应已创建一个可以根据 CPU 使用率进行扩展/缩减的缩放设置。
   ![基于 CPU 进行缩放][8]
1. 单击“保存” 。

祝贺！ 现已成功创建第一个缩放设置，用于根据 CPU 使用率自动缩放 Web 应用。

> [!NOTE]
> 若要开始使用虚拟机规模集或云服务角色，才可采用相同步骤操作。

## <a name="other-considerations"></a>其他注意事项
### <a name="scale-based-on-a-schedule"></a>基于计划的缩放
除了基于 CPU 进行缩放，还可设置为在特定的星期日期按其他方式缩放。

1. 单击“添加缩放条件”。
1. 缩放模式和规则的设置方式与默认条件的相同。
1. 为计划选择“重复特定的星期日期”。
1. 选择星期日期，以及需应用缩放条件的开始/结束时间。

![基于计划的缩放条件][9]
### <a name="scale-differently-on-specific-dates"></a>在特定的日期以不同的方式缩放
除了基于 CPU 进行缩放，还可设置为在特定日期按其他方式缩放。

1. 单击“添加缩放条件”。
1. 缩放模式和规则的设置方式与默认条件的相同。
1. 为计划选择“指定开始/结束日期”。
1. 选择开始/结束日期，以及需应用缩放条件的开始/结束时间。

![基于日期的缩放条件][10]

### <a name="view-the-scale-history-of-your-resource"></a>查看资源的缩放历史记录
每次增加或缩小资源后，都会在活动日志中记录一个事件。 切换到“运行历史记录”选项卡即可查看资源在过去 24 小时的缩放历史记录。

![运行历史记录][11]

若要查看完整的缩放历史记录（最长 90 天），请选择“单击此处查看更多详细信息”。 随后将启动活动日志，其中包含已预先选择“自动缩放”的资源和类别。

### <a name="view-the-scale-definition-of-your-resource"></a>查看资源的缩放定义
“自动缩放”是一种 Azure 资源管理器资源。 切换到“JSON”选项卡即可在 JSON 中查看缩放定义。

![缩放定义][12]

如果需要，可以直接在 JSON 中进行更改。 这些更改将在保存后生效。

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>禁用“自动缩放”并手动缩放实例
有时，可能需要禁用当前的缩放设置并手动缩放资源。

单击顶部的“禁用自动缩放”按钮。
![禁用自动缩放][13]

> [!NOTE]
> 此选项将禁用你的配置。 但是，再次启用“自动缩放”后，则可恢复此设置。

现在，可手动设置要缩放到的实例数。

![设置手动缩放][14]

始终可单击“启用自动缩放”，再单击“保存”来恢复自动缩放。 

## <a name="route-traffic-to-healthy-instances-app-service"></a>将流量路由到 (应用服务的正常实例) 

向外扩展到多个实例时，应用服务可以对实例执行运行状况检查，以将流量路由到正常的实例。 为此，请打开应用服务的门户，并选择 "**监视**" 下的**运行状况检查**。 选择 " **启用** "，并在应用程序中提供有效的 URL 路径，例如 `/health` 或 `/api/health` 。 单击“保存” 。

### <a name="health-check-path"></a>运行状况检查路径

路径必须在两分钟内响应，状态代码介于200与299之间 (包含) 。 如果路径在两分钟内未响应，或返回范围之外的状态代码，则实例将被视为 "不正常"。 运行状况检查与应用服务的身份验证和授权功能集成，即使启用了这些 microsoft.powershell.secuity 功能，系统也会到达终结点。 如果你使用自己的身份验证系统，运行状况检查路径必须允许匿名访问。 如果站点启用了 HTTP**S** ，则 healthcheck 将首先命中 http 终结点，然后接受 307 HTTP 重定向到 HTTPS 终结点。

运行状况检查路径应检查应用程序的关键组件。 例如，如果应用程序依赖于数据库和消息系统，则运行状况检查终结点应连接到这些组件。 如果应用程序无法连接到关键组件，路径应返回500级别的响应代码，以指示该应用程序不正常。

#### <a name="security"></a>安全性 

大型企业的开发团队通常需要遵守其公开的 Api 的安全要求。 若要保护 healthcheck 终结点，你应该首先使用 [IP 限制](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)、 [客户端证书](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)或虚拟网络等功能来限制对应用程序的访问。 你可以通过要求传入请求的匹配来保护 healthcheck 终结点本身 `User-Agent` `ReadyForRequest/1.0` 。 由于之前的安全功能已对该请求进行了保护，因此不能欺骗用户代理。

### <a name="behavior"></a>行为

如果提供了运行状况检查路径，应用服务会在所有实例上对路径进行 ping 操作。 如果在5个 ping 操作后未收到成功的响应代码，则将该实例视为 "不正常"。 不正常的实例 (s 将从负载均衡器轮换中排除) 。 此外，在纵向扩展或缩减时，应用服务会对运行状况检查路径进行 ping 操作，以确保新实例已准备好进行请求。

其他正常运行的实例可能会提高负载。 若要避免出现剩余的情况，则不会再包括一半的实例。 例如，如果应用服务计划扩展到4个实例，其中3个实例不正常，最多2个将从 loadbalancer 旋转中排除。 其他2个实例 (1 正常，1个不正常) 将继续接收请求。 在所有实例都运行不正常的最糟糕的情况下，将不包括任何实例。

如果实例在一小时内保持不正常，则会将其替换为新的实例。 每个应用服务计划中，最多只能将一个实例替换为每小时一次。

### <a name="monitoring"></a>监视

提供应用程序的运行状况检查路径后，可以使用 Azure Monitor 来监视站点的运行状况。 在门户中的 **运行状况检查** 边栏选项卡上，单击顶部工具栏中的 **度量值** 。 这会打开一个新的边栏选项卡，你可以在其中查看站点的历史运行状况状态并创建新的警报规则。 有关监视网站的详细信息， [请参阅 Azure Monitor 上的指南](../../app-service/web-sites-monitor.md)。

## <a name="next-steps"></a>后续步骤
- [创建活动日志警报以监视订阅上的所有自动缩放引擎操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [创建活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
