---
title: 快速入门：创建 Front Door 配置文件以实现应用程序的高可用性
description: 本快速入门文章介绍如何创建 Front Door，以实现高度可用的高性能全局 Web 应用程序。
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 67a4f9eb3290ba09a2c19325464cf7ad224856e7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184519"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>快速入门：创建 Front Door，以实现高度可用的全局 Web 应用程序

本快速入门介绍如何创建 Front Door 配置文件，以实现全局 Web 应用程序的高度可用性和高性能。 

本快速入门中介绍的方案包括两个在不同 Azure 区域中运行的 Web 应用程序实例。 创建了基于相等[权重和相同优先级后端](front-door-routing-methods.md)的 Front Door 配置，可帮助将用户流量定向到运行应用程序的最靠近的站点后端集。 Front Door 持续监视 Web 应用程序，并在最靠近的站点不可用时提供目标为下一个可用后端的自动故障转移。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure 
通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="prerequisites"></a>先决条件
本快速入门要求部署在不同 Azure 区域（美国东部和西欧）运行的两个 Web 应用程序实例。   这两个 Web 应用程序实例都在主动/主动模式下运行，即其中任一实例都可随时接收流量，这不同于活动/待机配置（其中一个实例充当故障转移）。

1. 在屏幕左上方，选择“创建资源”   > “Web”   >   “Web 应用” >   “创建”。
2. 在“Web 应用”中输入或选择以下信息，并在未指定任何设置的地方输入默认设置  ：

     | 设置         | 值     |
     | ---              | ---  |
     | Name           | 输入 Web 应用的唯一名称  |
     | Resource group          | 选择“新建”  ，然后键入 myResourceGroupFD1  |
     | 应用服务计划/位置         | 选择“新建”  。  在应用服务计划中，输入 *myAppServicePlanEastUS*，然后选择“确定”。  
     |      位置  |   美国东部        |
    |||

3. 选择“创建”  。
4. 默认网站在 Web 应用成功部署时创建。
5. 重复步骤 1-3，使用以下设置在另一 Azure 区域创建另一个网站：

     | 设置         | 值     |
     | ---              | ---  |
     | Name           | 输入 Web 应用的唯一名称  |
     | Resource group          | 选择“新建”  ，然后键入 myResourceGroupFD2  |
     | 应用服务计划/位置         | 选择“新建”  。  在应用服务计划中，输入 *myAppServicePlanWestEurope*，然后选择“确定”。  
     |      位置  |   西欧      |
    |||


## <a name="create-a-front-door-for-your-application"></a>为应用程序创建 Front Door
### <a name="a-add-a-frontend-host-for-front-door"></a>A. 为 Front Door 添加前端主机
创建 Front Door 配置，基于两个后端之间的最低延迟定向用户流量。

1. 在屏幕的左上方，选择“创建资源” > “网络” > “Front Door” > “创建”     。
2. 在创建“Front Door”  中，先添加基本信息，并提供要在其中配置 Front Door 的订阅。 同样，与任何其他 Azure 资源类似，如果要创建新的 Front Door，还需要提供 ResourceGroup 和资源组区域。 最后，需要提供 Front Door 的名称。
3. 填充基本信息后，需要定义的第一步是配置的前端主机  。 结果应为有效的域名，如 `myappfrontend.azurefd.net`。 此主机名必须全局唯一，但 Front Door 将负责进行验证。 

### <a name="b-add-application-backend-and-backend-pools"></a>B. 添加应用程序后端和后端池

接下来，需要在后段池中为 Front Door 配置应用程序后端，以便了解应用程序所在的位置。 

1. 单击“+”图标添加后端池，然后为后端池指定名称  ，即 `myBackendPool`。
2. 然后，单击“添加后端”，添加之前创建的网站。
3. 选择“目标主机类型”  作为“应用服务”，选择在其中创建了网站的订阅，然后从“目标主机名”  中选择第一个网站，即 myAppServicePlanEastUS.azurewebsites.net  。
4. 将剩余字段保持原样，然后单击“添加”  。
5. 重复步骤 2 到 4 以添加其他网站，即myAppServicePlanWestEurope.azurewebsites.net 
6. 可以选择更新后端池的运行状况探测和负载均衡设置，但默认值应该也没问题。 单击“添加”  。


### <a name="c-add-a-routing-rule"></a>C. 添加路由规则
最后，单击路由规则上的“+”图标，配置传递规则。 需要进行此操作才可将前端主机映射到后端池，这本质上是在配置请求是否传入 `myappfrontend.azurefd.net`，然后将其转发到后端池`myBackendPool`。 单击“添加”  ，为 Front Door 添加传递规则。 现在应已准备好创建 Front Door，因此请单击“查看并创建”  。

>[!WARNING]
> 必须确保 Front Door 中的每个前端主机都具有传递规则，且其中包含与之关联的默认路径 ('/\*')  。 也就是说，在所有传递规则中，默认路径 ('/\*') 中定义的每个前端主机必须至少有一个路由规则。 如果不这样做，可能会导致最终用户流量无法正确路由。

## <a name="view-front-door-in-action"></a>通过实际操作了解 Front Door
创建 Front Door 后，需要几分钟时间在全局部署配置。 完成后，访问创建的前端主机，即，转到 Web 浏览器并点击 URL `myappfrontend.azurefd.net`。 你的请求将从后端池中的指定后端自动路由到离你最近的后端。 

### <a name="view-front-door-handle-application-failover"></a>了解 Front Door 如何处理应用程序故障转移
如果要在实际操作中测试 Front Door 的即时全局故障转移，可以转到你创建的一个网站并将其停止。 根据为后端池定义的运行状况探测设置，我们将立即将流量故障转移到其他网站部署。 此外，还可以通过对 Front Door 禁用后端池中的后端配置来测试行为。 

## <a name="clean-up-resources"></a>清理资源
若不再需要资源组、Web 应用程序以及所有相关资源，请将其删除。

## <a name="next-steps"></a>后续步骤
本快速入门介绍了如何创建 Front Door，以便定向用户流量，实现高度可用且性能最佳的 Web 应用程序。 要详细了解路由流量，请查看 Front Door 使用的[路由方法](front-door-routing-methods.md)。