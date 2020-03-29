---
title: 创建个性化体验创建服务资源
description: 服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336067"
---
# <a name="create-a-personalizer-resource"></a>创建个性化资源

个性化器资源与个人学习循环相同。 为您拥有的每个主题域或内容区域创建单个资源或学习循环。 不要在同一循环中使用多个内容区域，因为这会混淆学习循环并提供较差的预测。

如果希望个性化程序为网页的多个内容区域选择最佳内容，请为每个内容使用不同的学习循环。


## <a name="create-a-resource-in-the-azure-portal"></a>在 Azure 门户中创建资源

为每个反馈循环创建一个个性化体验创建服务资源。

1. 登录到[Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。 前面的链接会将你带到个性化体验创建服务的“创建”页。****
1. 输入服务名称，选择订阅、位置、定价层和资源组。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure 门户创建个性化工具资源，也称为学习循环。](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. 选择 **"创建**"以创建资源。

1. 部署资源后，选择"**转到资源**"按钮以转到个性化程序资源。

1. 选择资源的 **"快速入门页**"，然后复制终结点和键的值。 您需要资源终结点和密钥才能使用排名和奖励 API。

1. 选择新资源的 **"配置"** 页以[配置学习循环](how-to-settings.md)。

## <a name="create-a-resource-with-the-azure-cli"></a>使用 Azure CLI 创建资源

1. 使用以下命令登录到 Azure CLI：

    ```azurecli-interactive
    az login
    ```

1. 创建资源组，一个逻辑分组来管理要与个性化工具资源一起使用的所有 Azure 资源。


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 使用现有资源组的以下命令创建新的个性化资源 _（学习循环_）。

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    这将返回一个 JSON 对象，其中包括**您的资源终结点**。

1. 使用以下 Azure CLI 命令获取**资源密钥**。

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    您需要资源终结点和密钥才能使用排名和奖励 API。

## <a name="next-steps"></a>后续步骤

* [配置](how-to-settings.md)个性化学习循环