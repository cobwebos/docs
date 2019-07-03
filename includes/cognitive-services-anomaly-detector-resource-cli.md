---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503631"
---
以下 Azure CLI 命令将在免费定价层上预配一个异常检测器资源。 单击“试用”  按钮，粘贴代码，并按 Enter 以在 Azure Cloud Shell 中运行它。 它将输出用于对应用程序进行身份验证的密钥。 完成后，为你的任一密钥[创建一个环境变量](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)（名为 `ANOMALY_DETECTOR_KEY`）。

> [!NOTE]
> * 可以选择：
>    * 使用 [Azure 门户](../articles/cognitive-services/cognitive-services-apis-create-account.md)创建资源，或使用 [Azure CLI](../articles/cognitive-services/cognitive-services-apis-create-account.md) 在本地计算机上创建资源。
>    * 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，它将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上提供。
>    * 请在 [Azure 门户](https://portal.azure.com/)上查看此资源。 

认知服务由你预配的 Azure 资源表示。 每个认知服务帐户（及其关联的 Azure 资源）都必须属于某个 Azure 资源组。

1. 创建 Azure 资源组

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. 在免费层上创建异常检测器资源

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. 列出资源的密钥

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```