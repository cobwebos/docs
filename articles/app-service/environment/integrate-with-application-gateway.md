---
title: 将 ILB 应用服务环境与 Azure 应用程序网关集成
description: 有关如何将 ILB 应用服务环境中的应用与应用程序网关集成的演练
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.openlocfilehash: 31aea1d19ed6da856bb5fc634a919819513cb6b2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
ms.locfileid: "30833578"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>将 ILB 应用服务环境与 Azure 应用程序网关集成 #

[应用服务环境](./intro.md)是客户的 Azure 虚拟网络子网中的 Azure 应用服务部署。 可通过应用访问的公共或专用终结点来进行部署。 具有专用终结点（即，内部负载平衡器）的应用服务环境的部署称为 ILB 应用服务环境。  

Web 应用程序防火墙会检查入站 Web 流量，并阻止 SQL 注入、跨站点脚本、恶意软件上传和应用程序 DDoS 及其他攻击，从而帮助你保护 Web 应用程序的安全。 它还会检查后端 Web 服务器的响应，实现针对数据丢失预防 (DLP)。 可以从 Azure Marketplace 获取 WAF 设备，也可以使用 [Azure 应用程序网关][appgw]。

Azure 应用程序网关是一个虚拟设备，可提供第 7 层负载均衡、SSL 卸载以及 Web 应用程序防火墙 (WAF) 保护。 可侦听公共 IP 地址和将流量路由到应用程序终结点。 以下信息描述了如何将 WAF 配置的应用程序网关与 ILB 应用服务环境中的应用进行集成。  

将应用程序网关与 ILB 应用服务环境进行集成是在应用级别上进行的。 将应用程序网关与 ILB 应用服务环境进行集成时，是为 ILB 应用服务环境中特定应用进行的此操作。 此技术可以在单个 ILB 应用服务环境中托管安全的多租户应用程序。  

![应用程序网关指向 ILB 应用服务环境中的某个应用][1]

本演练中的操作：

* 创建 Azure 应用程序网关。
* 配置应用程序网关，以指向 ILB 应用服务环境中的某个应用。
* 配置应用，以遵循自定义域名。
* 编辑指向应用程序网关的公共 DNS 主机名。

## <a name="prerequisites"></a>先决条件

若要将应用程序网关与 ILB 应用服务环境集成，需要：

* ILB 应用服务环境。
* ILB 应用服务环境中运行的应用。
* 与 ILB 应用服务环境中应用一起使用的 Internet 可路由的域名。
* ILB 应用服务环境使用的 ILB 地址。 此信息位于“设置” > “IP地址”下的应用服务环境门户中：

    ![ILB 应用服务环境使用的 IP 地址示例列表][9]
    
* 稍后用于指向应用程序网关的公共 DNS 名称。 

有关如何创建 ILB 应用服务环境的详细信息，请参阅[创建和使用 ILB 应用服务环境][ilbase]。

本文假定你在部署了应用服务环境的 Azure 虚拟网络中需要一个应用程序网关。 在开始创建应用程序网关之前，请选择或创建一个将用来承载网关的子网。 

不应使用名为 GatewaySubnet 的子网。 如果将应用程序网关放在 GatewaySubnet 中，随后将无法创建虚拟网络网关。 

也不能将网关放在 ILB 应用服务环境使用的子网中。 只有应用服务环境可以在此子网中。

## <a name="configuration-steps"></a>配置步骤 ##

1. 在 Azure 门户 中，转到“新建” > “网络” > “应用程序网关”。

2. 在“基本信息”区域中：

   a. 对于“名称”，输入应用程序网关的名称。

   b. 对于“层级”，选择“WAF”。

   c. 对于“订阅”，选择应用服务环境虚拟网络使用的相同订阅。

   d. 对于“资源组”，创建或选择资源组。

   e. 对于“位置”，选择应用服务环境虚拟网络使用的位置。

   ![新应用程序网关创建基本信息][2]

3. 在“设置”区域中：

   a. 对于“虚拟网络”，选择应用服务环境虚拟网络。

   b. 对于“子网”，选择需要将应用程序网关部署到的子网。 请勿使用 GatewaySubnet，因为它会阻止创建 VPN 网关。

   c. 对于“IP 地址类型”，选择“公共”。

   d. 对于“公共 IP 地址”，选择一个公共 IP 地址。 如果没有，请立即创建一个。

   e. 对于“协议”，选择“HTTP”或“HTTPS”。 如果要配置 HTTPS，需要提供 PFX 证书。

   f. 对于“Web 应用程序防火墙”，可启用防火墙，还可根据自己需要将其设置为“检测”或“防止”。

   ![新应用程序网关创建设置][3]
    
4. 在“摘要”部分中，查看设置并选择“确定”。 应用程序网关安装程序可能需要 30 多分钟才能完成。  

5. 在应用程序网关安装完成后，转到你的应用程序网关门户。 选择“后端池”。 添加 ILB 应用服务环境的 ILB 地址。

   ![配置后端池][4]

6. 后端池配置过程完成后，选择“运行状况探测”。 为想用于应用的域名创建运行状况探测。 

   ![配置运行状况探测][5]
    
7. 运行状况探测配置过程完成后，选择“HTTP 设置”。 编辑现有设置，选择“使用自定义探测”，然后选择你配置的探测。

   ![配置 HTTP 设置][6]
    
8. 转到应用程序网关的“概述”部分，并复制应用程序网关使用的公用 IP 地址。 将该 IP 地址设置为应用域名的 A 记录，或在 CNAME 记录中使用该地址的 DNS 名称。 从公用 IP 地址 UI 选择公用 IP 地址并复制此地址，比从应用程序网关的“概述”部分上的链接进行复制更为简单。 

   ![应用程序网关门户][7]

9. 为 ILB 应用服务环境中的应用设置自定义域名。 转到门户中的应用，并在“设置”下选择“自定义域”。

   ![在该应用上设置自定义域名][8]

[设置 Web 应用自定义域名][custom-domain]一文中提供有关设置 Web 应用自定义域名的信息。 但是对于 ILB 应用服务环境中的应用，域名上不存在任何验证。 因为拥有管理应用终结点的 DNS，便可随意设置域名。 在这种情况下添加的自定义域名无需存在于 DNS 中，但它仍需要通过应用进行配置。 

安装完成之后，有一小段时间进行 DNS 更改传播，可使用创建的自定义域名访问应用。 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
