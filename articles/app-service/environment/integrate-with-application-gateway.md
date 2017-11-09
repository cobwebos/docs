---
title: "将 ILB ASE 与 Azure 应用程序网关进行集成"
description: "演练如何将 ILB ASE 的应用与 Azure 应用程序网关进行集成"
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
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>将 ILB ASE 与应用程序网关进行集成 #

[Azure 应用服务环境 (ASE)](./intro.md) 是客户 Azure 虚拟网络子网中的 Azure 应用服务部署。 可通过应用访问的公共或专用终结点来进行部署。 具有专用终结点的 ASE 的部署称之为 ILB ASE。  
Azure 应用程序网关是一个虚拟设备，可提供第 7 层负载平衡、SSL 卸载，和 WAF 保护。 可侦听公共 IP 地址和将流量路由到应用程序终结点。 以下信息描述了如何将 WAF 配置的应用程序网关与 ILB ASE 上的应用进行集成。  

将应用程序网关与 ILB ASE 进行集成是在应用级别上进行的。  将应用程序网关与 ILB ASE 进行集成时，是为 ILB ASE 中特定应用进行的此操作。 会启动单个 ILB ASE 中的托管安全多租户应用程序。  

![应用程序网关指向 ILB ASE 中的某个应用][1]

本演练中的操作：

* 创建应用程序网关
* 配置应用程序网关，以指向 ILB ASE 中的某个应用
* 配置应用，以遵循自定义域名
* 编辑指向应用程序网关的公共 DNS 主机名

若要将应用程序网关与 ILB ASE 集成，需要：

* ILB ASE
* 在 ILB ASE 中运行的应用
* 与 ILB ASE 中应用一起使用的 internet 可路由的域名
* ILB ASE 使用的 ILB 地址（在“设置”->“IP 地址”下的 ASE 门户中）

    ![ILB ASE 使用的 IP 地址][9]
    
* 用于指向应用程序网关的公共 DNS 名称 

有关如何创建 ILB ASE 的详细信息，请读取文档[创建和使用 ILB ASE][ilbase]

本指南假定你希望在部署有 ASE 的同一个 Azure 虚拟网络中有应用程序网关。 在开始创建应用程序网关之前，请先选取或创建将用于承载应用程序网关的子网。 不应使用名为 GatewaySubnet 的子网或 ILB ASE 使用的子网。
如果将应用程序网关放在 GatewaySubnet 中，随后将无法创建虚拟网络网关。 也不能将其放在 ILB ASE 使用的子网中，因为其子网只能有 ASE。

## <a name="steps-to-configure"></a>配置步骤 ##

1. 在 Azure 门户中，请转到“新建”>“网络”>“应用程序网关” 
    1. 提供：
        1. 应用程序网关名称
        1. 选择 WAF
        1. 选择用于 ASE VNet 的相同订阅
        1. 创建或选择资源组
        1. 选择 ASE VNet 所在的位置

    ![新应用程序网关创建基础信息][2]   
    1. 在“设置”区域设置：
        1. ASE VNet
        1. 需要将应用程序网关部署到的子网。 请勿使用 GatewaySubnet，因为它会阻止创建 VPN 网关
        1. 选择公共
        1. 选择公共 IP 地址。 如果没有公共 IP 地址，便在此时创建一个
        1. 一个 HTTP 或多个 HTTP 配置。 如果要配置 HTTP，需要提供 PFX 证书
        1. 选择 Web 应用程序防火墙设置。 此处可启用防火墙，还可根据自己需要将其设置为检测或防止。

    ![新应用程序网关创建设置][3]
    
    1. 在摘要部分评审中，选择“确定”。 应用程序网关安装程序完成可能需要 30 多分钟。  

2. 应用程序网关安装完成后，请转到你的应用程序网关门户。 选择“后端池”。  添加 ILB ASE 的 ILB 地址。

    ![配置后端池][4]

3. 后端池配置程序完成后，选择“运行状况探测”。 为想用于应用的域名创建运行状况探测。 

    ![配置运行状况探测][5]
    
4. 运行状况探测配置程序完成后，选择“HTTP 设置”。  编辑现有设置，选择“使用自定义探测”，然后选择你配置的探测。

    ![配置 HTTP 设置][6]
    
5. 转到应用程序网关“概述”，并复制用于应用程序网关的公共 IP 地址。  将该 IP 地址设置为应用域名的 A 记录，或在 CNAME 记录中使用该地址的 DNS 名称。  从公共 IP 地址 UI 选择公共 IP 地址并复制此地址，比从应用程序网关概述部分上的链接复制更为简单。 

    ![应用程序网关门户][7]

6. 为 ILB ASE 中的应用设置自定义域名。  转到门户中的应用，并在“设置”下选择“自定义域”

![在该应用上设置自定义域名][8]

[设置 web 应用自定义域名][custom-domain]中提供有设置 web 应用自定义域名的信息。 域名与 ILB ASE 中应用和该文档的差异是域名不存在任何验证。  拥有管理应用程序终结点的 DNS，便可随意设置域名。 在这种情况下添加的自定义域名无需存在于 DNS 中，但它仍需要通过应用进行配置。 

安装完成之后，有一小段时间进行 DNS 更改传播，然后，便可使用创建的自定义域名访问应用。 


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
