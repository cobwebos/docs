---
title: "创建应用程序网关 - Azure 门户 | Microsoft Docs"
description: "了解如何使用门户创建应用程序网关"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: d3c39cfe3159cd4059a81f966fb551175188278b
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="create-an-application-gateway-with-the-portal"></a>使用门户创建应用程序网关

[应用程序网关](application-gateway-introduction.md)是一个专用的虚拟设备，以服务形式提供应用程序传送控制器 (ADC)，为应用程序提供各种第 7 层负载均衡功能。 本文分步介绍如何使用 Azure 门户创建应用程序网关，并将现有服务器添加为后端成员。

## <a name="log-in-to-azure"></a>登录 Azure

通过 [http://portal.azure.com](http://portal.azure.com) 登录到 Azure 门户

## <a name="create-application-gateway"></a>创建应用程序网关

若要创建应用程序网关，请完成下面的步骤。 应用程序网关需要自己的子网。 在创建虚拟网络时，请确保保留足够的地址空间，以便设置多个子网。 应用程序网关部署到子网后，只能向其添加其他应用程序网关。

1. 在门户的“收藏夹”窗格中，单击“新建”
1. 在“新建”边栏选项卡中，单击“网络”。 在“网络”边栏选项卡中，单击“应用程序网关”，如下图所示：

    ![创建应用程序网关][1]

1. 在显示的“基本信息”边栏选项卡中，输入以下值，然后单击“确定”：

   | **设置** | **值** | **详细信息**|
   |---|---|---|
   |**Name**|AdatumAppGateway|应用程序网关的名称|
   |层|标准|可用值为标准和 WAF。 若要了解有关 WAF 的详细信息，请访问 [Web 应用程序防火墙](application-gateway-web-application-firewall-overview.md)。|
   |**SKU 大小**|中型|选择标准层时，选项为小型、中型和大型。 选择 WAF 层时，选项只有中型和大型。|
   |**实例计数**|2|实现高可用性时应用程序网关的实例数。 实例计数 1 仅应用于测试目的。|
   |**订阅**|[订阅]|选择要在其中创建应用程序网关的订阅。|
   |**资源组**|**新建：**AdatumAppGatewayRG|创建资源组。 资源组名称必须在所选订阅中唯一。 若要详细了解资源组，请阅读 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) 这篇概述文章。|
   |**位置**|美国西部||

1. 在“虚拟网络”下的“设置”边栏选项卡中，单击“选择虚拟网络”。 这会打开“选择虚拟网络”边栏选项卡。  单击“新建”打开“创建虚拟网络”边栏选项卡。

   ![选择虚拟网络][2]

1. 在“创建虚拟网络”边栏选项卡中，输入以下值，并单击“确定”。 这会关闭“创建虚拟网络”和“选择虚拟网络”边栏选项卡。 此步骤使用所选子网填充“设置”边栏选项卡上的“子网”字段。

   | **设置** | **值** | **详细信息**|
   |---|---|---|
   |**Name**|AdatumAppGatewayVNET|应用程序网关的名称|
   |**地址空间**|10.0.0.0/16|这是用于虚拟网络的地址空间|
   |**子网名称**|AppGatewaySubnet|应用程序网关子网的名称|
   |**子网地址范围**|10.0.0.0/28|此子网允许后端池成员的虚拟网络中存在多个附加子网|

1. 在“设置”边栏选项卡的“前端 IP 配置”下选择“公共”作为“IP 地址类型”

1. 在“设置”边栏选项卡的“公共 IP 地址”下，单击“选择一个公共 IP 地址”，这将打开“选择公共 IP 地址”边栏选项卡，然后单击“新建”。

   ![选择公共 IP][3]

1. 在“创建公共 IP 地址”边栏选项卡中，接受默认值，并单击“确定”。 这会关闭边栏选项卡并使用所选公共 IP 地址填充“公共 IP 地址”。

1. 在“设置”边栏选项卡的“侦听器配置”下，单击“协议”下方的“HTTP”。 在“端口”字段中输入要使用的端口。

2. 在“设置”边栏选项卡上单击“确定”以继续。

1. 查看“摘要”边栏选项卡上的设置，并单击“确定”开始创建应用程序网关。 创建应用程序网关是一个长时间运行的任务，需要一些时间来完成。

## <a name="add-servers-to-backend-pools"></a>将服务器添加到后端池

创建应用程序网关后，仍需将系统（托管着要进行负载均衡的应用程序）添加到应用程序网关。 这些服务器的 IP 地址、FQDN 或 NIC 已添加到后端地址池。

### <a name="ip-address-or-fqdn"></a>IP 地址或 FQDN

1. 创建应用程序网关后，在 Azure 门户的“收藏夹”窗格中单击“所有资源”。 在“所有资源”边栏选项卡中单击“AdatumAppGateway”应用程序网关。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入“AdatumAppGateway”， 轻松访问应用程序网关。

1. 将显示已创建的应用程序网关。 单击“后端池”，然后选择当前后端池“appGatewayBackendPool”，这会打开“appGatewayBackendPool”边栏选项卡。

   ![应用程序网关后端池][4]

1. 单击“添加目标”以添加 FQDN 值的 IP 地址。 选择“IP 地址或 FQDN”作为“类型”，在该字段中输入 IP 地址或 FQDN。 为其他后端池成员重复执行此步骤。 完成后单击“保存”。

### <a name="virtual-machine-and-nic"></a>虚拟机和 NIC

也可以将虚拟机 NIC 添加为后端池成员。 只有应用程序网关所在的虚拟网络中的虚拟机通过下拉列表提供。

1. 创建应用程序网关后，在 Azure 门户的“收藏夹”窗格中单击“所有资源”。 在“所有资源”边栏选项卡中单击“AdatumAppGateway”应用程序网关。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入“AdatumAppGateway”， 轻松访问应用程序网关。

1. 将显示已创建的应用程序网关。 单击“后端池”，然后选择当前后端池“appGatewayBackendPool”，这会打开“appGatewayBackendPool”边栏选项卡。

   ![应用程序网关后端池][5]

1. 单击“添加目标”以添加 FQDN 值的 IP 地址。 选择“虚拟机”作为“类型”，并选择要使用的虚拟机和 NIC。 完成后单击“保存”

   > [!NOTE]
   > 下拉列表中只提供与应用程序网关位于同一虚拟网络中的虚拟机。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、应用程序网关以及所有相关资源，可将其删除。 为此，请从应用程序网关边栏选项卡中选择该资源组，并单击“删除”。

## <a name="next-steps"></a>后续步骤

此方案部署了应用程序网关，并向后端添加了服务器。 后续步骤是通过修改设置以及调整网关中的规则，配置应用程序网关。 通过访问以下文章，可找到这些步骤：

访问[创建自定义运行状况探测](application-gateway-create-probe-portal.md)，了解如何创建自定义运行状况探测

访问[配置 SSL 卸载](application-gateway-ssl-portal.md)，了解如何配置 SSL 卸载并从 Web 服务器中剥离开销较高的 SSL 解密

了解如何使用应用程序网关的 [Web 应用程序防火墙](application-gateway-webapplicationfirewall-overview.md)功能保护应用程序。

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

