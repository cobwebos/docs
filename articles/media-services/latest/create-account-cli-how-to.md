---
title: 使用 Azure CLI 创建媒体服务帐户 - Azure | Microsoft Docs
description: 按照本快速入门的步骤，创建 Azure 媒体服务帐户。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: fc80e2c540279c0a0c8acb575c9e5b1478a46cf8
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133360"
---
# <a name="create-an-azure-media-services-account"></a>创建 Azure 媒体服务帐户

若要开始加密、编码、分析、管理和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 创建媒体服务帐户时，还会在此帐户所在的地理区域内创建一个关联的媒体服务帐户（或使用现有存储帐户）。
本文介绍使用 Azure CLI 创建新 Azure 媒体服务帐户的步骤。  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 一个有效的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，并非所有[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中运行。 建议在本地使用 CLI。

## <a name="set-the-azure-subscription"></a>设置 Azure 订阅

在以下命令中，为媒体服务帐户提供想要使用的 Azure 订阅 ID。 导航到[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)即可查看有权访问的订阅列表。

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>后续步骤

[流式传输文件](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>另请参阅

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

