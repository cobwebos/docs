---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7d0286b63703c165dda6cd12bb625fc64272aac1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91376358"
---
Azure 文件存储提供了四种不同的存储层（高级、事务优化、热和冷存储层），因此你能够根据方案的性能和价格要求定制共享：

- **Premium**：高级文件共享由固态硬盘 (SSD) 支持，并部署在 FileStorage 存储帐户类型中。 高级文件共享提供稳定的高性能和低延迟，对于大多数 IO 操作和 IO 密集型工作负荷，延迟不到 10 毫秒。 高级文件共享适合各种各样的工作负载，例如数据库、网站托管和开发环境。 高级文件共享既可用于服务器消息块 (SMB) 协议，也可用于网络文件系统 (NFS) 协议。
- **事务优化**：事务优化文件共享可实现事务繁重的工作负载，这些工作负荷不需要高级文件共享提供的延迟。 事务优化文件共享在硬盘驱动器 (HDD) 支持的标准存储硬件上提供，并部署在常规用途版本 2 (GPv2) 存储帐户类型中。 虽然事务优化一直被称为“标准”层，但这指的是存储介质类型而不是层本身（热和冷存储层也是“标准”层，因为它们位于标准存储硬件上）。
- **Hot**：热文件共享提供针对常规用途文件共享方案（如团队共享和 Azure 文件同步）优化的存储。热文件共享在 HDD 支持的标准存储硬件上提供，并部署在常规用途版本 2 (GPv2) 存储帐户类型中。
- **Cool**：冷文件共享提供针对在线存档存储方案优化的经济高效的存储。 Azure 文件同步可能也适用于低改动工作负载。 冷文件共享在 HDD 支持的标准存储硬件上提供，并部署在常规用途版本 2 (GPv2) 存储帐户类型中。

高级文件共享只能在预配的计费模型下使用。 有关高级文件共享的预配计费模型的详细信息，请参阅[了解预配高级文件共享](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)。 标准文件共享（包括事务优化、热和冷文件共享）以即用即付计费的形式提供。

热文件共享和冷文件共享在所有 Azure 公共区域和 Azure 政府区域中均可用。 事务优化文件共享在所有 Azure 区域中可用，包括 Azure 中国和 Azure 德国区域。

> [!Important]  
> 可以在 GPv2 存储帐户类型中的各层（事务优化、热和冷）之间移动文件共享。 共享在层间移动会产生事务：从较热层移动到较冷层会导致对共享中每个文件收取冷层的写入事务费用，而从较冷层移动到较热层会导致对共享中每个文件收取冷层的读取事务费用。