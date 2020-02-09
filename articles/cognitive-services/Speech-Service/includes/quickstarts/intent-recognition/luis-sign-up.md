---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: 58122066e65fbcb02b6b4333985785b219d3dbbd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900338"
---
若要完成意向识别快速入门，需要使用 LUIS 预览门户创建 LUIS 帐户和项目。 本快速入门仅需 LUIS 订阅。 无需语音服务订阅  。

你需要做的第一件事是使用 LUIS 预览门户创建 LUIS 帐户和应用。 创建的 LUIS 应用会将预生成域用于主自动化，提供意向、实体和示例言语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点，可使用语音 SDK 进行调用。 

可以按照这些说明创建 LUIS 应用：

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">快速入门：构建预生成域应用 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

完成后，需要以下四项信息：

* 在打开“语音启动”  的情况下重新发布
* 你的 LUIS **主密钥**
* 你的 LUIS **位置**
* 你的 LUIS **应用 ID**

可以在 [LUIS 预览门户](https://preview.luis.ai/)中从以下位置找到此信息：

1. 在LUIS 预览门户中，选择你的应用，然后选择“发布”  按钮。

2. 选择“生产”  槽，如果使用的是 `en-US`，请将“语音启动”  选项切换到“开”  位置。 然后选择“发布”  按钮。

    > [!IMPORTANT]
    > 强烈建议使用“语音启动”  ，因为它会提高语音识别的准确性。

    > [!div class="mx-imgBorder"]
    > ![将 LUIS 发布到终结点](../../../media/luis/publish-app-popup.png)

3. 在 LUIS 预览版门户中，选择“管理”，然后选择“Azure 资源”   。 在此页上，可以找到你的 LUIS 密钥和位置（有时也称为“区域”）  。

   > [!div class="mx-imgBorder"]
   > ![LUIS 密钥和位置](../../../media/luis/luis-key-region.png)

4. 获取密钥和位置后，需要应用 ID。 选择“应用程序设置”-- 你的应用 ID 在此页上提供  。

   > [!div class="mx-imgBorder"]
   > ![LUIS 应用 ID](../../../media/luis/luis-app-id.png)
