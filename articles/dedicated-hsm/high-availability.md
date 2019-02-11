---
title: 高可用性 - Azure 专用 HSM | Microsoft Docs
description: Azure 专用 HSM 高可用性示例和基本注意事项
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 991482d3d6be4d09b37014e5bb03708987e0f74e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078168"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure 专用 HSM 高可用性

Azure 专用 HSM 由 Microsoft 具有高可用性的数据中心提供支持。 但是，任何具有高可用性的数据中心都容易出现本地化故障，并且在极端情况下会出现区域级故障。 Microsoft 将 HSM 设备部署到区域中的不同数据中心以确保预配多个设备不会导致这些设备共享单个机架。 可以通过在区域中的数据中心之间将这些 HSM 配对来实现更高的高可用性。 还可以在区域之间将设备配对以在进行灾难恢复的情况下解决区域性故障转移。 使用此多层高可用性配置时，会自动解决任何设备故障以使应用程序保持正常工作。 所有数据中心的现场还具有备用设备和组件，以便可以及时更换任何失败的设备。

## <a name="high-availability-example"></a>高可用性示例

“Gemalto Luna 网络 HSM 管理指南”中提供了有关如何在软件级别配置 HSM 设备以实现高可用性的信息。 此文档位于 [Gemalto 客户支持门户](https://supportportal.gemalto.com/csm/)上。

下图展示了一个具有高可用性的体系结构。 它在区域中使用了多个设备并且在每个单独的区域中使用了多个配对的设备。 此体系结构使用了四 HSM 设备的最低配置和虚拟网络组件。

![高可用性关系图](media/high-availability/high-availability.png)

## <a name="next-steps"></a>后续步骤

建议在进行设备预配和应用程序设计或部署之前，先深刻理解此服务的所有重要概念，例如高可用性和安全性。
更多概念级别的主题：

* [部署体系结构](deployment-architecture.md)
* [物理安全性](physical-security.md)
* [网络](networking.md)
* [可支持性](supportability.md)
* [监视](monitoring.md)

有关配置 HSM 设备以实现高可用性的具体详细信息，请参考 Gemalto 客户支持门户上的管理员指南并查看第 6 部分。
