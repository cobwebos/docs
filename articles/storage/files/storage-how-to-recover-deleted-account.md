---
title: 将 Azure 文件共享用于 Azure 存储 |Microsoft Docs
description: 了解如何将 Azure 文件共享与 Windows 和 Windows Server 配合使用。
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: e1e581134b4e3821659c43bb7f55003239594bd9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233787"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>如何恢复已删除的存储帐户

Azure 存储通过自动化副本提供数据复原功能，但不会阻止用户或应用程序代码破坏数据（不管是意外还是恶意）。 在应用程序或用户错误的实例中维护数据保真度需要更高级的技术，如使用审核日志将数据复制到辅助存储位置。

下表概述了存储帐户恢复的范围，具体取决于复制策略。

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Azure 资源管理器的存储帐户|是|是|是|是|
|经典存储帐户|是|是|是|是|

收集以下信息，并向 Microsoft 支持部门提供支持请求：

* 存储帐户名称
* 删除日期
* 存储帐户区域
* 如何删除存储帐户？
* 什么方法删除了存储帐户？ （门户、PowerShell 等）

要点

* 通常，从存储服务的删除时间起，最多需要15天才能执行垃圾回收，因此无法使用 SLA 恢复存储帐户恢复。
* Microsoft 支持部门将尽力尽力恢复容器/帐户，且无法保证恢复。

> [!NOTE]
> 如果重新创建了帐户，则恢复可能失败。 如果你已经重新创建了该帐户，则必须先将其删除，然后才能尝试恢复。
