---
title: Azure SAP HANA（大型实例）操作系统升级 | Microsoft Docs
description: 执行 Azure SAP HANA（大型实例）操作系统升级
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1354b67ade78a0da1578ef9d98c3b1e8edb41cd4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077742"
---
# <a name="operating-system-upgrade"></a>操作系统升级
本文档介绍 HANA 大型实例操作系统升级的详细信息。

>[!NOTE]
>操作系统升级是客户的责任，Microsoft 运营支持团队可指导你发现升级过程中需要留意的重点领域。 在计划升级前，你还应咨询操作系统供应商。

Microsoft 运营团队在预配 HLI 单元时安装操作系统。 随着时间推移，你需要维护 HLI 单元的操作系统（例如，修补、优化、升级等）。

在对操作系统进行重大更改（例如，升级操作系统）前，你**必须**考虑以下兼容性对照表。 你还**必须**在启动重大操作系统活动（如升级）前，通过打开支持票证进行咨询来与 Microsoft 运营团队联系。

有关不同 Linux 版本的不同 SAP HANA 版本的支持矩阵，请参阅 [SAP 说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。

以下兼容性已针对 HLI 进行测试。 如果你的 HLI 服务器不在兼容性对照表中，请联系 Microsoft 运营支持团队。

## <a name="for-type-i-class-sku-category"></a>对于 I 类 SKU 类别

| 配置 | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| 服务器固件 | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| ENIC 版本 | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| FNIC 版本 | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | 已禁用 | 已禁用 | 已禁用 | 已禁用 |
| 内核版本 | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.x86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>对于 II 类 SKU 类别

| 配置 | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| RMC 固件版本 | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| BMC 固件版本 | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Software Foundation Server (SFS) 版本 | 2.16    | 2.16    | 2.14/2.16   | 2.16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| i40e 版本    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| 内核版本    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>已知问题

以下是升级过程中几个常见的已知问题：
- 在 II 类 SKU 上，Software Foundation Software (SFS) 会在操作系统升级后移除。 必须在操作系统升级后重新安装兼容的 SFS。
- 以太网卡驱动程序（ENIC 和 FNIC）会回滚到旧版本。 必须在升级后重新安装兼容版本的驱动程序。

## <a name="next-steps"></a>后续步骤
- 有关 I 类 SKU 操作系统备份的信息，请参阅[备份和恢复](hana-overview-high-availability-disaster-recovery.md)。
- 有关 II 类 SKU 的信息，请参阅 [II 类 SKU 操作系统备份](os-backup-type-ii-skus.md)。