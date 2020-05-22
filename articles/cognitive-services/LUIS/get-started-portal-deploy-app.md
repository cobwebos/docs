---
title: 快速入门：通过 LUIS 门户部署应用
description: 本快速入门介绍如何通过创建预测终结点资源、分配资源、训练和发布应用来部署应用。
ms.topic: quickstart
ms.date: 05/06/2020
ms.openlocfilehash: 9b22f1eda822b5354f0b434a04c2ea03e4c0ff2a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585044"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>快速入门：在 LUIS 门户中部署应用

LUIS 应用准备好将话语预测返回到客户端应用程序（例如聊天机器人）后，就需要将应用部署到预测终结点。

本快速入门介绍如何部署应用程序。 此外还介绍如何创建预测终结点资源、如何将资源分配给应用、如何训练应用以及如何发布应用。

## <a name="prerequisites"></a>先决条件

* 获取 [Azure 订阅](https://azure.microsoft.com/free)。
* 完成[上述门户快速入门](get-started-portal-build-app.md)或[下载并导入应用](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json)。
* 如果应用将 Azure 资源身份验证的日期提前，则[迁移到 Azure 资源](luis-migration-authoring.md)。 电子邮件身份验证生效时，某些门户页面的外观会有所不同。

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>将资源关键字分配给 LUIS 门户中的 LUIS 应用

每次为 LUIS 创建新的创作或查询预测资源时，都需要将资源分配给 LUIS 应用。 分配后，除非创建新资源，否则无需再次执行此步骤。 可以创建新资源来扩展应用的区域或支持更多预测查询。

1. 登录 [LUIS 门户](https://www.luis.ai)，从应用列表中选择“myEnglishApp”应用。

1. 在右上角菜单中选择“管理”，然后选择“Azure 资源”。

1. 若要添加 LUIS，请选择“添加预测资源”。

    ![若要添加 LUIS 预测资源，请选择“添加预测资源”](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. 选择租户、订阅和资源名称。 选择“分配资源”。

   ![将资源分配给应用](./media/get-started-portal-deploy-app/assign-resource.png)

1. 完成相同的步骤，将创作密钥添加到应用。

1. 在表中找到新预测资源对应的新行，并复制终结点 URL。 它构造正确，可以向用于预测的 LUIS API 终结点发出 `HTTP GET` 请求。

> [!TIP]
> 如果要使用主动学习来改善 LUIS 应用，请选择“更改查询参数”，然后选择“保存日志”。 此操作通过添加 `log=true` 查询字符串参数来更改示例 URL。 对运行时终结点进行预测查询时，请复制并使用更改后的示例查询 URL。

## <a name="train-the-app"></a>训练应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>将应用发布到预测终结点

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>预测终结点请求

在门户中，URL 末尾的 `query=` 为用户言语追加到 GET 请求的位置。 在 `query=` 之后，输入在上一个快速入门结束时使用的相同用户言语：

```Is there a form named hrf-234098```

确保查询字符串包含以下对：

* `show-all-intents=true`
* `verbose=true`

浏览器会显示响应：

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

若要在测试窗格中看到相同级别的信息，必须发布应用。 发布应用后，在测试窗格中选择“与已发布版本进行比较”。 在发布的测试窗格中使用“显示 JSON 视图”，以查看与上一步相同的 JSON。 这样，就可以将当前所用应用的更改与已发布到终结点的应用的更改进行比较。

[![比较当前正在编辑的应用和已发布版本的应用](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>清理资源

完成此快速入门后，从顶部导航菜单中选择“我的应用”。 在列表中选中应用的复选框，然后在列表上方上下文工具栏中选择“删除”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [确定常见意向和实体](luis-tutorial-prebuilt-intents-entities.md)
