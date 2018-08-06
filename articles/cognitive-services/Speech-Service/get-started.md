---
title: 免费试用语音服务
description: 了解可以如何免费试用语音服务。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ff9a258d42f25ded82545909cdeade119548148e
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325201"
---
# <a name="try-the-speech-service-for-free"></a>免费试用语音服务

以简单、经济的方式开始使用语音服务。 可利用 30 天免费试用了解此服务的功能并确定它能否满足应用程序需求。

如果需要更长时间，请注册 Microsoft Azure 帐户，其中附带 200 美元的服务额度，可用于支付长达 30 天的付费语音服务订阅。

最后，语音服务提供适用于开发应用程序的免费、低容量层。 服务额度到期后，仍可以保留此免费订阅。

## <a name="free-trial"></a>免费试用

利用 30 天免费试用可在有限时间内访问 S0 标准定价层。 若要注册 30 天免费试用，请执行下列步骤。

1. 转到[试用认知服务](https://azure.microsoft.com/try/cognitive-services/)页。

1. 切换到“语音”选项卡，单击“语音服务”旁的“获取 API 密钥”按钮。

   ![语音服务选项卡](media/index/try-speech-api-free-trial1.png)<br>
   ![API 密钥](media/index/try-speech-api-free-trial2.png)

3. 同意条款，然后从下拉菜单选择区域设置。

   ![同意条款](media/index/try-speech-api-free-trial3.png)

4. 使用 Microsoft、Facebook、LinkedIn 或 GitHub 帐户登录。 或者，可以注册免费的 Microsoft 帐户：

    * 转到 [Microsoft 帐户门户](https://account.microsoft.com/account)。
    * 单击“Microsoft 登录”。

    ![登录](media/index/try-speech-api-free-trial4.png)

    * 系统提示登录时，请单击“创建帐户”。

    ![创建新帐户](media/index/try-speech-api-free-trial5.png)

    * 在后续步骤中，输入电子邮件地址或者电话号码，设置密码，然后按照说明验证新的 Microsoft 帐户。

登录后，免费试用开始。 显示的网页中将列出你当前具有其试用订阅的所有认知服务。 “语音服务”旁将列出两个订阅密钥。 可以在应用程序中使用任一密钥。

> [!NOTE]
> 所有免费试用订阅均位于美国西部区域。 发出请求时，请确保使用与区域对应的终结点。

## <a name="new-azure-account"></a>新 Azure 帐户

新 Azure 帐户将收到可持续长达 30 天的 200 美元服务额度。 可使用此额度深入探索语音服务或者开始应用程序开发。

若要注册新的 Azure 帐户，请执行下列步骤。

1. 转到 [Azure 注册页](https://azure.microsoft.com/free/ai/)。 

1. 单击“免费开始”。

    ![免费开始](media/index/try-speech-api-new-azure1.png)

3. 使用 Microsoft 帐户登录。 如果没有帐户，请：

    * 转到 [Microsoft 帐户门户](https://account.microsoft.com/account)。
    * 单击“Microsoft 登录”。
    * 系统提示登录时，请单击“创建帐户”。
    * 在后续步骤中，输入电子邮件地址或者电话号码，设置密码，然后按照说明验证新的 Microsoft 帐户。

1. 输入注册帐户所需的其余信息。 指定国家/地区和姓名，并提供电话号码和电子邮件地址。

    ![输入信息](media/index/try-speech-api-new-azure2.png)

    通过电话和提供信用卡号验证身份，然后接受 Azure 用户协议。 （不会对信用卡计费。）

    ![接受协议](media/index/try-speech-api-new-azure3.png)

免费 Azure 帐户已创建。 按照下一部分的步骤开始订阅语音服务。

## <a name="create-a-speech-resource-in-azure"></a>在 Azure 中创建语音资源

要将语音服务资源添加到 Azure 帐户，请执行以下步骤。

1. 使用 Microsoft 帐户登录 [Azure 门户](https://ms.portal.azure.com/)。

1. 单击门户左上角的“创建资源”（绿色 + 图标）。

    ![创建资源](media/index/try-speech-api-create-speech1.png)

1. 在“新建”窗口中，搜索“语音”。

    ![单击“语音”](media/index/try-speech-api-create-speech2.png)

1. 在搜索结果中，单击“语音(预览)”。

1. 单击“语音”服务面板底部的“创建”按钮。

    ![单击“创建”](media/index/try-speech-api-create-speech3.png)

1. 在“创建”面板中，输入：

    * 新资源的名称。 名称有助于区分同一服务的多个订阅。
    * 选择新资源关联的 Azure 订阅，以确定计费方式。
    * 选择将使用资源的区域。 目前在东亚、北欧和美国西部区域提供语音服务。
    * 选择定价层：F0（有限的免费订阅）或 S0（标准订阅）。 单击“查看全部定价详细信息”，获取每个层的定价和用量配额的完整信息。
    * 为此“语音”订阅创建新的资源组或将其分配到现有组。 资源组有助于使多种 Azure 订阅保持有序状态。
    * 为了以后可便捷访问订阅，请勾选“固定到仪表板”复选框。
    * 单击“创建”。

    ![单击面板中的“创建”](media/index/try-speech-api-create-speech4.png)

    创建和部署新语音资源可能需要一些时间。 “快速启动”面板将出现并显示新资源的信息。

    ![“快速启动”面板](media/index/try-speech-api-create-speech5.png)

1. 单击“快速启动”面板步骤 1 下的“密钥”链接，以显示订阅密钥。 每个订阅有两个密钥；可在应用程序中使用任意一个。 单击每个密钥旁的按钮，可将其复制到剪贴板以粘贴到代码中。

> [!NOTE]
> 可在一个或多个区域中创建任意数量的标准层订阅。 但是，只能创建一个免费层订阅。

## <a name="next-steps"></a>后续步骤

完成我们的 10 分钟快速入门之一，或查看我们的 SDK 示例。

> [!div class="nextstepaction"]
> [快速入门：在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
> [语音 SDK 示例](speech-sdk.md#get-the-samples)
