---
title: 从本地 CloudSimple 访问 Azure VMware 解决方案
description: 通过防火墙从本地网络 CloudSimple 访问 Azure VMware 解决方案
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0f575417819f0e2d46565ad15aaa23a04fd7cf1
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972641"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>从本地访问你的 CloudSimple 私有云环境和应用程序

可以使用 Azure ExpressRoute 或站点到站点 VPN 将连接从本地网络设置为 CloudSimple。  使用连接访问你在私有云上运行的 CloudSimple 私有云 vCenter 和任何工作负荷。  你可以使用本地网络中的防火墙控制连接上打开的端口。  本文介绍了一些典型的应用程序端口要求。  对于任何其他应用程序, 请参阅应用程序文档以了解端口要求。

## <a name="ports-required-for-accessing-vcenter"></a>访问 vCenter 所需的端口

若要访问私有云 vCenter 和 NSX-T 管理器, 必须在本地防火墙上打开下表中定义的端口。  

| Port       | Source                           | 目标                      | 用途                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | 本地 DNS 服务器          | 私有云 DNS 服务器        | 需要将*az.cloudsimple.io*的 dns 查找从本地网络转发到私有云 DNS 服务器。       |
| 53 (UDP)   | 私有云 DNS 服务器        | 本地 DNS 服务器          | 需要将 DNS 查找从私有云 vCenter 到本地 DNS 服务器的本地域名。 |
| 80 (TCP)   | 本地网络              | 私有云管理网络 | 需要将 vCenter URL 从*http*重定向到*https*。                                                           |
| 443 (TCP)  | 本地网络              | 私有云管理网络 | 从本地网络访问 vCenter 和 NSX-T 管理器时需要。                                             |
| 8000 (TCP) | 本地网络              | 私有云管理网络 | 对于从本地到私有云的虚拟机是必需的。                                            |
| 8000 (TCP) | 私有云管理网络 | 本地网络              | 对于从私有云到本地的虚拟机是必需的。                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>使用本地 active directory 作为标识源所需的端口

若要在私有云 vCenter 上将本地 active directory 配置为标识源, 必须打开表中定义的端口。  有关配置步骤, 请参阅[使用 Azure AD 作为 CloudSimple 私有云上的 vCenter 的标识提供者](https://docs.azure.cloudsimple.com/azure-ad/)。

| Port         | Source                           | 目标                                         | 用途                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | 私有云 DNS 服务器        | 本地 DNS 服务器                             | 需要将 DNS 查找从私有云 vCenter 到本地 DNS 服务器的本地 active directory 域名。          |
| 389 (TCP/UDP) | 私有云管理网络 | 本地 active directory 域控制器     | 从私有云 vCenter 服务器到 active directory 域控制器的 LDAP 通信需要进行用户身份验证。                |
| 636 (TCP)     | 私有云管理网络 | 本地 active directory 域控制器     | 安全 LDAP (LDAPS) 从私有云 vCenter 服务器到 active directory 域控制器的通信需要进行用户身份验证。 |
| 3268 (TCP)    | 私有云管理网络 | 本地 active directory 全局编录服务器 | 需要在多域控制器部署中进行 LDAP 通信。                                                                        |
| 3269 (TCP)    | 私有云管理网络 | 本地 active directory 全局编录服务器 | 需要在多域控制器部署中进行 LDAPS 通信。                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>访问工作负荷虚拟机所需的常用端口

访问工作负荷在私有云上运行的虚拟机需要在本地防火墙上打开端口。  下表显示了所需的一些常用端口及其用途。  有关任何特定于应用程序的端口要求, 请参阅应用程序文档。

| Port         | Source                         | 目标                          | 用途                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | 本地网络            | 私有云工作负荷网络       | 安全外壳访问私有云上运行的 Linux 虚拟机。              |
| 3389 (TCP)    | 本地网络            | 私有云工作负荷网络       | 远程桌面到在私有云上运行的 windows 虚拟机。                 |
| 80 (TCP)      | 本地网络            | 私有云工作负荷网络       | 访问在私有云上运行的虚拟机上部署的任何 web 服务器。        |
| 443 (TCP)     | 本地网络            | 私有云工作负荷网络       | 访问在私有云上运行的虚拟机上部署的任何安全 web 服务器。 |
| 389 (TCP/UDP) | 私有云工作负荷网络 | 本地 active directory 网络 | 将 Windows 工作负荷虚拟机加入本地 active directory 域。       |
| 53 (UDP)      | 私有云工作负荷网络 | 本地网络                  | 为本地 DNS 服务器的工作负荷虚拟机提供 DNS 服务访问。         |

## <a name="next-steps"></a>后续步骤

* [创建和管理 Vlan 和子网](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [使用 Azure ExpressRoute 连接到本地网络](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [从本地设置站点到站点 VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)
