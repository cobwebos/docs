---
title: Azure 防火墙远程工作支持
description: 本文介绍 Azure 防火墙如何支持远程工作人员需求。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: da5100fafc98ae38809c93e9b3db5ef41c58766a
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863410"
---
# <a name="azure-firewall-remote-work-support"></a>Azure 防火墙远程工作支持

Azure 防火墙是一种基于云的托管网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>虚拟桌面基础结构（VDI）部署支持

家庭策略中的工作要求许多 IT 组织解决容量、网络、安全性和管理方面的基本变化。 在家工作时，员工不受与本地服务关联的分层安全策略的保护。 Azure 上的虚拟桌面基础结构（VDI）部署可帮助组织快速应对此变化的环境。 但是，您需要一种方法来保护对这些 VDI 部署的入站/出站 Internet 访问。 可以使用 Azure 防火墙[DNAT 规则](rule-processing.md)及其基于[威胁情报](threat-intel.md)的筛选功能来保护 VDI 部署。

## <a name="azure-windows-virtual-desktop-support"></a>Azure Windows 虚拟桌面支持

Windows 虚拟桌面是一个在 Azure 中运行的综合性桌面和应用虚拟化服务。 这是唯一一种可提供简化管理、多会话 Windows 10、对 Office 365 专业增强版的优化以及对远程桌面服务 (RDS) 环境的支持的虚拟桌面基础结构 (VDI)。 你可以在几分钟内部署和缩放 Azure 上的 Windows 桌面和应用，并获得内置的安全性和符合性功能。 Windows 虚拟桌面不需要您打开虚拟网络的任何入站访问权限。 但是，你必须为在虚拟网络中运行的 Windows 虚拟机虚拟机允许一组出站网络连接。 有关详细信息，请参阅[使用 Azure 防火墙保护 Windows 虚拟桌面部署](protect-windows-virtual-desktop.md)。

## <a name="next-steps"></a>后续步骤

详细了解[Windows 虚拟桌面](https://docs.microsoft.com/azure/virtual-desktop/)。