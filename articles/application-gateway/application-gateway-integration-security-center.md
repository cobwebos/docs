---
title: "应用程序网关集成与 Azure 安全中心 | Microsoft Docs"
description: "本页介绍如何将应用程序网关集成到 Azure 安全中心。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 737cdff3140be68cf9d6d396b470dd09c65c52f2
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017

---

# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>应用程序网关和 Azure 安全中心之间的集成概述

了解应用程序网关和安全中心如何帮助保护 Web 应用程序资源。 应用程序网关 Web 应用程序防火墙 (WAF) 与[安全中心](../security-center/security-center-intro.md)进行集成，实现不间断查看，以阻止、检测和响应你的环境中对未受保护的 Web 应用程序的威胁。

## <a name="overview"></a>概述

建议在安全中心采用应用程序网关 WAF，以便保护 Web 应用程序免受入侵和漏洞威胁。 在安全中心，不受 WAF 保护的启用了 Web 的资源显示为高严重性建议。 有关针对 Web 应用程序防火墙的建议，可在“概述”页面的“应用程序”下查看。

![与安全中心集成][1]

单击与 Web 应用程序防火墙相关的任何建议后，都将打开一个新的边栏选项卡，显示建议的详细信息。

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>向现有资源添加 Web 应用程序防火墙

导航到“更多服务” > “安全 + 标识” > “安全中心”，然后在“安全中心 - 概述”边栏选项卡上，单击“应用程序”。 在“安全 + 标识 - 应用程序”边栏选项卡上，表格包含安全中心在订阅中检测到的应用程序的列表。

![Web 应用程序][3]

单击存在严重问题的 Web 应用程序，随即出现“应用程序安全运行状况”边栏选项卡。 在下图中，就是不受 Web 应用程序防火墙保护的 Web 应用程序 

![不受保护的 Web 资源][2]

在“建议”下，单击“添加 Web 应用程序防火墙”打开“添加 Web 应用程序防火墙”边栏选项卡。

如果目前没有应用程序网关，或者想要新建一个，请单击“新建”，在然后“新建 Web 应用程序防火墙”边栏选项卡上，单击“Microsoft - 应用程序网关”。 这将引导你完成创建应用程序网关的步骤。 此时，Web 应用程序已作为受保护资源添加，安全中心现跟踪到该资源受到 Web 应用程序防火墙的保护。 该程序不会添加为后端池成员。

如果拥有现有应用程序网关，可在“使用现有解决方案”下将其选中。

![“添加 Web 应用程序防火墙”边栏选项卡][4]

如果通过安全中心将 Web 应用程序添加到应用程序网关，资源不会添加为后端池成员，此步骤必须在应用程序网关资源上直接完成。

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>将资源添加到现有 Web 应用程序防火墙。

导航到“更多服务” > “安全 + 标识” > “安全中心”，然后在“安全中心 - 概述”边栏选项卡上，单击“合作伙伴解决方案”。 在“合作伙伴解决方案”边栏选项卡下将显示现有安全中心感知应用程序网关。

![合作伙伴解决方案][7]

单击“链接应用”打开“链接应用程序”边栏选项卡，可在此处选择现有应用程序。 选择要保护的应用程序，然后单击“确定”。 这不会向应用程序网关的后端池添加 Web 应用程序。 而是将资源设置为受保护的资源，以便安全中心跟踪。 若要将资源添加为后端池成员，必须在应用程序网关上操作，可在当前边栏选项卡上单击“解决方案控制台”，转到可向后端池添加 Web 应用程序的应用程序网关资源。

![合作伙伴解决方案应用程序][6]

## <a name="finalize-configuration"></a>完成配置

安全中心会跟踪作为受保护资源添加到应用程序网关的应用程序。  它会监视此资源的运行状况，确保其受到应用程序网关保护。 接下来是向应用程序网关的后端池添加专用 IP、公共 IP或 拟机的 NIC。 完成之前，将显示一条“完成应用程序保护”的额外建议，直到资源添加完毕。

![“添加 Web 应用程序防火墙”边栏选项卡][5]

## <a name="security-alerts"></a>安全警报

在安全中心导航到“检测” > “安全警报”。  此处显示应用程序网关的 WAF 警报。 警报按 WAF 规则进行划分。

![安全警报][8]

单击规则将显示该特定 WAF 规则的警报列表。 每个警报均显示有关查找结果的额外详细信息。 详细信息中随附一个应用程序网关链接。
 
![警报详细信息][9]

## <a name="next-steps"></a>后续步骤

若要了解如何在现有应用程序网关上启用 Web 应用程序防火墙，请参阅[通过 Web 应用程序防火墙创建或更新 Azure 应用程序网关](application-gateway-web-application-firewall-portal.md#add-web-application-firewall-to-an-existing-application-gateway)

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png
