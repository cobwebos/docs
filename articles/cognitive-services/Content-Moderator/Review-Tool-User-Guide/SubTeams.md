---
title: 内容审查器 API 中的团队和子团队 | Microsoft Docs
description: 了解如何使用认知服务内容审查器 API 中的团队和子团队。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: 161c7cd8bac07d5ffc138297d98a40317a8d88fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365447"
---
# <a name="team-and-subteams"></a>团队和子团队 #

必须先创建团队，然后才能使用内容审查器。 注册并命名团队后，此团队就会成为默认团队。 “审阅”工具中只能有一个团队。 不过，可以创建多个子团队。 子团队适用于创建呈报团队或专门负责审阅特定类别内容的团队。 例如，建议将成人内容发送到其他团队，以供执行更多审阅。

本主题介绍了如何创建子团队，并便捷地分配审阅。 不过，可以使用[工作流](workflows.md)根据特定条件分配审阅。

## <a name="go-to-the-teams-setting"></a>转到“团队”设置 ##

若要开始创建子团队，请选择“设置”下的“团队”选项。

![“团队”设置](images/0-teams-1.png)

## <a name="create-subteams"></a>创建子团队 ##

默认团队包含所有可能的审阅者；子团队是默认团队的子集。 无法将尚不是默认团队成员的审阅者分配到子团队，因此现在需要将所有审阅者添加到默认团队。 单击“团队”页上的“邀请”按钮。

![邀请用户](images/invite-users.png)

### <a name="1-create-a-subteam"></a>1.创建子团队。
在“团队”页上，向下滚动到“子团队”部分。 单击“添加子团队”按钮。 

![添加子团队](images/1-teams-1.png)

### <a name="2-name-your-subteam"></a>2.命名子团队。
在对话框中输入子团队名称，再单击“保存”。

![子团队名称](images/1-Teams-2.PNG)

### <a name="3-assign-members-from-your-default-team"></a>3.分配默认团队中的成员。
单击“添加成员”按钮可以将默认团队中的成员分配到一个或多个子团队。 只能将现有用户添加到子团队。 若要添加“审阅”工具中没有的新用户，请使用“团队”设置页上的“邀请”按钮邀请用户。

![分配子团队成员](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>向子团队分配审阅 ##

创建子团队并分配团队成员后，便能开始向这些子团队分配图像和文本审阅。 这是在“审阅”窗口中完成。
若要向子团队分配单张图像，请单击图像右上角的省略号，再依次选择“移到”和子团队。

![向子团队分配图像审阅](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>切换子团队以审阅分配的内容 ##

若为一个或多个子团队的成员，可以在“审阅”工具仪表板中切换这些子团队。 若要查看子团队当前所有的待处理审阅，请选择“图像”选项卡中的“选择子团队”。

![切换子团队](images/3-review-image-subteam-2.png)
