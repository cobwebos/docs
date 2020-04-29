---
title: Azure 防火墙远程工作支持
description: 本文介绍 Azure 防火墙如何支持远程工作强制要求。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289636"
---
# <a name="azure-firewall-remote-work-support"></a>Azure 防火墙远程工作支持

Azure 防火墙是一种托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 

## <a name="firewall-rules"></a>防火墙规则

可以使用 azure 防火墙通过 Azure 防火墙[DNAT 规则](rule-processing.md)保护虚拟桌面基础结构（VDI）对 Azure 虚拟网络的入站 RDP 访问。 Windows 虚拟桌面（WVD）不需要您打开虚拟网络的任何入站访问权限。 但是，你必须为在虚拟网络中运行的 WVD 虚拟机允许一组出站网络连接。 有关详细信息，请参阅[什么是 Windows 虚拟桌面？](../virtual-desktop/overview.md#requirements)

可以使用 Azure 防火墙应用程序规则配置此出站访问。 有关详细信息，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。

## <a name="next-steps"></a>后续步骤

详细了解[Windows 虚拟桌面](https://docs.microsoft.com/azure/virtual-desktop/)。