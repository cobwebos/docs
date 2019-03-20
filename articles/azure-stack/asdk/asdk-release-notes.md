---
title: Microsoft Azure Stack 开发工具包发行说明 |Microsoft Docs
description: Azure Stack 开发工具包的改进、修复和已知问题。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 33f1ccf3f1c7bc657cc66efe7c5025356c954ad6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58187755"
---
# <a name="asdk-release-notes"></a>ASDK 发行说明

本文介绍了 Azure Stack 开发工具包 (ASDK) 中的更改、修复和已知问题。 如果不确定所运行的版本，可以[使用门户检查版本](../azure-stack-updates.md#determine-the-current-version)。

请订阅 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [源](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，随时了解 ASDK 的新增功能。

## <a name="build-11902069"></a>生成 1.1902.0.69

### <a name="new-features"></a>新增功能

- 1902 版本在 Azure Stack 管理员门户上引入了新的用户界面用于创建计划、套餐、配额和附加计划。 有关详细信息（包括屏幕截图），请参阅[创建计划、套餐和配额](../azure-stack-create-plan.md)。

- 此版本中的其他更改和改进的列表，请参阅[本节](../azure-stack-update-1902.md#improvements)Azure Stack 中发行说明。

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>已知问题

- 我们已识别到以下问题：发往内部负载均衡器 (ILB) 的超过 1450 字节的数据包将被丢弃。 该问题的原因是主机上的 MTU 设置过小，无法容纳遍历角色的 VXLAN 封装数据包，自版本 1901 开始，该角色已移到主机。 有至少两个方案，可能会遇到我们曾经表现此问题：

  - SQL 查询发往内部负载均衡器 (ILB) 后面的 SQL Always On，并且超过 660 字节。
  - 如果尝试启用多个主节点，Kubernetes 部署失败。  

  在同一虚拟网络但不同子网中的 VM 与 ILB 之间进行通信时，将发生此问题。 在 ASDK 主机上权限提升的命令提示符下运行以下命令即可解决此问题：

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>内部版本 1.1901.0.95

请参阅[Azure Stack 发行说明中的重要的生成信息](../azure-stack-update-1901.md#build-reference)。

### <a name="changes"></a>更改

此内部版本包含对 Azure Stack 的以下改进：

- BGP 和 NAT 组件现在部署在物理主机上。 这样就不需要通过两个公共的或公司的 IP 地址来部署 ASDK，同时也简化了部署。
- Azure Stack 集成系统备份现在可以通过 **asdk-installer.ps1** PowerShell 脚本进行[验证](asdk-validate-backup.md)。

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](../azure-stack-update-1901.md#new-features)。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 如需此版本中已修复问题的列表，请参阅 Azure Stack 发行说明的[此部分](../azure-stack-update-1901.md#fixed-issues)。 如需已知问题的列表，请参阅[此部分](../azure-stack-update-1901.md#known-issues-post-installation)。
- 请注意，[发布的 Azure Stack 修补程序](../azure-stack-update-1901.md#azure-stack-hotfixes)不适用于 Azure Stack ASDK。

## <a name="build-118110101"></a>内部版本 1.1811.0.101

### <a name="changes"></a>更改

此内部版本包含对 Azure Stack 的以下改进：  

- ASDK 有一套新的最低硬件和软件要求，以及建议的配置。 [Azure Stack 部署规划注意事项](asdk-deploy-considerations.md)中阐述了这些新的建议规范。 由于 Azure Stack 平台已经演进，现在可以使用更多的服务，此外可能需要更多的资源。 增加的规范反映了这些修订的建议。

### <a name="new-features"></a>新增功能

如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](../azure-stack-update-1811.md#new-features)。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

如需此版本中已修复问题的列表，请参阅 Azure Stack 发行说明的[此部分](../azure-stack-update-1811.md#fixed-issues)。 如需已知问题的列表，请参阅[此部分](../azure-stack-update-1811.md#known-issues-post-installation)。
