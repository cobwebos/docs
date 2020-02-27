---
title: 创建个性化体验创建服务资源
description: 服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624227"
---
# <a name="create-a-personalizer-resource"></a>创建 Personalizer 资源

Personalizer 资源与 Personalizer 学习循环是相同的。 为每个使用者域或内容区域创建单个资源（即学习循环）。 不要在同一循环中使用多个内容区域，因为这会使学习循环混乱，并提供不太好的预测。

如果希望 Personalizer 为网页的多个内容区域选择最佳内容，请对每个内容使用不同的学习循环。


## <a name="create-a-resource-in-the-azure-portal"></a>在 Azure 门户中创建资源

为每个反馈循环创建一个个性化体验创建服务资源。

1. 登录到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。 前面的链接会将你带到个性化体验创建服务的“创建”页。
1. 输入服务名称，选择订阅、位置、定价层和资源组。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure 门户创建 Personalizer 资源（也称为学习循环）。](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. 选择 "**创建**" 以创建资源。

1. 部署资源后，请选择 "**中转到资源**" 按钮，以跳到 Personalizer 资源。 若要[配置学习循环](how-to-settings.md)，请参阅新资源的 "**配置**" 页。

## <a name="create-a-resource-with-the-azure-cli"></a>使用 Azure CLI 创建资源

1. 用以下命令登录到 Azure CLI：

    ```bash
    az login
    ```

1. 创建资源组，使用逻辑分组来管理要与 Personalizer 资源一起使用的所有 Azure 资源。


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 使用以下命令为现有资源组创建新的 Personalizer 资源、_学习循环_。

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>后续步骤

* [配置](how-to-settings.md)Personalizer 学习循环