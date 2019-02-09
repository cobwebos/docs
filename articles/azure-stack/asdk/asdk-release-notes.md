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
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 35a3850cdda7f6844258439b74b29dcc20852a89
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959589"
---
# <a name="asdk-release-notes"></a>ASDK 发行说明

本文提供有关更改、 修复和已知的问题在 Azure Stack 开发工具包 (ASDK) 的信息。 如果不确定所运行的版本，可以[使用门户检查版本](../azure-stack-updates.md#determine-the-current-version)。

请订阅 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [源](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，随时了解 ASDK 的新增功能。

## <a name="build-11901095"></a>生成 1.1901.0.95

### <a name="changes"></a>更改

此版本包括了适用于 Azure Stack 的以下改进：

- 现在，BGP 和 NAT 组件将部署在物理主机上。 这消除了需要有两个部署 ASDK 中，所需的公共或公司 IP 地址，并还简化了部署过程。
- Azure Stack 集成的系统备份现在可以[验证](asdk-validate-backup.md)使用**asdk-installer.ps1** PowerShell 脚本。

### <a name="new-features"></a>新增功能

- 有关此版本中的新功能的列表，请参阅[本节](../azure-stack-update-1901.md#new-features)Azure Stack 的发行说明。

### <a name="fixed-and-known-issues"></a>固定和已知问题

- 在此版本中已修复的问题的列表，请参阅[本节](../azure-stack-update-1901.md#fixed-issues)Azure Stack 的发行说明。 有关已知问题的列表，请参阅[本节](../azure-stack-update-1901.md#known-issues-post-installation)。
- 请注意，[可用的 Azure Stack 修补程序](../azure-stack-update-1901.md#azure-stack-hotfixes)不适用于 Azure Stack ASDK。

## <a name="build-118110101"></a>内部版本 1.1811.0.101

### <a name="changes"></a>更改

此版本包括了适用于 Azure Stack 的以下改进：  

- ASDK 有一套新的最低硬件和软件要求，以及建议的配置。 [Azure Stack 部署规划注意事项](asdk-deploy-considerations.md)中阐述了这些新的建议规范。 由于 Azure Stack 平台已经演进，现在可以使用更多的服务，此外可能需要更多的资源。 增加的规范反映了这些修订的建议。

### <a name="new-features"></a>新增功能

有关此版本中的新功能的列表，请参阅[本节](../azure-stack-update-1811.md#new-features)Azure Stack 的发行说明。

### <a name="fixed-and-known-issues"></a>固定和已知问题

在此版本中已修复的问题的列表，请参阅[本节](../azure-stack-update-1811.md#fixed-issues)Azure Stack 的发行说明。 有关已知问题的列表，请参阅[本节](../azure-stack-update-1811.md#known-issues-post-installation)。

## <a name="build-11809090"></a>内部版本 1.1809.0.90

### <a name="new-features"></a>新增功能

有关此版本中的新功能的列表，请参阅[本节](../azure-stack-update-1809.md#new-features)Azure Stack 的发行说明。

### <a name="fixed-issues"></a>修复的问题

在此版本中已修复的问题的列表，请参阅[本节](../azure-stack-update-1809.md#fixed-issues)。

### <a name="known-issues"></a>已知问题

在此版本中的已知问题的列表，请参阅[本节](../azure-stack-update-1809.md#known-issues-post-installation)。
