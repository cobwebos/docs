---
title: "使用 Web 应用程序防火墙创建或更新 Azure 应用程序网关 | Microsoft Docs"
description: "了解如何使用门户创建具有 Web 应用程序防火墙的应用程序网关"
services: application-gateway
documentationcenter: na
author: georgewallace
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
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 224aa0db2feb7a83bec5b4ec46140046d10f012e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/04/2017


---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>使用门户创建具有 Web 应用程序防火墙的应用程序网关

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-web-application-firewall-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-web-application-firewall-powershell.md)

了解如何创建启用了 Web 应用程序防火墙的应用程序网关。

Azure 应用程序网关中的 Web 应用程序防火墙 (WAF) 可保护 Web 应用程序，使其免受常见 Web 攻击的威胁，例如 SQL 注入、跨站点脚本攻击和会话劫持。 Web 应用程序可以防止 OWASP 十大常见 Web 漏洞中的大部分漏洞。

## <a name="scenarios"></a>方案

本文介绍两个方案：

在第一个方案中，了解如何[创建具有 Web 应用程序防火墙的应用程序网关](#create-an-application-gateway-with-web-application-firewall)

在第二个方案中，了解如何[将 Web 应用程序防火墙添加到现有的应用程序网关](#add-web-application-firewall-to-an-existing-application-gateway)。

![方案示例][scenario]

> [!NOTE]
> 针对应用程序网关进行的其他配置（包括自定义运行状况探测、后端池地址以及其他规则）是在对应用程序网关配置以后配置的，不是在初始部署期间配置的。

## <a name="before-you-begin"></a>开始之前

Azure 应用程序网关需要自己的子网。 在创建虚拟网络时，请确保保留足够的地址空间，以便设置多个子网。 将应用程序网关部署到子网后，只能向该子网添加其他应用程序网关。

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a> 将 Web 应用程序防火墙添加到现有的应用程序网关

此示例会更新现有的应用程序网关，以在阻止模式下支持 Web 应用程序防火墙。

1. 在 Azure 门户的“收藏夹”窗格中单击“所有资源”。 在“所有资源”边栏选项卡中单击现有应用程序网关。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入名称， 轻松访问 DNS 区域。

   ![创建应用程序网关][1]

1. 单击“Web 应用程序防火墙”并更新应用程序网关设置。 完成后，单击“保存”

    用于更新现有应用程序网关以支持 Web 应用程序防火墙的设置如下：

   | **设置** | **值** | **详细信息**
   |---|---|---|
   |**升级到 WAF 层**| 已选中 | 这将应用程序网关的层设置为 WAF 层。|
   |**防火墙状态**| Enabled | Enabled | 此设置在 WAF 上启用防火墙。|
   |**防火墙模式** | 预防 | 此设置是指 Web 应用程序防火墙处理恶意流量的方式。 **检测**模式只记录事件，而**阻止**模式则会记录事件并停止恶意流量。|
   |**规则集**|3.0|此设置确定用于保护后端池成员的[核心规则集](application-gateway-web-application-firewall-overview.md#core-rule-sets)。|
   |**配置已禁用的规则**|多种多样|为了防止可能误报，此设置允许你禁用某些[规则和规则组](application-gateway-crs-rulegroups-rules.md)。|

    >[!NOTE]
    > 将现有应用程序网关升级到 WAF SKU 时，SKU 大小将更改为“中型”。 配置完成后，可以重新配置此项。

    ![显示基本设置的边栏选项卡][2-1]

    > [!NOTE]
    > 若要查看 Web 应用程序防火墙日志，必须启用诊断功能并选择 ApplicationGatewayFirewallLog。 进行测试时，可以选择 1 作为实例计数。 必须知道的是，2 以下的实例计数不受 SLA 支持，因此不建议使用。 使用 Web 应用程序防火墙时，无法使用小型网关。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>创建具有 Web 应用程序防火墙的应用程序网关

此方案将：

* 创建包含两个实例的中型应用程序防火墙应用程序网关。
* 创建名为 AdatumAppGatewayVNET 且包含 10.0.0.0/16 保留 CIDR 块的虚拟网络。
* 创建名为 Appgatewaysubnet 且使用 10.0.0.0/28 作为其 CIDR 块的子网。
* 配置进行 SSL 卸载的证书。

1. 登录到 [Azure 门户](https://portal.azure.com)。 如果还没有帐户，可以注册[一个月免费试用版](https://azure.microsoft.com/free)
1. 在门户的“收藏夹”窗格中，单击“新建”
1. 在“新建”边栏选项卡中，单击“网络”。 在“网络”边栏选项卡中，单击“应用程序网关”，如下图所示：
1. 导航到 Azure 门户，依次单击“新建” > “网络” > “应用程序网关”

    ![创建应用程序网关][1]

1. 在显示的“基本信息”边栏选项卡中，输入以下值，然后单击“确定”：

   | **设置** | **值** | **详细信息**
   |---|---|---|
   |**Name**|AdatumAppGateway|应用程序网关的名称|
   |**层**|WAF|可用值为标准和 WAF。 若要了解有关 WAF 的详细信息，请访问 [Web 应用程序防火墙](application-gateway-web-application-firewall-overview.md)。|
   |**SKU 大小**|中型|选择标准层时，选项为小型、中型和大型。 选择 WAF 层时，选项只有中型和大型。|
   |**实例计数**|2|实现高可用性时应用程序网关的实例数。 实例计数 1 仅应用于测试目的。|
   |**订阅**|[你的订阅]|选择要在其中创建应用程序网关的订阅。|
   |**资源组**|**新建：**AdatumAppGatewayRG|创建资源组。 资源组名称必须在所选订阅中唯一。 若要详细了解资源组，请阅读 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) 这篇概述文章。|
   |**位置**|美国西部||

   ![显示基本设置的边栏选项卡][2-2]

1. 在“虚拟网络”下的“设置”边栏选项卡中，单击“选择虚拟网络”。 这将打开“选择虚拟网络”边栏选项卡。  单击“新建”打开“创建虚拟网络”边栏选项卡。

   ![选择虚拟网络][2]

1. 在“创建虚拟网络”边栏选项卡中，输入以下值，然后单击“确定”。 这将关闭“创建虚拟网络”和“选择虚拟网络”边栏选项卡。 这也将用所选的子网填充“设置”边栏选项卡上的“子网”字段。

   |**设置** | **值** | **详细信息** |
   |---|---|---|
   |**Name**|AdatumAppGatewayVNET|应用程序网关的名称|
   |**地址空间**|10.0.0.0/16| 这是用于虚拟网络的地址空间|
   |**子网名称**|AppGatewaySubnet|应用程序网关子网的名称|
   |**子网地址范围**|10.0.0.0/28| 此子网允许后端池成员的虚拟网络中存在多个附加子网|

1. 在“设置”边栏选项卡的“前端 IP 配置”下选择“公共”作为“IP 地址类型”

1. 在“设置”边栏选项卡的“公共 IP 地址”下，单击“选择一个公共 IP 地址”，这将打开“选择公共 IP 地址”边栏选项卡，然后单击“新建”。

   ![选择公共 IP][3]

1. 在“创建公共 IP 地址”边栏选项卡中，接受默认值，然后单击“确定”。 这将关闭“选择公共 IP 地址”边栏选项卡和“创建公共 IP 地址”边栏选项卡，并用所选的公共 IP 地址填充“公共 IP 地址”。

1. 在“设置”边栏选项卡的“侦听器配置”下，在“协议”下方单击“HTTP”。 若要使用“https”，需要提供证书。 需要提供证书的私钥，这样就需要提供证书的 .pfx 导出结果以及文件的密码。

1. 配置 **WAF** 特定设置。

   |**设置** | **值** | **详细信息** |
   |---|---|---|
   |**防火墙状态**| Enabled| 此设置可打开或关闭 WAF。|
   |**防火墙模式** | 预防| 此设置确定 WAF 对恶意流量采取的操作。 如果选择**检测**，只会记录流量。  如果选择“阻止”，会记录并停止流量，同时返回“403 未授权”响应。|


1. 查看“摘要”页，然后单击“确定”。  现在会让应用程序网关排队，然后创建它。

1. 创建应用程序网关以后，可在门户中导航到该网关，然后继续进行配置。

    ![应用程序网关资源视图][10]

这些步骤会创建基本的应用程序网关，提供侦听器、后端池、后端 http 设置以及规则的默认设置。 预配成功后，即可根据部署修改这些设置

> [!NOTE]
> 为使用基本 Web 应用程序防火墙配置创建的应用程序网关配置 CRS 3.0 以进行保护。

## <a name="next-steps"></a>后续步骤

若要了解如何配置诊断日志记录，以及如何记录通过 Web 应用程序防火墙检测到或阻止的事件，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)

访问[创建自定义运行状况探测](application-gateway-create-probe-portal.md)，了解如何创建自定义运行状况探测

访问[配置 SSL 卸载](application-gateway-ssl-portal.md)，了解如何配置 SSL 卸载并从 Web 服务器中剥离开销较高的 SSL 解密

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

