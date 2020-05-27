---
title: 快速入门：在 LUIS 门户中创建新应用
description: 本快速入门将创建应用的基本组成部分以及意向和实体，并在 LUIS 门户中使用示例言语进行测试。
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 7cf55a7891b7e06c18c80d9d359b19e54f0413a9
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697270"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>快速入门：在 LUIS 门户中创建新应用

本快速入门介绍如何在 LUIS 门户中生成新应用。 首先，创建应用的基本部件、意向和实体 。 然后，通过在交互式测试面板中提供用于获取预测的意向的示例用户话语，对应用进行测试。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>创建应用

1. 从上下文工具栏中选择“+ 新建用于对话的应用”，然后再次选择“+ 新建用于对话的应用” 。

    > [!div class="mx-imgBorder"]
    > [![在 LUIS 门户中创建新应用的屏幕截图](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. 在弹出窗口中使用以下设置配置应用，然后选择“完成”。

   |设置名称| 值 | 目的|
   |--|--|--|
   |名称|`myEnglishApp`|唯一的 LUIS 应用名称<br>必填|
   |环境|**英语**|用户的言语语言：**en-us**<br>必填|
   |说明（可选）|`App made with LUIS Portal`|应用的说明<br>可选|
   |预测资源（可选） |-  |请不要选择。 LUIS 免费提供一个初学者密钥用于创作和发出 1,000 个预测终结点请求。 |

   ![输入新应用设置的屏幕截图](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>创建意向

创建 LUIS 应用后，需创建意向。 意向用于分类用户提供的文本。 例如，人力资源应用可以有两项功能。 帮助用户：

 1. 找工作和申请工作
 1. 查找工作申请表

此应用的两个不同_意向_对应于以下意向：

|Intent|用户提供的文本示例<br>称为“言语”|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

若要创建意向，请完成以下步骤：

1. 创建应用后，会转到“生成”部分的“意向”页。  选择“创建”。

   [![选择“创建”以创建新意向的屏幕截图](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. 输入意向名称 `FindForm`，然后选择“完成”。

## <a name="add-an-example-utterance"></a>添加示例话语

在创建意向后添加示例话语。 示例话语是用户在聊天机器人或其他客户端应用程序中输入的文本。 它们可将用户文本的意向映射到 LUIS 意向。

就此示例应用程序的 `FindForm` 意向来说，示例话语将包含表格编号。 客户端应用程序需要该表格编号来履行用户的请求，因此必须将其包括在话语中。

> [!div class="mx-imgBorder"]
> [![输入 FindForm 意向的示例言语的屏幕截图](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

将以下 15 个示例话语添加到 `FindForm` 意向。

|#|示例陈述|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

根据设计，这些示例话语按以下方式变化：

* 言语长度
* [标点](luis-reference-application-settings.md#punctuation-normalization)
* 选词
* 谓语时态（现在时、过去时和将来时）
* 词序


## <a name="create-a-regular-expression-entity"></a>创建正则表达式实体

若要在运行时预测响应中返回表格编号，必须将表格编号提取为一个实体。 由于表格编号文本已高度结构化，可以使用正则表达式实体。 通过以下步骤创建正则表达式实体：

1. 在左侧菜单中选择“实体”。

1. 在“实体”页上选择“创建”。 

1. 输入名称 `FormNumber`，选择“Regex”实体类型。

1. 输入正则表达式，“Regex”字段中的 `hrf-[0-9]{6}`。 此条目匹配文本字符 `hrf-`，只能包含 6 位数，然后选择“创建”。

    > [!div class="mx-imgBorder"]
    > ![创建正则表达式实体的屏幕截图](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    此实体提取在任何意向中匹配正则表达式的任何文本。

## <a name="add-example-utterances-to-the-none-intent"></a>将话语示例添加到 None 意向

**None** 意向是回退意向，不可留空。 每为应用的其他意向添加 10 个示例话语，此意向就应该包含 1 个话语。

**None** 意向的示例言语应在客户端应用程序域的范围以外。

1. 在左侧菜单中选择“意向”，然后从意向列表中选择“None”。 

1. 将以下示例言语添加到该意向：

   |None 意向示例言语|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   对于此应用，这些示例言语在域的范围以外。 如果域包括动物或海洋，则应将其他示例言语用于 None 意向。

## <a name="train-the-app"></a>训练应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>在示例言语中查找正则表达式实体

1. 在左侧菜单中选择“意向”，以验证是否能够在“FindForm”意向中找到该实体。  然后，选择“FindForm”意向。

   会标记该实体在示例言语中的显示位置。

   > [!div class="mx-imgBorder"]
   > [![标记有实体的所有示例言语的屏幕截图](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>使用交互式测试窗格测试新应用

使用 LUIS 门户中的交互式“测试”窗格可以验证实体是否是从应用尚未看到的新话语中提取的。

1. 在右上方菜单中选择“测试”。

1. 添加新话语，然后按 Enter：

   ```Is there a form named hrf-234098```

    选择“检查”以查看实体预测。

   > [!div class="mx-imgBorder"]
   > ![在测试窗格中测试新言语的屏幕截图](./media/get-started-portal-build-app/test-new-utterance.png)

   排名靠前的预测意向为 **FindForm**（正确），其置信度超过 90% (0.977)。 已提取“FormNumber”实体，其值为 hrf-234098。

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，如果不继续学习下一快速入门，请在顶部导航菜单中选择“我的应用”。 接着在列表中选中应用左侧的复选框，然后在列表上方的上下文工具栏中选择“删除”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [2.部署应用](get-started-portal-deploy-app.md)
