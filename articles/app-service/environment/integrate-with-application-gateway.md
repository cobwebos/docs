---
title: 将 ILB 应用服务环境与应用程序网关集成
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
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: c64b686d7a9016b3834096ebc88179db8972098f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="integrate-your-ilb-app-service-environment-with-an-application-gateway"></a>将 ILB 应用服务环境与应用程序网关集成 #

[应用服务环境](./intro.md)是客户的 Azure 虚拟网络子网中的 Azure 应用服务部署。 可通过应用访问的公共或专用终结点来进行部署。 具有专用终结点（即，内部负载平衡器）的应用服务环境的部署称为 ILB 应用服务环境。  

Azure 应用程序网关是一个虚拟设备，可提供第 7 层负载平衡、SSL 卸载以及 Web 应用程序防火墙 (WAF) 保护。 可侦听公共 IP 地址和将流量路由到应用程序终结点。 

以下信息描述了如何将 WAF 配置的应用程序网关与 ILB 应用服务环境中的应用进行集成。  

将应用程序网关与 ILB 应用服务环境进行集成是在应用级别上进行的。 将应用程序网关与 ILB 应用服务环境进行集成时，是为 ILB 应用服务环境中特定应用进行的此操作。 此技术可以在单个 ILB 应用服务环境中托管安全的多租户应用程序。  

![应用程序网关指向 ILB 应用服务环境中的某个应用][1]

本演练中的操作：

* 创建应用程序网关。
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
    
* 用于指向应用程序网关的公共 DNS 名称。 

有关如何创建 ILB 应用服务环境的详细信息，请参阅[创建和使用 ILB 应用服务环境][ilbase]。

本文假定你希望应用程序网关位于部署应用服务环境的同一个 Azure 虚拟网络中。 在开始创建应用程序网关之前，请选择或创建一个将用于托管网关的子网。 

不应使用名为 GatewaySubnet 的子网。 如果将应用程序网关放在 GatewaySubnet 中，随后将无法创建虚拟网络网关。 

也不能将网关放在 ILB 应用服务环境使用的子网中。 只有应用服务环境可以在此子网中。

## <a name="configuration-steps"></a>配置步骤 ##

1. 在 Azure 门户 中，转到“新建” > “网络” > “应用程序网关”。

2. 在“基本信息”区域中：

   a. 对于“名称”，输入应用程序网关的名称。

   b. 对于“层级”，选择“WAF”。

   c. 对于“订阅”，选择应用服务环境虚拟网络使用的相同订阅。

   d.单击“下一步”。 对于“资源组”，创建或选择资源组。

   e. 对于“位置”，选择应用服务环境虚拟网络使用的位置。

   ![新应用程序网关创建基础信息][2]

3. 在“设置”区域中：

   a. 对于“虚拟网络”，选择应用服务环境虚拟网络。

   b. 对于“子网”，选择需要将应用程序网关部署到的子网。 请勿使用 GatewaySubnet，因为它会阻止创建 VPN 网关。

   c. 对于“IP 地址类型”，选择“公共”。

   d.单击“下一步”。 对于“公共 IP 地址”，选择一个公共 IP 地址。 如果没有，请立即创建一个。

   e. 对于“协议”，选择“HTTP”或“HTTPS”。 如果要配置 HTTPS，需要提供 PFX 证书。

   f. 对于“Web 应用程序防火墙”，可启用防火墙，还可根据自己需要将其设置为“检测”或“防止”。

   ![新应用程序网关创建设置][3]
    
4. 在“摘要”部分中，查看设置并选择“确定”。 应用程序网关安装程序完成可能需要 30 多分钟。  

5. 应用程序网关安装完成后，请转到你的应用程序网关门户。 选择“后端池”。 添加 ILB 应用服务环境的 ILB 地址。

   ![配置后端池][4]

6. 后端池配置过程完成后，选择“运行状况探测”。 为想用于应用的域名创建运行状况探测。 

   ![配置运行状况探测][5]
    
7. 运行状况探测配置过程完成后，选择“HTTP 设置”。 编辑现有设置，选择“使用自定义探测”，然后选择你配置的探测。

   ![配置 HTTP 设置][6]
    
8. 转到应用程序网关的“概述”部分，并复制用于应用程序网关的公共 IP 地址。 将该 IP 地址设置为应用域名的 A 记录，或在 CNAME 记录中使用该地址的 DNS 名称。 从公共 IP 地址 UI 选择公共 IP 地址并复制此地址，比从应用程序网关的“概述”部分上的链接进行复制更为简单。 

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
