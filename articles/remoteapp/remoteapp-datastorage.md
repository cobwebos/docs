---
title: "永远不要将敏感数据存储到 Azure RemoteApp 的自定义映像上 |Microsoft Docs"
description: "了解关于如何在 Azure RemoteApp 的自定义映像中存储数据的指导"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 5a19903b-15f9-49d9-9bc1-ae80f2671aa1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 75d5415d33324d957617426e75909a6c6c58b1f9
ms.lasthandoff: 03/31/2017


---
# <a name="never-store-sensitive-data-on-custom-images"></a>永远不要将敏感数据存储在自定义映像上
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

在 Azure RemoteApp 中托管你自己的应用程序时，第一步是创建自定义映像。 我们会使用自定义映像来创建 VM 实例，向你的用户提供你的应用。 自定义映像应该仅包含应用程序，永远不要包含可能会丢失的敏感数据，比如 SQL 数据库、人事档案或像 QuickBooks 公司文件那样的特殊数据文件。 所有敏感数据应放置位于 Azure RemoteApp 外部的文件服务器上，可以是另一个 Azure VM，或在 SQL Azure 中。 该映像应仅承载连接到数据源并呈现数据的应用程序。 有关详细信息，请查看 [Azure RemoteApp 映像的要求](remoteapp-imagereqs.md)。 

要了解为什么不应存储敏感数据，你需要知道 Azure RemoteApp 的工作原理。 在创建或更新集合时，后台会创建映像的多个克隆或副本。 所有这些 VM 实例都是和自定义映像完全一样的副本；当用户启动应用程序时，他们就会连接到这些 VM 执行其中的一个。 但不保证是同一个实例，这也不该有什么影响，因为它们是非持久的。 承载应用程序的 VM 实例是非持久的，可能会被毁坏或被删除，例如，在集合更新过程中。 

集合进行配置且用户开始连接到 VM 之后，用户数据就是持久的并受到保护，因为它保存在 VHD 内的单独存储空间中，我们将该存储空间称为[用户配置文件磁盘 (UPD)](remoteapp-upd.md)，它是位于 c:\users\<userprofile> 中的用户配置文件。 应用程序启动时，就会装载 UPD，并且操作系统会将其视同本地用户配置文件进行处理。 了解 [Azure RemoteApp 如何保存用户数据和设置](remoteapp-upd.md)。

不应驻留在映像中的示例数据︰

* 供用户访问的共享数据
* SQL DB 或 QuickBooks DB
* D:\ 中的任何数据

可以位于默认配置文件，要复制到每个用户 UPD 的示例数据︰

* 每用户配置文件
* 用户需要保留在其 UPD 中的脚本

要点：

* 创建自定义映像时，永远不要存储可能会在映像上丢失的敏感数据。
* 敏感数据应该始终驻留在云中的一个单独文件服务器上、单独的 Azure VM 上，且始终位于 Azure RemoteApp 内承载你的应用程序的 VM 实例的外部。 
* 用户数据保存并保持在用户配置文件磁盘 (UPD) 中


