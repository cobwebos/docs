---
title: 应用程序网关高流量支持
description: 本文提供了配置 Azure 应用程序网关以支持高网络流量方案的指导。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: e3f8445f68ec959ce1bb0d1ba4029807bd25907a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257421"
---
# <a name="application-gateway-high-traffic-support"></a>应用程序网关高流量支持 

本文介绍了一些建议的指南，以帮助您设置应用程序网关来处理由于高流量方案（如 COVID-19）而导致的额外流量。 您可以将应用程序网关与 Web 应用程序防火墙 （WAF） 一起用于可扩展和安全的方式来管理 Web 应用程序的流量。 

以下建议可帮助您使用 WAF 设置应用程序网关以处理额外的流量。 

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>使用 v2 SKU 超过 v1 来增强其自动缩放功能和性能优势
v2 SKU 提供自动缩放，以确保应用程序网关可以随着流量的增加而扩展。 它还提供其他显著的性能优势，例如与 v1 相比，TLS 卸载性能提高 5 倍、部署和更新时间更快、区域冗余等。 有关详细信息，请参阅我们的[v2 文档](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)。 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>将最大实例计数设置为最大可能 （125）
 
假设您有应用程序网关 v2 SKU，将最大实例计数设置为最大可能值 125，使应用程序网关可以根据需要横向扩展。 这允许它处理应用程序可能的流量增加。 您只能为使用的容量单位 （C） 收费。  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>根据您的平均 CU 使用情况设置最小实例计数

假设您有应用程序网关 v2 SKU，自动缩放需要 6 到 7 分钟才能横向扩展。使用较高的最小实例计数时，应用程序网关可以更好地在负载增加时处理流量，因为流量峰值不需要自动缩放操作。  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>如果某个指标超过平均 CU 利用率的 75%，则发出警报 
有关指标和其他演练的详细说明，请参阅[应用程序网关指标文档](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization)。 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>示例：在平均 CU 使用率的 75% 上设置警报

此示例演示如何在达到平均 CU 使用量的 75% 时使用 Azure 门户设置警报。 
1. 导航到**应用程序网关**。
2. 在左侧面板上，在"**监视"** 选项卡下选择 **"指标**"。 
3. 添加**平均当前计算单位的**指标。 
![设置 WAF 指标](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 如果将最小实例计数设置为平均 CU 使用，请继续并在使用最小实例的 75% 时设置警报。 例如，如果您的平均使用量为 10 个 C，请设置 7.5 个库的警报。 如果使用量增加，这将提醒您，并为您提供响应时间。 如果您认为此流量将持续，以提醒您流量可能会增加，则可以提高最低流量。 
![设置 WAF 警报](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 您可以将警报设置为以较低的 CU 利用率百分比或更高的 CU 利用率百分比发生，具体取决于您希望对潜在流量峰值的敏感程度。

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>使用地理筛选和自动程序保护设置 WAF 以停止攻击
如果要在应用程序前面增加一层安全性，请使用应用程序网关WAF_v2 SKU 进行 WAF 功能。 您可以将 v2 SKU 配置为仅允许从给定国家/地区访问您的应用程序。 设置 WAF 自定义规则以根据地理位置显式允许或阻止流量。 有关详细信息，请参阅[地理筛选自定义规则](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules)[以及如何通过 PowerShell 在应用程序网关WAF_v2 SKU 上配置自定义规则](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)。

启用自动程序保护以阻止已知的坏机器人。 这将减少访问应用程序的流量。 有关详细信息，请参阅[使用设置说明的机器人保护](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>打开应用程序网关和 WAF 上的诊断

诊断日志允许您查看防火墙日志、性能日志和访问日志。 您可以使用 Azure 中的这些日志来管理应用程序网关并排除应用程序网关的故障。 有关详细信息，请参阅我们的[诊断文档](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>设置 TLS 策略以提供额外的安全性
确保您使用的是最新的 TLS 策略版本[（AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)）。 这将强制执行 TLS 1.2 和更强的密码。 有关详细信息，请参阅通过[PowerShell 配置 TLS 策略版本和密码套件](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)。
