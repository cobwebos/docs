---
title: Azure SAP HANA（大型实例）操作系统升级 | Microsoft Docs
description: 执行 Azure SAP HANA（大型实例）操作系统升级
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff9be3b074dde4a0335675663133a8df81ae62d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114586"
---
# <a name="operating-system-upgrade"></a>操作系统升级
本文档介绍 HANA 大型实例操作系统升级的详细信息。

>[!NOTE]
>操作系统升级是客户的责任，Microsoft 运营支持团队可指导你发现升级过程中需要留意的重点领域。 在计划升级前，你还应咨询操作系统供应商。

Microsoft 运营团队在预配 HLI 单元时安装操作系统。 随着时间推移，你需要维护 HLI 单元的操作系统（例如，修补、优化、升级等）。

在对操作系统进行重大更改（例如，将 SP1 升级到 SP2）之前，必须通过开具支持票证进行咨询来与 Microsoft 运营团队联系。


有关不同 Linux 版本的不同 SAP HANA 版本的支持矩阵，请参阅 [SAP 说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。


## <a name="known-issues"></a>已知问题

以下是升级过程中几个常见的已知问题：
- 在 II 类 SKU 上，Software Foundation Software (SFS) 会在操作系统升级后移除。 必须在操作系统升级后重新安装兼容的 SFS。
- 以太网卡驱动程序（ENIC 和 FNIC）会回滚到旧版本。 必须在升级后重新安装兼容版本的驱动程序。

## <a name="next-steps"></a>后续步骤
- 有关 I 类 SKU 操作系统备份的信息，请参阅[备份和恢复](hana-overview-high-availability-disaster-recovery.md)。
- 有关 II 类 SKU 的信息，请参阅 [II 类 SKU 操作系统备份](os-backup-type-ii-skus.md)。