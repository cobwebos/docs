---
title: "更新 Azure RemoteApp 集合 | Microsoft Docs"
description: "了解如何更新 Azure RemoteApp 集合"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: e553d432-e581-48fe-b996-c432357eb64a
ms.service: remoteapp
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: compute
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 41c8a13ebd008ed4f9d8a5399bf8e272bf0fd7b2
ms.lasthandoff: 03/31/2017


---
# <a name="update-a-collection-in-azure-remoteapp"></a>在 Azure RemoteApp 中更新集合
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

总会有一天，你必然会需要更新你的 Azure RemoteApp 集合中的应用或映像。 如果你正在云集合或混合集合中使用随你的 Azure RemoteApp 订阅提供的其中一个映像，那么 Azure RemoteApp 本身就会处理所有更新，这样你就可以高枕无忧了。

但是，如果你使用自定义映像（无论是从头开始构建的，还是通过修改我们的其中一个映像来创建的），则要负责维护映像和应用。 如果你需要更新映像或其中的任何应用，则需要创建映像的一个新的更新版本，然后将集合中的现有映像替换为这个新的更新映像。

因此，你打算如何更新你的集合？ 这非常简单︰

1. 更新在你的集合中使用的映像。 根据需要应用任何修补程序或更新，然后使用一个新名称保存它。
2. 将映像[上载](remoteapp-uploadimage.md)或[导入](remoteapp-image-on-azurevm.md)到 RemoteApp。
3. 现在，在集合页上，单击“**更新**”。
4. 从“**模板映像**”列表中选择新映像。
5. 以下是最为棘手的部分 - 你需要决定如何处理当前正在使用集合中应用的任何用户。 可以使用以下选择：
   
   * **更新后给用户 60 分钟时间**。 只要完成更新，Azure RemoteApp 就会向所有活动用户显示一条消息，通知他们保存其工作，注销，然后再次登录。 在 60 分钟后尚未注销的任何活动用户将被自动注销。 用户可以立即再次登录。
   * **立即注销用户**。 只要完成更新，就会自动注销所有用户，而不会显示任何警告。 如果选择此选项，用户可能会丢失数据。 但是，他们可以立即重新连接到应用。
6. 请单击复选标记开始更新。


