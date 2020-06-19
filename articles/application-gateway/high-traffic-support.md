---
title: 应用程序网关高流量支持
description: 本文提供了有关如何配置 Azure 应用程序网关以支持高网络流量方案的指导原则。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 56622ee2c014bd8dbca7c61ec00b927c56f63a40
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83740393"
---
# <a name="application-gateway-high-traffic-support"></a>应用程序网关高流量支持

>[!NOTE]
> 本文介绍了几条建议的指导原则，可帮助你设置应用程序网关，以应对因新冠肺炎危机而可能导致的高流量的额外流量。

可以使用配置了 Web 应用程序防火墙 (WAF) 的应用程序网关，以可缩放且安全的方式管理流向 Web 应用程序的流量。

以下建议可帮助你设置部署有 WAF 的应用程序网关来应对额外流量。

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>请使用 v2 SKU 而不是 v1，因为前者具有自动缩放功能，且性能更有优势
v2 SKU 提供自动缩放功能，确保应用程序网关能够随着流量的增加而纵向扩展。 与 v1 相比，它还提供其他重要性能优势，例如，TLS 卸载性能要高出 5 倍、部署和更新时间更快、支持区域冗余等。 有关详细信息，请参阅我们的 [v2 文档](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)。 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>将最大实例计数设置为最大可能值 (125)
 
假设你有应用程序网关 v2 SKU，将最大实例计数设置为最大可能值 125 可使应用程序网关按需横向扩展。 这样，应用程序网关就能处理应用程序中可能出现的流量增大情形。 你只需为使用的容量单位 (CU) 付费。  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>根据平均 CU 用量设置最小实例计数

假设你有应用程序网关 v2 SKU，自动缩放需要花费 6 到 7 分钟才能完成横向扩展。如果使用较大的最小实例计数，应用程序网关可以在负载增大时更好地处理流量，因为出现流量高峰时不需要执行自动缩放操作。  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>当特定指标超过平均 CU 用量的 75% 时发出警报 
有关指标的详细说明和其他演练，请参阅[应用程序网关指标文档](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization)。 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>示例：设置在达到平均 CU 用量的 75% 时发出警报

此示例演示如何使用 Azure 门户设置在达到平均 CU 用量的 75% 时发出警报。 
1. 导航到应用程序网关。
2. 在左侧面板中，选择“监视”选项卡下的“指标” 。 
3. 为“平均当前计算单位数”添加一个指标。 
![设置 WAF 指标](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 如果已将最小实例计数设置为平均 CU 用量，请继续设置在使用了最小实例数的 75% 时发出警报。 例如，如果平均用量为 10 个 CU，则设置在使用了 7.5 个 CU 时发出警报。 这会在用量不断增大时发出警报，并让你从容应对。 如果你认为这种流量将会持续，可以提高最小值，以提醒自己该流量可能会不断增大。 
![设置 WAF 警报](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 你可以根据自己对潜在流量高峰的敏感程度，设置在 CU 用量百分比降低或提高时发出警报。

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>为 WAF 设置地理筛选和机器人防护来阻止攻击
如果需要在应用程序的前面使用额外的安全层，请为 WAF 功能使用应用程序网关 WAF_v2 SKU。 可将 v2 SKU 配置为仅允许从给定的国家/地区访问你的应用程序。 设置一个 WAF 自定义规则，使其基于地理位置明确允许或阻止流量。 有关详细信息，请参阅[地理位置筛选自定义规则](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules)和[如何通过 PowerShell 在应用程序网 WAF_v2 中配置自定义规则](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)。

启用机器人防护以阻止已知恶意的机器人。 这应该可以减少进入应用程序的流量。 有关详细信息，请参阅[机器人防护和设置说明](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>在应用程序网关和 WAF 上启用诊断

通过诊断日志，你可以查看防火墙日志、性能日志和访问日志。 可在 Azure 中使用这些日志来对应用程序网关进行管理和故障排除。 有关详细信息，请参阅我们的[诊断文档](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>设置 TLS 策略以进一步提高安全性
请确保使用最新的 TLS 策略版本 ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s))。 此版本强制实施 TLS 1.2 和更强的密码。 有关详细信息，请参阅[通过 PowerShell 配置 TLS 策略版本和加密套件](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)。
