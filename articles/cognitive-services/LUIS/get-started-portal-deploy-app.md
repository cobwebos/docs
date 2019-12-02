---
title: 快速入门：通过 LUIS 门户部署应用
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何通过创建预测终结点资源、分配资源、训练和发布应用来部署应用。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 65af2caa2272549b5ad562ff3c38b90e3ea43fd5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278542"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>快速入门：在 LUIS 门户中部署应用

[!INCLUDE [Uses preview portal](./includes/uses-portal-preview.md)]

LUIS 应用准备好将话语预测返回到客户端应用程序（例如聊天机器人）后，就需要将应用部署到预测终结点。

本快速入门介绍如何部署应用程序。 此外还介绍如何创建预测终结点资源、如何将资源分配给应用、如何训练应用以及如何发布应用。

## <a name="prerequisites"></a>先决条件

* 获取 [Azure 订阅](https://azure.microsoft.com/free)。
* 完成[上述门户快速入门](get-started-portal-build-app.md)或[下载并导入应用](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json)。
* 如果应用将 Azure 资源身份验证的日期提前，则[迁移到 Azure 资源](luis-migration-authoring.md)。 电子邮件身份验证生效时，某些门户页面的外观会有所不同。

## <a name="create-the-endpoint-resource"></a>创建终结点资源

在 Azure 门户中创建预测终结点资源。 此资源仅应用于终结点预测查询。 请勿使用此资源来创建对应用的更改。

1. 在 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)中登录并创建资源。

1. 使用以下设置配置订阅：

   |设置|值|目的|
   |--|--|--|
   |Name|`my-luis-resource`|Azure 资源的名称。 将资源分配给 LUIS 门户中的应用时需要此名称。|
   |Subscription|订阅|选择与你的帐户关联的其中某个订阅。|
   |Resource group|`my-resource-group`|为所有认知服务资源创建新资源组。 完成资源后，可以删除资源组以清理订阅。 |
   |创作位置|**美国西部**|用于创作的 Azure 区域。|
   |创作定价层|**F0**|用于创作的默认定价层。|
   |运行时位置|**美国西部**|用于预测终结点查询的 Azure 区域。|
   |运行时定价层|**S0**|此定价层面向高流量网站。|
   | | | |


   ![Azure API 选择](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. 选择“创建”以创建 Azure 资源  。

   在下一部分中，你将了解如何在 LUIS 门户中将此新资源连接到 LUIS 应用。

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>将资源关键字分配给 LUIS 门户中的 LUIS 应用

每次为 LUIS 创建新资源时，都需要将资源分配给 LUIS 应用。 分配后，除非创建新资源，否则无需再次执行此步骤。 可以创建新资源来扩展应用的区域或支持更多预测查询。

1. 登录 [LUIS 门户](https://preview.luis.ai)，从应用列表中选择“myEnglishApp”应用  。

1. 在右上角菜单中选择“管理”  ，然后选择“Azure 资源”  。

1. 若要添加 LUIS，请选择“添加预测资源”  。

    ![若要添加 LUIS 预测资源，请选择“添加预测资源”](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. 选择租户、订阅和资源名称。 选择“分配资源”。 

   ![将资源分配给应用](./media/get-started-portal-deploy-app/assign-resource.png)

1. 完成相同的步骤，将创作密钥添加到应用。

1. 在表中找到新预测资源对应的新行，并复制终结点 URL。 它构造正确，可以向用于预测的 LUIS API 终结点发出 `HTTP GET` 请求。

## <a name="train-the-app"></a>训练应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>将应用发布到预测终结点

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>预测终结点请求

在预览门户中，URL 末尾的 `query=` 为用户言语追加到 GET 请求的位置。 在 `query=` 之后，输入在上一个快速入门结束时使用的相同用户言语：

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

若要在测试窗格中看到相同级别的信息，必须发布应用。 发布应用后，在测试窗格中选择“与已发布版本进行比较”  。 在发布的测试窗格中使用“显示 JSON 视图”，以查看与上一步相同的 JSON  。 这样，就可以将当前所用应用的更改与已发布到终结点的应用的更改进行比较。

[![比较当前正在编辑的应用和已发布版本的应用](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>清理资源

完成此快速入门后，从顶部导航菜单中选择“我的应用”  。 在列表中选中应用的复选框，然后在列表上方上下文工具栏中选择“删除”  。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [确定常见意向和实体](luis-tutorial-prebuilt-intents-entities.md)
