---
title: 高可用性 - Azure 专用 HSM | Microsoft Docs
description: Azure 专用 HSM 高可用性示例和基本注意事项
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 536ef62acad900090924598edfa45450b2a8c951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70882248"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure 专用 HSM 高可用性

Azure 专用 HSM 由 Microsoft 具有高可用性的数据中心提供支持。 但是，任何具有高可用性的数据中心都容易出现本地化故障，并且在极端情况下会出现区域级故障。 Microsoft 将 HSM 设备部署到区域中的不同数据中心以确保预配多个设备不会导致这些设备共享单个机架。 通过使用金雅拓 HA 组功能，将这些 HSM 在区域内的数据中心进行配对，可以实现更高级别的高可用性。 还可以在区域之间将设备配对以在进行灾难恢复的情况下解决区域性故障转移。 使用此多层高可用性配置时，会自动解决任何设备故障以使应用程序保持正常工作。 所有数据中心的现场还具有备用设备和组件，以便可以及时更换任何失败的设备。

## <a name="high-availability-example"></a>高可用性示例

“Gemalto Luna 网络 HSM 管理指南”中提供了有关如何在软件级别配置 HSM 设备以实现高可用性的信息。 本文档可在[金雅拓 HSM 页面](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)找到。

下图展示了一个具有高可用性的体系结构。 它在区域中使用了多个设备并且在每个单独的区域中使用了多个配对的设备。 此体系结构使用了四 HSM 设备的最低配置和虚拟网络组件。

![高可用性关系图](media/high-availability/high-availability.png)

## <a name="next-steps"></a>后续步骤

建议在进行设备预配和应用程序设计或部署之前，先深刻理解此服务的所有重要概念，例如高可用性和安全性。
更多概念级别的主题：

* [部署体系结构](deployment-architecture.md)
* [物理安全](physical-security.md)
* [网络连接](networking.md)
* [可支持性](supportability.md)
* <bpt i="1000001" x="1000001" type="formatting">{b&gt;</bpt>监视<ept i="1000001">&lt;b}</ept>

有关配置 HSM 设备以实现高可用性的具体详细信息，请参考 Gemalto 客户支持门户上的管理员指南并查看第 6 部分。
