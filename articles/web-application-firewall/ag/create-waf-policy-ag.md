---
title: 创建应用程序网关的 Web 应用程序防火墙（WAF）策略
description: 了解如何创建应用程序网关的 Web 应用程序防火墙策略。
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 3f7d213aed82d1cb94bb96b9e212d3b255851afd
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171223"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>创建应用程序网关的 Web 应用程序防火墙策略

将 WAF 策略与侦听器相关联，可允许单个 WAF 后面的多个站点受到不同策略的保护。 例如，如果 WAF 后面有5个站点，则可以使用五个单独的 WAF 策略（每个侦听器一个）为一个站点自定义排除项、自定义规则和托管规则集，而不会影响其他四个策略。 如果希望将单个策略应用到所有站点，只需将该策略与应用程序网关相关联，而不是将它应用于单个侦听器，使其全局应用。 策略还可以应用于基于路径的路由规则。 

   > [!NOTE]
   > 每站点 WAF 策略在美国中南部和北欧提供。 若要在门户中访问它们，请使用[此链接](https://aka.ms/AppgwwafWithAllFeatureFlags)，直到所有人都可以使用。  

你可以根据需要创建任意多个策略。 创建策略后，必须将它关联到应用程序网关才能生效，但它可以与应用程序网关和侦听器的任何组合关联。 

如果应用程序网关应用了策略，然后将不同的策略应用于该应用程序网关上的侦听器，则该侦听器的策略将生效，但只适用于它们分配到的侦听器。 应用程序网关策略仍适用于未分配特定策略的所有其他侦听器。 

   > [!NOTE]
   > 每个站点和每个 URI 的 WAF 策略均为公共预览版。 这意味着此功能受 Microsoft 补充使用条款的约束。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

所有新的 Web 应用程序防火墙的 WAF 设置（自定义规则、托管 rulset 配置、排除项等）都在 WAF 策略中实时运行。 如果你有现有的 WAF，则这些设置可能仍存在于你的 WAF 配置中。有关如何移动到新 WAF 策略的步骤，请参阅本文后面的将[WAF Config 迁移到 WAF 策略](#migrate)。 

## <a name="create-a-policy"></a>创建策略

首先，使用 Azure 门户创建具有托管默认规则集（DRS）的基本 WAF 策略。

1. 在门户的左上方，选择 "**创建资源**"。 搜索 " **WAF**"，选择 " **Web 应用程序防火墙**"，然后选择 "**创建**"。
2. 在 "**创建 WAF 策略**" 页上的 "**基本**信息" 选项卡上，输入或选择以下信息，接受其余设置的默认值，然后选择 "**查看 + 创建**"：

   |设置  |值  |
   |---------|---------|
   |策略     |区域 WAF （应用程序网关）|
   |订阅     |选择订阅名称|
   |资源组     |选择你的资源组|
   |策略名称     |键入 WAF 策略的唯一名称。|
3. 在 "**关联**" 选项卡上，输入以下设置之一，然后选择 "**添加**"：

   |设置  |值  |
   |---------|---------|
   |关联应用程序网关     |选择应用程序网关配置文件名称。|
   |关联侦听器     |选择应用程序网关侦听器的名称，然后选择 "**添加**"。|

   > [!NOTE]
   > 如果已将策略分配到已有策略的应用程序网关（或侦听器），则会覆盖原始策略，并将其替换为新策略。
4. 依次选择“查看 + 创建”、“创建”。

   ![WAF 策略基础知识](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>配置 WAF 规则（可选）

创建 WAF 策略时，默认情况下它处于*检测*模式。 在检测模式下，WAF 不会阻止任何请求。 相反，匹配 WAF 规则记录在 WAF 日志中。 若要查看操作中的 WAF，可以将模式设置更改为 "*阻止*"。 在阻止模式下，在所选 CRS 规则集中定义的匹配规则将被阻止和/或记录在 WAF 日志中。

## <a name="managed-rules"></a>托管规则

默认情况下启用 Azure 托管的 OWASP 规则。 若要禁用规则组中的单个规则，请展开该规则组中的规则，选中规则编号前面的复选框，并在上面的选项卡上选择 "**禁用**"。

[![托管规则](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>自定义规则

若要创建自定义规则，请在 "**自定义规则**" 选项卡下选择 "**添加自定义规则**"。这将打开 "自定义规则配置" 页。 以下屏幕截图显示了一个示例自定义规则，该规则配置为在查询字符串包含文本*blockme*时阻止请求。

[![编辑自定义规则](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate"></a>将 WAF 配置迁移到 WAF 策略

如果有现成的 WAF，可能会注意到门户中的一些更改。 首先需要确定在 WAF 上启用的策略类型。 有三种可能的状态：

- 无 WAF 策略
- 仅自定义规则策略
- WAF 策略

可以通过在门户中查看 WAF，来确定你的的状态。 如果 WAF 设置可见，并且可在应用程序网关视图中更改，则 WAF 处于状态1。

[![WAF 配置](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

如果选择 " **Web 应用程序防火墙**" 并显示关联的策略，则 WAF 的状态为 "2" 或 "状态 3"。 导航到策略后，如果它**仅**显示自定义规则和关联的应用程序网关，则它是自定义规则 "仅限" 策略。

![WAF 自定义规则](../media/create-waf-policy-ag/waf-custom-rules.png)

如果它还显示策略设置和托管规则，则它是完整的 Web 应用程序防火墙策略。 

![WAF 策略设置](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>迁移到 WAF 策略

如果仅有自定义规则 WAF 策略，则可能需要移动到新的 WAF 策略。 今后，防火墙策略将支持 WAF 策略设置、托管规则集、排除项和禁用的规则组。 实质上，以前在应用程序网关内完成的所有 WAF 配置现在都是通过 WAF 策略完成的。 

仅禁用对自定义规则的编辑 WAF 策略。 若要编辑任何 WAF 设置（例如禁用规则、添加排除项等），必须迁移到新的顶级防火墙策略资源。

为此，请创建一个*Web 应用程序防火墙策略*，并将其关联到所选的应用程序网关和侦听器。 此新策略**必须**与当前 WAF config 完全相同，这意味着每个自定义规则、排除、禁用的规则等都必须复制到您要创建的新策略中。 一旦你有与应用程序网关关联的策略，你就可以继续更改 WAF 规则和设置。 还可以通过 Azure PowerShell 执行此操作。 有关详细信息，请参阅[将 WAF 策略与现有的应用程序网关相关联](associate-waf-policy-existing-gateway.md)。

或者，你可以使用迁移脚本迁移到 WAF 策略。 有关详细信息，请参阅[使用 Azure PowerShell 迁移 Web 应用程序防火墙策略](migrate-policy.md)。

## <a name="next-steps"></a>后续步骤

了解有关[Web 应用程序防火墙 CRS 规则组和规则](application-gateway-crs-rulegroups-rules.md)的详细信息。
