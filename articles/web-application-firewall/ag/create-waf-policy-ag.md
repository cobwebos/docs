---
title: 为应用程序网关创建 Web 应用程序防火墙 （WAF） 策略
description: 了解如何为应用程序网关创建 Web 应用程序防火墙策略。
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 3e8cd2f1e594cd6a60296b2df135f275641df313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086984"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>为应用程序网关创建 Web 应用程序防火墙策略

将 WAF 策略与侦听器关联允许单个 WAF 后面的多个站点受到不同策略的保护。 例如，如果 WAF 后面有五个站点，则可以有五个单独的 WAF 策略（每个侦听器一个策略），以自定义一个站点的排除规则、自定义规则和托管规则集，而不会影响其他四个站点。 如果希望单个策略应用于所有站点，则只需将策略与应用程序网关（而不是单个侦听器）相关联，即可使其全局应用。 策略也可以应用于基于路径的路由规则。 

您可以根据需要制定尽可能多的策略。 创建策略后，它必须与应用程序网关关联才能生效，但它可以与应用程序网关和侦听器的任意组合相关联。 

如果应用程序网关应用了策略，然后对该应用程序网关上的侦听器应用了不同的策略，则侦听器的策略将生效，但仅针对分配给侦听器的侦听器。 应用程序网关策略仍适用于未为其分配特定策略的所有其他侦听器。 

   > [!NOTE]
   > 每个站点和每个 URI 的 WAF 策略均为公共预览版。 这意味着此功能受 Microsoft 补充使用条款的约束。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

所有新的 Web 应用程序防火墙的 WAF 设置（自定义规则、托管 rulset 配置、排除等）都位于 WAF 策略中。 如果您有现有的 WAF，这些设置可能仍然存在于 WAF 配置中。有关如何移动到新的 WAF 策略的步骤，请参阅本文后面的[WAF 配置迁移到 WAF 策略](#migrate)。 

## <a name="create-a-policy"></a>创建策略

首先，使用 Azure 门户使用托管默认规则集 （DRS） 创建基本 WAF 策略。

1. 在门户的左上角，选择 **"创建资源**"。 搜索**WAF**，选择**Web 应用程序防火墙**，然后选择 **"创建**"。
2. 在**创建 WAF 策略**页"**基础知识"** 选项卡上，输入或选择以下信息，接受其余设置的默认值，然后选择 **"查看 + 创建**：

   |设置  |“值”  |
   |---------|---------|
   |政策     |区域 WAF（应用程序网关）|
   |订阅     |选择订阅名称|
   |资源组     |选择你的资源组|
   |策略名称     |为 WAF 策略键入唯一名称。|
3. 在 **"关联"** 选项卡上，输入以下设置之一，然后选择 **"添加**：

   |设置  |“值”  |
   |---------|---------|
   |关联应用程序网关     |选择应用程序网关配置文件名称。|
   |助理监听器     |选择应用程序网关侦听器的名称，然后选择 **"添加**"。|

   > [!NOTE]
   > 如果将策略分配给已具有策略的应用程序网关（或侦听器），则原始策略将被新策略覆盖并替换。
4. 依次选择“查看 + 创建”、“创建”。********

   ![WAF 策略基础知识](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>配置 WAF 规则（可选）

创建 WAF 策略时，默认情况下处于*检测*模式。 在检测模式下，WAF 不会阻止任何请求。 相反，匹配的 WAF 规则将记录在 WAF 日志中。 要查看 WAF 在操作中，可以将模式设置更改为 *"预防*"。 在"预防"模式下，所选 CRS 规则集中定义的匹配规则将被阻止和/或记录在 WAF 日志中。

## <a name="managed-rules"></a>托管规则

默认情况下启用 Azure 管理的 OWASP 规则。 要禁用规则组中的单个规则，请展开该规则组中的规则，选择规则编号前面的复选框，然后在上面的选项卡上选择 **"禁用**"。

[![托管规则](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>自定义规则

要创建自定义规则，请在 **"自定义规则"** 选项卡下选择 **"添加自定义规则**"。这将打开自定义规则配置页。 以下屏幕截图显示了一个自定义规则示例，该规则配置为在查询字符串包含文本*块块*时阻止请求。

[![编辑自定义规则](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>将 WAF 配置迁移到 WAF 策略

如果您有现有的 WAF，您可能注意到门户中的一些更改。 首先，您需要确定在 WAF 上启用的策略类型。 有三种潜在状态：

- 无 WAF 策略
- 仅自定义规则策略
- WAF 策略

通过在门户中查看 WAF，可以判断您的 WAF 处于哪个状态。 如果 WAF 设置可见，并且可以从应用程序网关视图中更改，则 WAF 处于状态 1。

[![WAF](../media/create-waf-policy-ag/waf-configure.png)配置](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

如果选择**Web 应用程序防火墙**，并且它显示关联的策略，WAF 处于状态 2 或状态 3。 导航到策略后，如果**策略仅**显示自定义规则和关联应用程序网关，则它是仅自定义规则策略。

![WAF 自定义规则](../media/create-waf-policy-ag/waf-custom-rules.png)

如果它还显示策略设置和托管规则，则它是完整的 Web 应用程序防火墙策略。 

![WAF 策略设置](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>迁移到 WAF 策略

如果您只有自定义规则 WAF 策略，则可能需要迁移到新的 WAF 策略。 今后，防火墙策略将支持 WAF 策略设置、托管规则集、排除和禁用的规则组。 本质上，以前在应用程序网关内完成的所有 WAF 配置现在都通过 WAF 策略完成。 

仅禁用对自定义规则的编辑。 要编辑任何 WAF 设置（如禁用规则、添加排除项等），必须迁移到新的顶级防火墙策略资源。

为此，请创建*Web 应用程序防火墙策略*并将其关联到您选择的应用程序网关和侦听器。 此新策略**必须**与当前 WAF 配置完全相同，这意味着必须将每个自定义规则、排除规则、禁用规则等复制到要创建的新策略中。 获得与应用程序网关关联的策略后，可以继续更改 WAF 规则和设置。 还可以使用 Azure PowerShell 执行此操作。 有关详细信息，请参阅将[WAF 策略与现有应用程序网关相关联](associate-waf-policy-existing-gateway.md)。

或者，可以使用迁移脚本迁移到 WAF 策略。 有关详细信息，请参阅使用[Azure PowerShell 迁移 Web 应用程序防火墙策略](migrate-policy.md)。

## <a name="next-steps"></a>后续步骤

了解有关[Web 应用程序防火墙 CRS 规则组和规则](application-gateway-crs-rulegroups-rules.md)的更多内容。
