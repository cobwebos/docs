---
title: 如何恢复删除的存储帐户
description: 了解如何恢复已删除的存储帐户
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252637"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>如何恢复删除的存储帐户

Azure 存储通过自动化副本提供数据复原能力，但不会阻止用户或应用程序代码破坏数据（不管这些行为是意外发生还是恶意发生）。 在遇到应用程序或用户错误时，维持数据保真度需要更先进的技术，例如使用审核日志将数据复制到辅助存储位置。

下表根据复制策略概述了存储帐户恢复的范围。

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|存储帐户 Azure 资源管理器|是|是|是|是|
|经典存储帐户|是|是|是|是|

收集以下信息，并与 Microsoft 支持部门提交支持请求：

* 存储帐户名称
* 删除时间
* 存储帐户区域
* 如何删除存储帐户？
* 删除存储帐户的方法是什么？ （门户、PowerShell 等）

重要事项

* 通常，从存储服务的删除时间起，最多需要 15 天来执行垃圾回收，因此无法使用 SLA 恢复存储帐户。
* Microsoft 支持将尽力恢复容器/帐户，并且不能保证恢复。

> [!NOTE]
> 如果重新创建了帐户，则恢复可能会失败。 如果已经重新创建了帐户，则必须先将其删除，然后才能尝试进行恢复。
