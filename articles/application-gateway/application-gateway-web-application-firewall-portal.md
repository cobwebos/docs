---
title: "使用 Web 应用程序防火墙创建或更新应用程序网关 | Microsoft 文档"
description: "了解如何使用门户创建具有 Web 应用程序防火墙的应用程序网关"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>使用门户创建具有 Web 应用程序防火墙的应用程序网关

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

了解如何创建启用 Web 应用程序防火墙 (WAF) 的应用程序网关。

Azure 应用程序网关中的 WAF 可保护 Web 应用程序，使其免受常见的基于 Web 的攻击威胁，例如 SQL 注入、跨站点脚本攻击和会话劫持。 WAF 可以防止 OWASP 十大常见 Web 漏洞中的大部分漏洞。

## <a name="scenarios"></a>方案

本文提供了两种方案。 在第一个方案中，了解如何[创建具有 WAF 的应用程序网关](#create-an-application-gateway-with-web-application-firewall)。 在第二个方案中，了解如何[将 WAF 添加到现有的应用程序网关](#add-web-application-firewall-to-an-existing-application-gateway)。

![方案示例][scenario]

> [!NOTE]
> 你可以将自定义运行状况探测、后端池地址和其他规则添加到应用程序网关。 配置应用程序网关后（而不是在初始部署期间），配置这些应用程序。

## <a name="before-you-begin"></a>开始之前

 应用程序网关需要自己的子网。 在创建虚拟网络时，请确保保留足够的地址空间，以便设置多个子网。 将应用程序网关部署到子网后，只能向该子网添加其他应用程序网关。

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>将 Web 应用程序防火墙添加到现有的应用程序网关

此示例会更新现有的应用程序网关，以在“阻止”模式下支持 WAF。

1. 在 Azure 门户的“收藏夹”窗格中，单击“所有资源”。 在“所有资源”边栏选项卡上，选择现有应用程序网关。 如果所选订阅中已包含多个资源，可在“按名称筛选”框中输入名称，以便轻松访问 DNS 区域。

   ![现有应用程序网关选择][1]

2. 选择“Web 应用程序防火墙”，并更新应用程序网关设置。 当更新完成时，选择“保存”。 

3. 用于更新现有应用程序网关以支持 WAF 的设置如下：

   | **设置** | **值** | **详细信息**
   |---|---|---|
   |**升级到 WAF 层**| 已选中 | 此选项将应用程序网关的层设置为 WAF 层。|
   |**防火墙状态**| Enabled | 此设置在 WAF 上启用防火墙。|
   |**防火墙模式** | 预防 | 此设置是指 WAF 处理恶意流量的方式。 “检测”模式只记录事件。 “预防”模式记录事件，并遏止恶意流量。|
   |**规则集**|3.0|此设置确定用于保护后端池成员的[核心规则集](application-gateway-web-application-firewall-overview.md#core-rule-sets)。|
   |**配置已禁用的规则**|多种多样|为了避免可能的误报，此设置可用于禁用某些[规则和规则组](application-gateway-crs-rulegroups-rules.md)。|

    >[!NOTE]
    > 将现有应用程序网关升级到 WAF SKU 时，SKU 大小将更改为“中型”。 配置完成后，可以重新配置此设置。

    ![基本设置][2-1]

    > [!NOTE]
    > 若要查看 WAF 日志，请启用诊断，并选择“ApplicationGatewayFirewallLog”。 仅出于测试目的，可以选择 1 作为实例计数。 不建议实例计数小于 2，因为它未被 SLA 涵盖。 在使用 WAF 时，小型网关不可用。

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>创建具有 Web 应用程序防火墙的应用程序网关

此方案将：

* 创建包含两个实例的中型 WAF 应用程序网关。
* 创建名为 AdatumAppGatewayVNET 且包含 10.0.0.0/16 保留 CIDR 块的虚拟网络。
* 创建名为 Appgatewaysubnet 且使用 10.0.0.0/28 作为其 CIDR 块的子网。
* 配置进行 SSL 卸载的证书。

1. 登录到 [Azure 门户](https://portal.azure.com)。 如果还没有帐户，可以注册[免费一个月试用版](https://azure.microsoft.com/free)。

2. 在门户的“收藏夹”窗格中，选择“新建”。

3. 在“新建”边栏选项卡上，选择“网络”。 在“网络”边栏选项卡上，选择“应用程序网关”，如下图所示：

    ![应用程序网关创建][1]

4. 在显示的“基本信息”边栏选项卡上，输入以下值，然后选择“确定”：

   | **设置** | **值** | **详细信息**
   |---|---|---|
   |**Name**|AdatumAppGateway|应用程序网关的名称。|
   |**层**|WAF|可用值为标准和 WAF。 若要了解有关 WAF 的详细信息，请参阅 [Web 应用程序防火墙](application-gateway-web-application-firewall-overview.md)。|
   |**SKU 大小**|中型|标准层选项包括“小型”、“中型”，和“大型”。 WAF 层选项只有“中型”和“大型”。|
   |**实例计数**|#N/A|实现高可用性时应用程序网关的实例数。 仅出于测试目的，可使用 1 作为实例计数。|
   |**订阅**|[订阅]|选择用于创建应用程序网关的订阅。|
   |**资源组**|**新建：**AdatumAppGatewayRG|创建资源组。 资源组名称必须在所选订阅中唯一。 若要详细了解资源组，请阅读 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) 这篇概述文章。|
   |**位置**|美国西部||

   ![基本设置配置][2-2]

5. 在“虚拟网络”下的“设置”边栏选项卡上，选择“选择虚拟网络”。 在“选择虚拟网络”边栏选项卡上，选择“新建”。

   ![虚拟网络选择][2]

6. 在“创建虚拟网络”边栏选项卡上，输入以下值，然后选择“确定”。 “设置”边栏选项卡上的“子网”字段将使用所选子网进行填充。

   |**设置** | **值** | **详细信息** |
   |---|---|---|
   |**Name**|AdatumAppGatewayVNET|应用程序网关的名称。|
   |**地址空间**|10.0.0.0/16| 此值是用于虚拟网络的地址空间。|
   |**子网名称**|AppGatewaySubnet|应用程序网关子网的名称。|
   |**子网地址范围**|10.0.0.0/28 | 此子网允许后端池成员的虚拟网络中存在多个附加子网。|

7. 在“设置”边栏选项卡的“前端 IP 配置”下，选择“公共”作为“IP 地址类型”。

8. 在“设置”边栏选项卡的“公共 IP 地址”下，选择“选择公共 IP 地址”。 在“选择公共 IP 地址”边栏选项卡上，选择“新建”。

   ![公共 IP 地址选择][3]

9. 在“创建公共 IP 地址”边栏选项卡上，接受默认值，并选择“确定”。 “公共 IP 地址”字段将使用所选的公共 IP 地址进行填充。

10. 在“设置”边栏选项卡的“侦听器配置”下，选择“协议”下的“HTTP”。 若要使用“HTTPS”，需要提供证书。 需要证书的私钥。 提供证书的 .pfx 导出，并输入文件的密码。

11. 配置 WAF 的特定设置。

   |**设置** | **值** | **详细信息** |
   |---|---|---|
   |**防火墙状态**| Enabled| 此设置可打开或关闭 WAF。|
   |**防火墙模式** | 预防| 此设置确定 WAF 对恶意流量采取的操作。 “检测”模式只记录流量。 “预防”模式记录并遏止具有“403 未经授权”响应的流量。|


12. 查看“摘要”页，并选择“确定”。 现在会让应用程序网关排队，并创建它。

13. 创建应用程序网关后，可在门户中访问它，以继续配置应用程序网关。

    ![应用程序网关资源视图][10]

这些步骤会使用侦听器、后端池、后端 HTTP 设置以及规则的默认设置创建基本的应用程序网关。 预配成功完成后，可根据部署修改这些设置。

> [!NOTE]
> 为使用基本 WAF 配置创建的应用程序网关配置 CRS 3.0 以进行保护。

## <a name="next-steps"></a>后续步骤

若要配置[公共 IP 地址](../dns/dns-custom-domain.md#public-ip-address)的自定义域别名，可以使用 Azure DNS 或另一个 DNS 提供程序。

若要配置诊断日志记录，以记录通过 WAF 检测到或阻止的事件，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

若要创建自定义运行状况探测，请参阅[创建自定义运行状况探测](application-gateway-create-probe-portal.md)。

若要配置 SSL 卸载并从 Web 服务器中剥离开销较高的 SSL 订阅，请参阅[配置 SSL 卸载](application-gateway-ssl-portal.md)。

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
