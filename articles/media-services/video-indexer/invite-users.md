---
title: 邀请用户加入视频索引器-Azure
titlesuffix: Azure Media Services
description: 本文介绍如何将用户邀请到视频索引器。
services: media-services
author: ReutAmior
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: 740b03179fad91e9042a54f8e74a622b3125b832
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950182"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>快速入门：邀请用户加入视频索引器

若要与同事进行协作，可以邀请他们加入视频索引器帐户。 

> [!NOTE]
> 只有帐户管理员可以添加或删除用户。

## <a name="invite-new-users"></a>邀请新用户

1. 登录到[视频索引器](https://www.videoindexer.ai/)网站。 请确保已使用管理员帐户进行连接。
1. 单击顶部菜单上的 "**邀请其他人**" 按钮：

   ![邀请新用户](./media/invite-users/invite-users.png)

1. 添加要添加到视频索引器帐户的人员的电子邮件地址：

    ![邀请用户加入此帐户](./media/invite-users/invite-to-account.png)
        
    >[!NOTE]
    > 你邀请的所有用户将对你帐户中的所有视频具有 "读取" 和 "写入" 权限。
1. 你邀请的用户将收到一封包含链接的电子邮件，一旦单击 "**加入视频索引器**" 链接，就可以访问该帐户：

    ![确认](./media/invite-users/invite-msg.png)

    用户必须单击 "加入" 链接才能获取对该帐户的访问权限。 

## <a name="removing-existing-users"></a>删除现有用户

如果要删除有权访问帐户的用户，可以单击其名称旁边的**X**符号：

![删除用户](./media/invite-users/remove-users.png)

删除时，用户不会收到通知。 删除后，用户将无法登录。

## <a name="next-steps"></a>后续步骤

你现在可以使用[视频索引器网站](video-indexer-view-edit.md)或[视频索引器开发人员门户](video-indexer-use-apis.md)来查看视频见解。

## <a name="see-also"></a>请参阅

- [视频索引器概述](video-indexer-overview.md)
- [如何注册并上传第一个视频](video-indexer-get-started.md)
- [开始使用 Api](video-indexer-use-apis.md)
