---
title: 快速入门：使用 LUIS 门户创建新应用
titleSuffix: Language Understanding - Azure Cognitive Services
description: 本快速入门介绍如何使用 LUIS 门户创建新应用。 我们将创建应用的基本部件、意向和实体，然后通过在交互式测试面板提供示例用户言语获取预测的意向，以进行测试。 生成应用是免费的操作，不需要 Azure 订阅。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783166"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>快速入门：在 LUIS 门户中创建新应用

本快速入门将在 [LUIS 门户](https://www.luis.ai)中创建新应用。 我们将创建应用的基本部件、**意向**和**实体**，然后通过在交互式测试面板提供示例用户言语获取预测的意向，以进行测试。

生成应用是免费的操作，不需要 Azure 订阅。 准备好部署应用后，可以创建 Azure 认知服务资源并将其分配到应用。 此部署过程将在[下一篇快速入门](get-started-portal-deploy-app.md)中予以介绍。

## <a name="create-app"></a>创建应用 

1. 在浏览器中打开 [LUIS 门户](https://www.luis.ai)并登录。 首次登录时，需要创建一个免费的 LUIS 门户用户帐户。

1. 在上下文工具栏中选择“创建新应用”。

    [![在 LUIS 门户中创建新应用](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. 在弹出窗口中，使用以下设置配置应用，然后选择“完成”。

    |设置名称| 值 | 目的|
    |--|--|--|
    |Name|`myEnglishApp`|唯一的 LUIS 应用名称<br>必填|
    |环境|**英语**|用户的言语语言：**en-us**<br>必填|
    |说明|`App made with LUIS Portal`|应用的说明<br>可选|

    ![输入新应用设置](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>创建意向 

创建此应用后，下一步是创建意向。 意向用于分类用户提供的文本。 如果某个人力资源应用具有两项功能：第一项功能是帮助人们查找和申请职位，第二项功能是查找职位申请表，那么，这两个不同的意向对应于以下意向：

|意向|用户提供的文本示例<br>称为“言语”|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. 创建应用后，会转到“生成”部分的“意向”页。 选择“创建新意向”。 

    [![选择“创建新意向”按钮](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. 输入意向名称 `FindForm`，然后选择“完成”。

    ![输入意向名称 FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>添加示例言语 

创建意向后，下一步是添加示例言语。 这是用户在聊天机器人或其他客户端应用程序中输入的文本，该应用程序可将用户文本中的意向映射到 LUIS 意向。 

对于此示例应用程序的 `FindForm` 意向，示例言语将包含表格编号，这是言语中的重要信息，客户端应用程序需要获取此信息才能实现用户的请求。 

将以下 15 个示例言语添加到 `FindForm` 意向。 

|#|示例陈述|
|--|--|
|1|正在查找 hrf-123456|
|2|人力资源表格 hrf-234591 在哪里？|
|3|hrf-345623 在哪里？|
|4|是否可以向我发送 hrf-345794|
|5|我是否需要使用 hrf-234695 来申请内部职位？|
|6|我的经理是否需要知道我正在使用 hrf-234091 申请职位|
|7|要将 hrf-234918 发送到何处？ 对方收到此表格后，我是否会收到电子邮件回复？|
|8|hrf-234555|
|9|hrf-234987 是何时更新的？|
|10|我是否要使用表格 hrf-876345 申请工程职位|
|11|是否为我的开放请求提交了 hrf-765234 的新版本？|
|12|我是否要使用 hrf-234234 申请国际职位？|
|13|hrf-234598 拼写错误|
|14|是否会根据新要求编辑 hrf-234567|
|15|hrf-123456、hrf-123123、hrf-234567|

这些示例言语根据以下因素有意做了改变：

* 言语长度
* 标点
* 选词
* 谓语时态（现在时、过去时和将来时）
* 词序

[![输入 FindForm 意向的示例言语](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>创建正则表达式实体 

若要获取作为运行时预测响应的一部分返回的表格编号，必须将该表格标记为实体。 由于表格编号文本已高度结构化，可以使用正则表达式实体来标记它。 使用以下步骤创建该实体。 

1. 在左侧导航菜单中选择“实体”。 

1. 在“实体”页上选择“创建新实体”。

1. 输入名称 `Human Resources Form Number`，选择“正则表达式”实体类型，然后输入正则表达式 `hrf-[0-9]{6}`。 此表达式匹配文本字符 `hrf-`，允许对刚好 6 位数进行匹配。 

    ![输入正则表达式实体的实体信息](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. 选择“完成”。 

## <a name="add-example-utterances-to-none-intent"></a>将示例言语添加到 None 意向

**None** 意向是回退意向，不可留空。 此意图应该为应用剩余意向中的每 10 个意向保留 1 个言语。 

**None** 意向的示例言语应在客户端应用程序域的范围以外。 

1. 在左侧菜单中选择“意向”，然后从“意向”列表中选择“None”。

1. 将以下示例言语添加到该意向：

    |None 意向示例言语|
    |--|
    |Barking dogs are annoying|
    |Order a pizza for me|
    |Penguins in the ocean|

    对于此人力资源应用，这些示例言语在域的范围以外。 如果人力资源域包括动物、食物或海洋，则不应将这些示例言语用于 **None** 意向。 

## <a name="train-the-app"></a>训练应用

在右上方的导航菜单中，选择“训练”以将意向和实体模型更改应用到当前版本的应用。 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>在示例言语中查找正则表达式实体

1. 在左侧菜单中选择“意向”，然后选择“FindForm”意向，以验证是否能够在“FindForm”意向中找到该实体。 

    会标记该实体在示例言语中的显示位置。 若要查看原始文本而不是实体名称，请在工具栏中切换“实体视图”。

    [![标记有实体的所有示例言语](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>使用交互式测试窗格测试新应用

使用 LUIS 门户中的交互式“测试”窗格可以验证实体是否是从应用尚未看到的新言语中提取的。

1. 在右上方菜单中选择“测试”。

1. 添加新言语并按 Enter：

    ```Is there a form named hrf-234098```

    ![在测试窗格中测试新言语](./media/get-started-portal-build-app/test-new-utterance.png)

    顶部预测的意向正确 (**FindForm**)，其置信度超过 90% (0.977)；已提取“人力资源表格编号”，其值为 hrf-234098。 

## <a name="clean-up-resources"></a>清理资源
完成本快速入门后，如果你不打算继续学习下一篇快速入门，请在顶部导航菜单中选择“我的应用”。 在列表中选中应用左侧的复选框，然后在列表上方的上下文工具栏中选择“删除”。 

[![从“我的应用”列表中删除应用](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [2.部署应用](get-started-portal-deploy-app.md)