---
title: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 644d58c3d1c60611b0d22d2757da089313fa12b6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423691"
---
在将 RBAC 角色分配到某个安全主体之前，请确定该安全主体应该获取的访问范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述可以限定 Azure blob 和队列资源访问权限范围的等级，从最窄的范围开始：

- **单个容器。** 在此范围内，角色分配将应用于容器中的所有 blob，以及容器属性和元数据。
- **单个队列。** 在此范围内，角色分配将应用于队列中的消息，以及队列属性和元数据。
- **存储帐户。** 在此范围内，角色分配将应用于所有容器及其 blob，或者所有队列及其消息。
- **资源组。** 在此范围内，角色分配适用于资源组中所有存储帐户内的所有容器或队列。
- **订阅。** 在此范围内，角色分配适用于订阅中所有资源组内的所有存储帐户中的所有容器或队列。
- **管理组。** 在此范围内，角色分配将应用于管理组中所有订阅的所有资源组中的所有存储帐户中的所有容器或队列。

有关 Azure 角色分配和范围的详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../articles/role-based-access-control/overview.md)。
