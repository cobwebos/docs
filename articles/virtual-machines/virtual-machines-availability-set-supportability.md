---
title: 将 Azure VM 添加到现有可用性集的可支持性 | Microsoft Docs
description: 本文提供了有关可在同一可用性集中混合的 VM 系列的可支持性矩阵
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: cb0034f2b353284e94d6f1508541b31040a5b076
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028407"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>将 Azure VM 添加到现有可用性集的可支持性

将新的虚拟机 (VM) 添加到现有可用性集时，可能偶尔会遇到限制。 以下图表详细说明了可以在同一可用性集中混合的 VM 系列。

以下是混合不同类型的 VM 的可支持性矩阵：

系列和可用性集|第二个 VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|第一个 VM|||||||
|A||确定|确定|确定|确定|确定|
|Av2||确定|确定|确定|确定|确定|
|D||确定|确定|确定|确定|确定|
|Dv2||确定|确定|确定|确定|确定|
|Dv3||确定|确定|确定|确定|确定|

所有其他系列都不能在同一可用性集中，因为它们需要特定的硬件。

由于需要专用 RDMA 后端网络，因此无法混合 A8/A9 VM 大小。
