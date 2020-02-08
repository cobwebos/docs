---
title: 从本地访问 Azure VMware 解决方案（AVS）
description: 通过防火墙从本地网络访问 Azure VMware 解决方案（AVS）
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a4a9760b5c7a70c58a1afe1b14b781a35f2b9b18
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082969"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>从本地访问你的 AVS 私有云环境和应用程序

使用 Azure ExpressRoute 或站点到站点 VPN，可以从本地网络设置到 AVS 的连接。 使用连接访问你的 AVS 私有云 vCenter 和你在 AVS 私有云上运行的任何工作负荷。 你可以使用本地网络中的防火墙控制连接上打开的端口。 本文介绍了一些典型的应用程序端口要求。 对于任何其他应用程序，请参阅应用程序文档以了解端口要求。

## <a name="ports-required-for-accessing-vcenter"></a>访问 vCenter 所需的端口

若要访问你的 AVS 私有云 vCenter 和 NSX-T 管理器，必须在本地防火墙上打开下表中定义的端口。 

| 端口       | 源                           | 目标                      | 目标                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53（UDP）   | 本地 DNS 服务器          | AVS 私有云 DNS 服务器        | 需要将*az.cloudsimple.io*的 DNS 查找从本地网络转发到 AVS 私有云 DNS 服务器。     |
| 53（UDP）   | AVS 私有云 DNS 服务器        | 本地 DNS 服务器          | 需要将 DNS 查找从 AVS 私有云 vCenter 到本地 DNS 服务器的本地域名。 |
| 80（TCP）   | 本地网络              | AVS 私有云管理网络 | 需要将 vCenter URL 从*http*重定向到*https*。                                                         |
| 443（TCP）  | 本地网络              | AVS 私有云管理网络 | 从本地网络访问 vCenter 和 NSX-T 管理器时需要。                                           |
| 8000（TCP） | 本地网络              | AVS 私有云管理网络 | 对于从本地到 AVS 私有云的虚拟机，需要此项。                                          |
| 8000（TCP） | AVS 私有云管理网络 | 本地网络              | 对于从 AVS 私有云到本地的虚拟机，需要此项。                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>使用本地 active directory 作为标识源所需的端口

若要在 AVS 私有云 vCenter 上将本地 active directory 配置为标识源，必须打开表中定义的端口。 有关配置步骤，请参阅[在 AVS Avs 私有云上使用 Azure AD 作为 vCenter 的标识提供者](https://docs.azure.cloudsimple.com/azure-ad/)。

| 端口         | 源                           | 目标                                         | 目标                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53（UDP）      | AVS 私有云 DNS 服务器        | 本地 DNS 服务器                             | 需要将 DNS 查找从 AVS 私有云 vCenter 到本地 DNS 服务器的本地 active directory 域名。        |
| 389 (TCP/UDP) | AVS 私有云管理网络 | 本地 active directory 域控制器     | 从 AVS 私有云 vCenter 服务器到 active directory 域控制器的 LDAP 通信需要进行用户身份验证。              |
| 636（TCP）     | AVS 私有云管理网络 | 本地 active directory 域控制器     | 安全 LDAP （LDAPS）从 AVS 私有云 vCenter 服务器到 active directory 域控制器的通信需要用于用户身份验证。 |
| 3268（TCP）    | AVS 私有云管理网络 | 本地 active directory 全局编录服务器 | 需要在多域控制器部署中进行 LDAP 通信。                                                                      |
| 3269（TCP）    | AVS 私有云管理网络 | 本地 active directory 全局编录服务器 | 需要在多域控制器部署中进行 LDAPS 通信。                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>访问工作负荷虚拟机所需的常用端口

Access 工作负荷在 AVS 私有云上运行的虚拟机需要在本地防火墙上打开端口。 下表显示了所需的一些常用端口及其用途。 有关任何特定于应用程序的端口要求，请参阅应用程序文档。

| 端口         | 源                         | 目标                          | 目标                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22（TCP）      | 本地网络            | AVS 私有云工作负荷网络       | 安全外壳访问在 AVS 私有云上运行的 Linux 虚拟机。            |
| 3389（TCP）    | 本地网络            | AVS 私有云工作负荷网络       | 远程桌面到在 AVS 私有云上运行的 windows 虚拟机。               |
| 80（TCP）      | 本地网络            | AVS 私有云工作负荷网络       | 访问在 AVS 私有云上运行的虚拟机上部署的任何 web 服务器。      |
| 443（TCP）     | 本地网络            | AVS 私有云工作负荷网络       | 访问在 AVS 私有云上运行的虚拟机上部署的任何安全 web 服务器。 |
| 389 (TCP/UDP) | AVS 私有云工作负荷网络 | 本地 active directory 网络 | 将 Windows 工作负荷虚拟机加入本地 active directory 域。     |
| 53（UDP）      | AVS 私有云工作负荷网络 | 本地网络                  | 为本地 DNS 服务器的工作负荷虚拟机提供 DNS 服务访问。       |

## <a name="next-steps"></a>后续步骤

* [创建和管理 Vlan 和子网](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [使用 Azure ExpressRoute 连接到本地网络](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [从本地设置站点到站点 VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)
