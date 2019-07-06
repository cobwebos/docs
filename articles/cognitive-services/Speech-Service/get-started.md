---
title: 免费试用语音服务
titleSuffix: Azure Cognitive Services
description: 以简单、经济的方式开始使用语音服务。 可利用 30 天免费试用了解此服务的功能并确定它能否满足应用程序需求。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: aaae84fcb7df2abfe7e78adb64c22fb19fe45f4e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606719"
---
# <a name="try-speech-services-for-free"></a>免费试用语音服务

以简单、经济的方式开始使用语音服务。 可利用 30 天免费试用了解此服务的功能并确定它能否满足应用程序需求。

如果需要更长时间，请注册 Microsoft Azure 帐户，其中附带 200 美元的服务额度，可用于支付长达 30 天的付费语音服务订阅。

最后，语音服务提供适合用于开发应用程序的免费、低容量层。 服务额度到期后，仍可以保留此免费订阅。

## <a name="free-trial"></a>免费试用

利用 30 天免费试用版，可在有限时间内访问标准定价层。

若要注册 30 天免费试用版，请执行下列步骤：

1. 转到[试用认知服务](https://azure.microsoft.com/try/cognitive-services/)。

1. 选择“语音 API”选项卡。 

   ![“语音服务”选项卡](media/index/try-speech-api-free-trial1.png)

1. 在“语音服务”下，选择“获取 API 密钥”按钮   。

   ![API 密钥](media/index/try-speech-api-free-trial2.png)

1. 同意条款，然后从下拉菜单选择区域设置。

   ![同意条款](media/index/try-speech-api-free-trial3.png)

1. 使用 Microsoft、Facebook、LinkedIn 或 GitHub 帐户登录。

    可以在 [Microsoft 帐户门户](https://account.microsoft.com/account)中注册免费的 Microsoft 帐户。 若要开始，请单击“Microsoft 登录”，然后，当系统要求登录时，请单击“创建帐户”。   按步骤创建并验证新的 Microsoft 帐户。

登录到“试用认知服务”后，免费试用开始。 显示的网页中将列出你当前具有其试用订阅的所有 Azure 认知服务服务。 “语音服务”旁将列出两个订阅密钥  。 可以在应用程序中使用任一密钥。

> [!NOTE]
> 所有免费试用订阅均位于美国西部区域。 发出请求时，请确保使用 `westus` 终结点。

## <a name="new-azure-account"></a>新 Azure 帐户

新 Azure 帐户将收到使用时间长达 30 天的 200 美元服务额度。 可使用此额度深入探索语音服务或者开始应用程序开发。

若要注册新的 Azure 帐户，请转到 [Azure 注册页](https://azure.microsoft.com/free/ai/)，单击“免费开始”，  然后使用 Microsoft 帐户创建新的 Azure 帐户。

可以在 [Microsoft 帐户门户](https://account.microsoft.com/account)中注册免费的 Microsoft 帐户。 若要开始，请单击“Microsoft 登录”，然后，当系统要求登录时，请单击“创建帐户”。   按步骤创建并验证新的 Microsoft 帐户。

创建 Azure 帐户后，请按照下一部分的步骤开始订阅语音服务。

## <a name="create-a-speech-resource-in-azure"></a>在 Azure 中创建语音资源

若要将语音服务资源（免费层或付费层）添加到 Azure 帐户，请执行以下操作：

1. 使用 Microsoft 帐户登录 [Azure 门户](https://portal.azure.com/)。

1. 选择门户左上角的“创建资源”。 

    ![创建资源](media/index/try-speech-api-create-speech1.png)

1. 在“新建”窗口中，搜索“语音”。  

1. 在搜索结果中，选择“语音”。 

    ![选择语音](media/index/try-speech-api-create-speech2.png)

1. 在“语音”下，选择“创建”   按钮。

    ![选择“创建”按钮](media/index/try-speech-api-create-speech3.png)

1. 在“创建”下输入： 

   * 新资源的名称。 名称有助于区分同一服务的多个订阅。
   * 选择新资源关联的 Azure 订阅，以确定计费方式。
   * 选择将使用资源的区域。 目前在东亚、欧洲北部和美国西部区域提供语音服务。
   * 选择免费或付费定价层。 单击“查看全部定价详细信息”，获取每个层的定价和用量配额的完整信息  。
   * 为此“语音”订阅创建新的资源组或将订阅分配到现有资源组。 资源组有助于使多种 Azure 订阅保持有序状态。
   * 为了以后可便捷访问订阅，请选中“固定到仪表板”复选框  。
   * 选择“创建”  。

     ![选择“创建”按钮](media/index/try-speech-api-create-speech4.png)

     创建和部署新语音资源需要一些时间。 选择“快速入门”，查看新资源的信息。 

     ![“快速启动”面板](media/index/try-speech-api-create-speech5.png)

1. 在“快速入门”下，单击步骤 1 中的“密钥”链接，以显示订阅密钥   。 每个订阅有两个密钥；可在应用程序中使用任意一个密钥。 选择每个密钥旁的按钮，可将其复制到剪贴板以粘贴到代码中。

> [!NOTE]
> 可在一个或多个区域中创建数量不受限的标准层订阅。 但是，只能创建一个免费层订阅。 在免费层上进行的模型部署如果连续 7 天处于未使用状态，则会被系统自动停用。

## <a name="switch-to-a-new-subscription"></a>切换到新订阅

若要从一个订阅切换到另一个订阅（例如，在免费试用过期或发布应用程序的情况下进行切换），请将代码中的区域和订阅密钥替换为新 Azure 资源的区域和订阅密钥。

> [!NOTE]
> 免费试用密钥在美国西部 (`westus`) 区域创建。 通过 Azure 仪表板创建的订阅可以位于某个其他的区域，具体取决于你的选择。

* 如果应用程序使用[语音 SDK](speech-sdk.md)，请在创建语音配置时提供区域代码，例如 `westus`。
* 如果应用程序使用某个语音服务的 [REST API](rest-apis.md)，则区域是你在发出请求时使用的终结点 URI 的一部分。

为某个区域创建的密钥仅在该区域有效。 尝试在其他区域使用此类密钥会导致身份验证错误。

## <a name="next-steps"></a>后续步骤

完成我们的 10 分钟快速入门之一，或查看我们的 SDK 示例：

> [!div class="nextstepaction"]
> [快速入门：在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
> [语音 SDK 示例](speech-sdk.md#get-the-samples)
