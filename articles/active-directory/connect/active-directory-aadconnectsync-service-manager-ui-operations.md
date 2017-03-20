---
title: "Azure AD Connect Synchronization Service Manager 操作 | Microsoft 文档"
description: "了解 Azure AD Connect 的 Synchronization Service Manager 中的“操作”选项卡。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 7f50ce0d4842bde809664c392ebee5425a70e6f0
ms.lasthandoff: 03/08/2017

---
# <a name="using-the-sync-service-manager-operations-tab"></a>使用“Sync Service Manager 操作”选项卡

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

“操作”选项卡显示最新操作的结果。 此选项卡是了解和排查问题的关键所在。

## <a name="understand-the-information-visible-in-the-operations-tab"></a>了解“操作”选项卡中显示的信息
上半部分按时间顺序显示所有运行。 默认情况下，操作日志保留最后七天的相关信息，但可以使用[计划程序](active-directory-aadconnectsync-feature-scheduler.md)来更改此设置。 想要查找所有未显示成功状态的运行。 可以单击标题来更改排序。

“状态”列是最重要的信息，并显示最严重的运行问题。 下面是按调查优先级顺序显示的最常见状态的快速摘要（其中 * 表示多个可能的错误字符串）。

| 状态 | 注释 |
| --- | --- |
| stopped-* |运行无法完成。 例如，如果远程系统已关闭且无法访问。 |
| stopped-error-limit |有 5,000 个以上的错误。 运行因错误数量过多而自动停止。 |
| completed-\*-errors |运行已完成，但发生应调查的错误（数量少于 5,000 个）。 |
| completed-\*-warnings |运行已完成，但某些数据并未处于预期的状态。 如果遇到错误，则此消息通常只是一种征兆。 在解决错误之前，不应该调查警告。 |
| 成功 |没有问题。 |

选择某一行时，底部将更新以显示该运行的详细信息。 在底部的最左边，可能会有一份显示“步骤编号”的列表。 仅当林中有多个域，而且每个域都以一个步骤表示时，才会显示此列表。 可以在“分区”标题下方找到域名。 在“同步统计信息”下方，可以找到有关已处理更改次数的详细信息。 可以单击链接获取已更改对象的列表。 如果有对象发生错误，这些错误将显示在“同步错误”下方。

有关详细信息，请参阅[排查对象不同步的问题](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)。

## <a name="next-steps"></a>后续步骤
了解有关 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)配置的详细信息。

了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。

