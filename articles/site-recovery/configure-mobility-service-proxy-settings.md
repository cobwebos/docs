---
title: 配置 Azure 到 Azure 灾难恢复的移动服务代理设置 |Microsoft Docs
description: 提供有关如何在客户在其源环境中使用代理时配置移动服务的详细信息。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503122"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>配置 Azure 到 Azure 灾难恢复的移动服务代理设置

本文提供了有关在使用[Azure Site Recovery](site-recovery-overview.md)将 azure vm 从一个区域复制和恢复到另一个区域时，如何在目标 Azure 虚拟机（VM）上自定义网络配置的指导。

本文档的目的是为 azure 到 Azure 灾难恢复方案中的 Azure Site Recovery 移动服务提供配置代理设置的步骤。 

代理是允许/禁止网络连接到端点的网络网关。 通常，代理是客户端计算机外部的一台计算机，尝试访问网络终结点。 使用跳过列表，客户端无需通过代理即可直接连接到终结点。 网络管理员可以选择为代理设置用户名和密码，以便只有经过身份验证的客户端才能使用代理。 

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。
使用[Azure Site Recovery](site-recovery-overview.md)从一个区域复制和恢复 Azure vm 时，了解[网络指导](azure-to-azure-about-networking.md)。
确保根据组织的需要，正确设置代理。

## <a name="configure-the-mobility-service"></a>配置移动服务

移动服务仅支持未经身份验证的代理。 它提供了两种方法来输入代理详细信息，以便与 Site Recovery 终结点进行通信。 

### <a name="method-1-auto-detection"></a>方法1：自动检测

移动服务在启用复制的过程中自动从环境设置或 IE 设置（仅限 Windows）检测代理设置。 

- Windows OS：在启用复制期间，移动服务将检测 Internet Explorer 中为本地系统用户配置的代理设置。 若要设置本地系统帐户的代理，管理员可以使用 psexec 启动命令提示符，然后启动 Internet Explorer。 
- Windows OS：代理设置配置为 http_proxy 和 no_proxy 环境变量。 
- Linux OS：在/etc/profile 或/etc/environment 中将代理设置配置为 http_proxy no_proxy 的环境变量。 
- 自动检测到的代理设置保存到移动服务代理配置文件 ProxyInfo 
- ProxyInfo 的默认位置 
    - Windows： C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux：/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>方法2：提供自定义应用程序代理设置

在这种情况下，客户会在移动服务配置文件 ProxyInfo 中提供自定义应用程序代理设置。 此方法允许客户仅为移动服务提供代理，或为 Azure Site Recovery 移动服务提供不同于计算机上其他应用程序的代理（或不使用代理）的代理。

## <a name="proxy-template"></a>代理模板
ProxyInfo 包含以下模板 [proxy] Address =http://1.2.3.4 Port = 5678 BypassList = hypervrecoverymanager. windowsazure.storage，，，.net. .net。 BypassList 不支持通配符，如 "*. windows.net"，但赋予 windows.net 的效果足以绕过。 

## <a name="next-steps"></a>后续步骤：
- 有关复制 Azure Vm 的详细阅读[网络指南](site-recovery-azure-to-azure-networking-guidance.md)。
- 通过[复制 Azure VM](site-recovery-azure-to-azure.md) 来部署灾难恢复。