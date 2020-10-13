---
title: 创建个性化体验创建服务资源
description: 本文介绍如何在 Azure 门户中为每个反馈循环创建 personalizer 资源。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c75d917f1abe72af2f4aa56b0f67dbb7bcd24a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303550"
---
# <a name="create-a-personalizer-resource"></a>创建 Personalizer 资源

Personalizer 资源与 Personalizer 学习循环是相同的。 为每个使用者域或内容区域创建单个资源（即学习循环）。 不要在同一循环中使用多个内容区域，因为这会使学习循环混乱，并提供不太好的预测。

如果希望 Personalizer 为网页的多个内容区域选择最佳内容，请对每个内容使用不同的学习循环。


## <a name="create-a-resource-in-the-azure-portal"></a>在 Azure 门户中创建资源

为每个反馈循环创建一个个性化体验创建服务资源。

1. 登录到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。 前面的链接会将你带到个性化体验创建服务的“创建”页。****
1. 输入服务名称，选择订阅、位置、定价层和资源组。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure 门户创建 Personalizer 资源（也称为学习循环）。](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. 选择 " **创建** " 以创建资源。

1. 部署资源后，请选择 " **中转到资源** " 按钮，以跳到 Personalizer 资源。

1. 选择资源的 " **快速启动** " 页，然后复制终结点和键的值。 需要使用 "资源终结点" 和 "密钥" 才能使用排名和奖励 Api。

1. 选择新资源的 " **配置** " 页， [配置学习循环](how-to-settings.md)。

## <a name="create-a-resource-with-the-azure-cli"></a>使用 Azure CLI 创建资源

1. 用以下命令登录到 Azure CLI：

    ```azurecli-interactive
    az login
    ```

1. 创建资源组，使用逻辑分组来管理要与 Personalizer 资源一起使用的所有 Azure 资源。


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 使用以下命令为现有资源组创建新的 Personalizer 资源、 _学习循环_。

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    这将返回一个 JSON 对象，其中包含 **资源终结点**。

1. 使用以下 Azure CLI 命令获取你的 **资源键**。

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    需要使用 "资源终结点" 和 "密钥" 才能使用排名和奖励 Api。

## <a name="next-steps"></a>后续步骤

* [配置](how-to-settings.md) Personalizer 学习循环
