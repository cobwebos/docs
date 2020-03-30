---
title: 通过云从本地轻松访问 Azure VMware 解决方案
titleSuffix: Azure VMware Solution by CloudSimple
description: 通过防火墙通过云简单从本地网络访问 Azure VMware 解决方案
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df4c51953c6f50e30ba61b993cdb35856fcb8e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082969"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>从本地访问云简单私有云环境和应用程序

可以使用 Azure ExpressRoute 或站点到站点 VPN 从本地网络连接到云简单连接。  访问云简单私有云 vCenter 以及您使用连接在私有云上运行的任何工作负载。  您可以使用本地网络中的防火墙控制连接上打开的端口。  本文讨论了一些典型的应用程序端口要求。  对于任何其他应用程序，请参阅有关端口要求的应用程序文档。

## <a name="ports-required-for-accessing-vcenter"></a>访问 vCenter 所需的端口

要访问私有云 vCenter 和 NSX-T 管理器，必须在本地防火墙上打开下表中定义的端口。  

| 端口       | 源                           | 目标                      | 目的                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 （UDP）   | 本地 DNS 服务器          | 私有云 DNS 服务器        | 需要将*az.cloudsimple.io*的 DNS 查找从本地网络转发到私有云 DNS 服务器。       |
| 53 （UDP）   | 私有云 DNS 服务器        | 本地 DNS 服务器          | 需要将 DNS 从私有云 vCenter 到本地 DNS 服务器的本地域名进行查找。 |
| 80 （TCP）   | 本地网络              | 私有云管理网络 | 需要将 vCenter URL 从*http*重定向到*https*。                                                           |
| 443 （TCP）  | 本地网络              | 私有云管理网络 | 从本地网络访问 vCenter 和 NSX-T 管理器是必需的。                                             |
| 8000 （TCP） | 本地网络              | 私有云管理网络 | 虚拟机从本地到私有云的 vMotion 需要。                                            |
| 8000 （TCP） | 私有云管理网络 | 本地网络              | 虚拟机从私有云到本地的 vMotion 需要。                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>将本地活动目录用作标识源所需的端口

要将本地活动目录配置为私有云 vCenter 上的标识源，必须打开表中定义的端口。  有关配置步骤[，请参阅在云简单私有云上使用 Azure AD 作为 vCenter 的标识提供程序](https://docs.azure.cloudsimple.com/azure-ad/)。

| 端口         | 源                           | 目标                                         | 目的                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 （UDP）      | 私有云 DNS 服务器        | 本地 DNS 服务器                             | 需要将 DNS 从私有云 vCenter 到本地 DNS 服务器的本地活动目录域名进行查找。          |
| 389 (TCP/UDP) | 私有云管理网络 | 本地活动目录域控制器     | LDAP 通信从私有云 vCenter 服务器到活动目录域控制器进行用户身份验证所需的。                |
| 636 （TCP）     | 私有云管理网络 | 本地活动目录域控制器     | 从私有云 vCenter 服务器到活动目录域控制器的安全 LDAP （LDAPS） 通信需要，以便进行用户身份验证。 |
| 3268 （TCP）    | 私有云管理网络 | 本地活动目录全局目录服务器 | 多域控制器部署中 LDAP 通信是必需的。                                                                        |
| 3269 （TCP）    | 私有云管理网络 | 本地活动目录全局目录服务器 | 多域控制器部署中 LDAPS 通信是必需的。                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>访问工作负载虚拟机所需的通用端口

访问在私有云上运行的工作负载虚拟机需要在本地防火墙上打开端口。  下表显示了所需的一些常见端口及其用途。  对于任何特定于应用程序的端口要求，请参阅应用程序文档。

| 端口         | 源                         | 目标                          | 目的                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 （TCP）      | 本地网络            | 私有云工作负载网络       | 安全访问在私有云上运行的 Linux 虚拟机。              |
| 3389 （TCP）    | 本地网络            | 私有云工作负载网络       | 远程桌面到在私有云上运行的窗口虚拟机。                 |
| 80 （TCP）      | 本地网络            | 私有云工作负载网络       | 访问部署在私有云上运行的虚拟机上的任何 Web 服务器。        |
| 443 （TCP）     | 本地网络            | 私有云工作负载网络       | 访问部署在私有云上运行的虚拟机上的任何安全 Web 服务器。 |
| 389 (TCP/UDP) | 私有云工作负载网络 | 本地活动目录网络 | 将 Windows 工作负载虚拟机加入本地活动目录域。       |
| 53 （UDP）      | 私有云工作负载网络 | 本地网络                  | 工作负载虚拟机对本地 DNS 服务器的 DNS 服务访问。         |

## <a name="next-steps"></a>后续步骤

* [创建和管理 VLAN 和子网](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [使用 Azure 快速路由连接到本地网络](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [从本地设置站点到站点 VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)
