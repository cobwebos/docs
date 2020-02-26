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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597766"
---
Azure 文件提供了两个不同的存储层（高级和标准），可让你根据方案的性能和价格要求定制你的共享：

- **高级文件共享**：高级文件共享以固态硬盘（ssd）为后盾，并部署在**FileStorage 存储帐户**类型中。 对于 IO 密集型工作负荷，高级文件共享为大多数 IO 操作提供持续的高性能和低延迟。 这使得它们适用于各种工作负荷，如数据库、网站托管和开发环境。 高级文件共享只能在预配的计费模型下使用。 有关预配高级文件共享的计费模型的详细信息，请参阅[了解高级文件共享的预配](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)。
- **标准文件共享**：标准文件共享受硬盘驱动器（hdd）的支持，并部署在**常规用途版本2（GPv2）存储帐户**类型中。 标准文件共享为 IO 工作负荷提供可靠的性能，这些工作负荷对性能变化（如一般用途文件共享和开发/测试环境）不敏感。 标准文件共享只能在即用即付计费模型下使用。