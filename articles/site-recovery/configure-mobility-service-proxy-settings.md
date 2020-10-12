---
title: 配置 Azure 到 Azure 灾难恢复的移动服务代理设置 |Microsoft Docs
description: 详细介绍了当客户在其源环境中使用代理时如何配置移动服务。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86133222"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>配置 Azure 到 Azure 灾难恢复的移动服务代理设置

本文提供了有关在使用 [Azure Site Recovery](site-recovery-overview.md) 将 Azure 虚拟机 (VM) 从一个区域复制和恢复到另一个区域时，在目标 Azure VM 上自定义网络配置的指导。

本文档的目的是提供相关步骤，演示如何在 Azure 到 Azure 灾难恢复方案中为 Azure Site Recovery 移动服务配置代理设置。 

代理是网关，可允许/禁用到终结点的网络连接。 代理通常是客户端计算机外部的一台计算机，它会尝试访问网络终结点。 客户端可以使用跳过列表，在不通过代理的情况下直接连接到终结点。 网络管理员可以选择为代理设置用户名和密码，只允许经过身份验证的客户端使用代理。 

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。
了解使用 [Azure Site Recovery](site-recovery-overview.md) 在不同区域之间复制和恢复 Azure VM 的[网络指南](azure-to-azure-about-networking.md)。
确保根据组织需要正确设置代理。

## <a name="configure-the-mobility-service"></a>配置移动服务

移动服务仅支持不进行身份验证的代理。 它提供两种输入代理详细信息的方法，方便与 Site Recovery 终结点通信。 

### <a name="method-1-auto-detection"></a>方法 1：自动检测

在启用复制的过程中，移动服务会自动检测环境设置或 IE 设置（仅限 Windows）中的代理设置。 

- Windows OS：在启用复制的过程中，移动服务会检测 Internet Explorer 中为 Local System 用户配置的代理设置。 若要为 Local System 帐户设置代理，管理员可以使用 psexec 来启动命令提示符，然后启动 Internet Explorer。 
- Windows OS：代理设置配置为环境变量 http_proxy 和 no_proxy。 
- Linux OS：代理设置在 /etc/profile 或 /etc/environment 中配置为环境变量 http_proxy、no_proxy。 
- 自动检测到的代理设置将保存到移动服务代理配置文件 ProxyInfo.conf 
- ProxyInfo.conf 的默认位置 
    - Windows:C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux：/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>方法 2：提供自定义应用程序代理设置

在这种情况下，客户会在移动服务配置文件 ProxyInfo.conf 中提供自定义应用程序代理设置。 使用此方法时，客户可以只为移动服务提供代理，或者为 Azure Site Recovery 移动服务提供不同于计算机上其他应用程序的代理（或者根本不为其他应用程序提供代理）。

## <a name="proxy-template"></a>代理模板
ProxyInfo 包含以下模板 [proxy] Address = http://1.2.3.4 Port = 5678 BypassList = hypervrecoverymanager. windowsazure.storage，，，.net. .net。 BypassList 不支持通配符，如 "*. windows.net"，但赋予 windows.net 的效果足以绕过。 

## <a name="next-steps"></a>后续步骤：
- 请参阅有关复制 Azure VM 复制的[网络指南](./azure-to-azure-about-networking.md)。
- 通过[复制 Azure VM](./azure-to-azure-quickstart.md) 来部署灾难恢复。
