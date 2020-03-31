---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597766"
---
Azure 文件提供两个不同的存储层（高级存储层和标准层），允许您根据方案的性能和价格要求定制共享：

- **高级文件共享**：高级文件共享由固态驱动器 （SSD） 支持，并部署在**文件存储存储帐户**类型中。 高级文件共享提供稳定的高性能和低延迟，对于大多数 IO 操作和 IO 密集型工作负荷，延迟不到 10 毫秒。 这使得它们适合于各种各样的工作负荷，例如数据库、网站托管和开发环境。 高级文件共享只能在预配的计费模型下使用。 有关高级文件共享的预配计费模型的详细信息，请参阅[了解高级文件共享的预配](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)。
- **标准文件共享**：标准文件共享由硬盘驱动器 （HDD） 支持，并部署在**通用版本 2 （GPv2） 存储帐户**类型中。 标准文件共享为对性能波动不太敏感的 IO 工作负荷（例如，常规用途文件共享和开发/测试环境）提供可靠的性能。 标准文件共享只能在即用即付计费模型下使用。