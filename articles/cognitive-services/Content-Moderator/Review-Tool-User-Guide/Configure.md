---
title: 配置内容审查器的“审阅”工具设置 | Microsoft Docs
description: 配置或获取团队、标记、连接器、工作流和凭据。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: a3432a1d8f424fbe78570f47b774c6e7942e16b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365436"
---
# <a name="about-review-tool-settings"></a>关于“审阅”工具设置 #

使用“审阅”工具仪表板上的“设置”选项卡，可以轻松定义和更改许多组件。

![内容审查器“审阅”设置](images/settings-1.png)

## <a name="team-and-subteams"></a>团队和子团队 ## 

在此选项卡中，可以管理团队和子团队。虽然只能有一个团队，但可以[创建多个子团队](subteams.md)，并向未来潜在成员发送邀请。 发出邀请后，可以监视邀请、更改团队成员权限，并能邀请其他用户。 在团队成员接受邀请后，可以将这些成员分配到不同的子团队。 可以将团队成员角色设置为管理员或审阅者：管理员能够邀请其他用户，而审阅者则不能。

![内容审查器“团队”设置](images/settings-2-team.png)

## <a name="tags"></a>标记 ##

在此选项卡中，可以输入标记的短代码、名称和说明，从而[定义自定义标记](tags.md)。 创建标记后，便可以在审阅期间使用它。 通过选中和取消选中“可见”，可以启用和禁用对各个审阅使用不同的标记。

![内容审查器“标记”设置](images/settings-3-tags.png)

## <a name="connectors"></a>连接器 ##

工作流使用连接器与“审阅”工具进行通信，从而增强功能。 “审阅”工具使用用于审查内容的默认工作流来调用内容审查器 API。 注册“审阅”工具时，它会为你自动预配审查器 API 凭据。 它还支持集成其他连接器 API，只要连接器可用即可。 我们提供了一些现成连接器。

在[“连接器”选项卡](connectors.md)中，可以管理连接器。 可以添加或删除连接器，并能查找特定连接器的订阅密钥。 单击“连接”可以将这些连接器添加到自定义工作流中。 

![内容审查器“连接器”设置](images/settings-4-connectors.png)

## <a name="workflows"></a>工作流 ##

在“工作流”选项卡中管理工作流。可以上传示例文件来测试工作流。 还可以使用（“连接器”选项卡中的）可用 API 连接器，为图像和文本[定义自定义工作流](workflows.md)。 

![内容审查器“工作流”设置](images/settings-5-workflows.png)

## <a name="credentials"></a>凭据 ##

在此选项卡中，可以快速访问内容审查器的订阅密钥，这需要使用内容审查器随附的 API（图像审查、文本审查、列表管理、工作流和审阅 API）。
 
![内容审查器“凭据”设置](images/settings-6-credentials.png)
