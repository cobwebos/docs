---
title: 为 Azure 配置 Azure 到 Azure 灾难恢复的移动服务代理设置 |微软文档
description: 提供有关客户在其源环境中使用代理时如何配置移动服务的详细信息。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503122"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>为 Azure 配置移动服务代理设置以 Azure 灾难恢复

本文提供有关使用[Azure 站点恢复](site-recovery-overview.md)将 Azure VM 从一个区域复制和恢复 Azure VM 时在目标 Azure 虚拟机 （VM） 上自定义网络配置的指导。

本文档的目的是提供步骤，在 Azure 到 Azure 灾难恢复方案中为 Azure 站点恢复移动服务配置代理设置。 

代理是允许/不允许网络连接到终结点的网络网关。 通常，代理是客户端计算机外尝试访问网络终结点的计算机。 旁路列表允许客户端直接连接到终结点，而无需通过代理。 网络管理员可以选择为代理设置用户名和密码，以便只有经过身份验证的客户端才能使用代理。 

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。
使用[Azure 站点恢复](site-recovery-overview.md)将 Azure VM 从一个区域复制和恢复到另一个区域时，了解[网络指南](azure-to-azure-about-networking.md)。
确保根据组织的需求适当地设置代理。

## <a name="configure-the-mobility-service"></a>配置移动服务

移动服务仅支持未经身份验证的代理。 它提供了两种输入代理详细信息的方法，以便与站点恢复终结点通信。 

### <a name="method-1-auto-detection"></a>方法 1：自动检测

移动服务在启用复制期间自动检测环境设置或 IE 设置（仅限 Windows）的代理设置。 

- Windows OS：在启用复制期间，移动服务检测本地系统用户在 Internet 资源管理器中配置的代理设置。 要为本地系统帐户设置代理，管理员可以使用 psexec 启动命令提示符，然后使用 Internet 资源管理器。 
- Windows OS：代理设置配置为环境变量http_proxy和no_proxy。 
- Linux OS：代理设置在 /etc/配置文件或 /etc//环境中配置为环境变量http_proxy，no_proxy。 
- 自动检测到的代理设置将保存到移动服务代理配置文件代理信息.conf 
- 代理信息.conf 的默认位置 
    - 窗口：C：\程序数据\微软 Azure 站点恢复\Config_代理信息.conf 
    - Linux： /usr/本地/Inmage/配置/代理信息.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>方法 2：提供自定义应用程序代理设置

在这种情况下，客户在移动服务配置文件 ProxyInfo.conf 中提供自定义应用程序代理设置。 此方法允许客户仅为移动服务提供代理，或者为 Azure 站点恢复移动服务提供与计算机上的其余应用程序的代理（或无代理）不同的代理。

## <a name="proxy-template"></a>代理模板
ProxyInfo.conf 包含以下模板 [代理] 地址http://1.2.3.4= 端口=5678 绕过列表_超v恢复管理器.windowsazure.com，login.microsoftonline.com，blob.core.windows.net。 旁路列表不支持"*.windows.net"之类的通配符，但给出windows.net足以绕过。 

## <a name="next-steps"></a>后续步骤：
- 阅读复制 Azure VM[的网络指南](site-recovery-azure-to-azure-networking-guidance.md)。
- 通过[复制 Azure VM](site-recovery-azure-to-azure.md) 来部署灾难恢复。