---
title: 使用自定义语音服务创建语言模型的教程 - Microsoft 认证服务 | Microsoft Docs
description: 本教程介绍如何在 Microsoft 认证服务中使用自定义语音服务创建语言模型。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ms.openlocfilehash: 599302cbf614f800d35a9a8c6a401c9692fc2e39
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268274"
---
# <a name="tutorial-create-a-custom-language-model"></a>教程：创建自定义语言模型

在本教程中，请为你期望用户在应用程序中说出或键入的文本查询或表述创建自定义语言模型， 然后即可将此自定义语言模型与 Microsoft 提供的现成的最新语音模型配合使用，以便向应用程序添加语音交互功能。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 准备数据
> * 导入语言数据集
> * 创建自定义语言模型

如果没有认知服务帐户，请在开始前创建一个[免费帐户](https://cris.ai)。

## <a name="prerequisites"></a>先决条件

打开[认知服务订阅](https://cris.ai/Subscriptions)页，确保认知服务帐户连接到某个订阅。

如果没有列出任何订阅，则可单击“获取免费订阅”按钮，让认知服务为你创建一个帐户。 也可单击“连接现有的订阅”按钮，连接到在 Azure 门户中创建的自定义搜索服务订阅。

若要了解如何在 Azure 门户中创建自定义搜索服务订阅，请参阅[在 Azure 门户中创建认知服务 API 帐户](../../cognitive-services-apis-create-account.md)。

## <a name="prepare-the-data"></a>准备数据

若要为应用程序创建自定义语言模型，需为系统提供一系列示例表述，例如：

*   “他上周患了荨麻疹。”
*   “病人有一个疝修补术愈合后的疤。”

这些句子不需要是完整的句子或语法正确的句子，但应准确反映系统在部署过程中通常会遇到的语音输入。 这些示例应该反映用户将通过应用程序执行的任务的样式和内容。

语言模型数据应该使用 US-ASCII 或 UTF-8 以纯文本文件格式写入，具体取决于区域设置。 就 en-US 来说，这两种编码都受支持。 就 zh-CN 来说，仅支持 UTF-8（BOM 为可选）。 此文本文件应该每行包含一个示例（句子、表述或查询）。

如果希望某些句子有更高的权重（重要性），可以多次将它添加到数据中。 重复次数最好是 10 - 100 次。 如果将其规范化为 100 次，则可轻松地据此对句子进行衡量。

下表汇总了语言数据的主要要求。

| 属性 | 值 |
|----------|-------|
| 文本编码 | en-US：US-ACSII 或 UTF-8；zh-CN：UTF-8|
| 每行的表述数 | 1 |
| 文件大小上限 | 200 MB |
| 备注 | 避免将字符重复 4 次以上，例如“aaaaa”|
| 备注 | 不得使用“\t”之类的特殊字符，也不得使用 [Unicode 字符表](http://www.utf8-chartable.de/)中 U+00A1 以上的任何其他 UTF-8 字符|
| 备注 | URI 也会被拒绝，因为没有特定的方法可以给 URI 发音|

文本在导入时，会进行文本规范化，使之可供系统处理。 但是，用户在上传数据之前，必须完成一些重要的规范化操作。 请参阅 [Transcription guidelines](cognitive-services-custom-speech-transcription-guidelines.md)（听录准则），确定在准备语言数据时哪一种语言合适。

## <a name="import-the-language-data-set"></a>导入语言数据集

单击“声学数据集”行中的“导入”按钮，此时站点会显示一个用于上传新数据集的页面。

准备导入语言数据集时，请登录到[自定义语音服务门户](https://cris.ai)。  然后单击顶部功能区的“自定义语音”下拉菜单，选择“适应数据”。 如果这是第一次将数据上传到自定义语音服务，则会看到名为“数据集”的空表。

若要导入新的数据集，请单击“语言数据集”行中的“导入”按钮，此时站点会显示一个用于上传新数据集的页面。 输入用于标识未来的数据集的“名称”和“说明”。 接下来，使用“选择文件”按钮找到语言数据的文本文件。 然后，单击“导入”，以便上传数据集。 这可能需要数分钟的时间，具体取决于数据集的大小。

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-import.png)

导入完成后会返回到语言数据表，此时会看到一个对应于语言数据集的条目。 请注意，已为其分配了一个唯一 ID (GUID)。 此数据还会有一个反映其当前状态的状态。 如果正在排队等待处理，则其状态为“正在等待”；如果正在进行验证，则其状态为“正在处理”；如果数据已做好使用准备，则其状态为“完成”。 进行数据验证时，会对文件中的文本以及数据的某些文本规范化内容执行一系列检查。

当状态为“完成”时，可以单击“查看报告”，查看语言数据验证报告。 将会显示通过验证和未通过验证的表述的数目，以及未通过验证的表述的详细信息。 在下面的示例中，两个示例因字符不正确而没有通过验证（在此数据集中，第一个示例有两个表情符，第二个示例有多个字符不在 ASCII 可打印字符集中）。

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-report.png)

当语言数据集的状态为“完成”后，即可将它用来创建自定义语言模型。

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>创建自定义语言模型

语言数据准备好以后，请单击“菜单”下拉菜单中的“语言模型”，启动创建自定义语言模型的过程。 此页包含一个名为“语言模型”的表，其中有你当前的自定义语言模型。 如果尚未创建任何自定义语言模型，则此表将为空。 当前的区域设置反映在表标题中。 若要创建另一语言的语言模型，请单击“更改区域设置”。 有关受支持语言的其他信息，可参阅[更改区域设置](cognitive-services-custom-speech-change-locale.md)的相关部分。 若要创建新的模型，请单击表标题下的“新建”链接。

在“创建语言模型”页上输入“名称”和“说明”，以便跟踪此模型的相关信息，例如所使用的数据集。 接下来，从下拉菜单中选择“基础语言模型”。 可以从此模型着手进行自定义。 有两个基础语言模型可供选择。 Microsoft 搜索和听写 LM 适用于在应用程序中发出的语音，例如命令、搜索查询或听写。 Microsoft 对话 LM 适用于识别以对话形式说出的语音。 此类语音通常是面对另一个人在呼叫中心或会议中发出。

在指定基础语言模型以后，请使用“语言数据”下拉菜单选择要用于自定义的语言数据集

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-language-models-create2.png)

就声学模型的创建来说，可以选择在处理完成后对新的模型进行脱机测试。 由于这是评估语音到文本性能，因此脱机测试需要声学数据集。

若要对语言模型执行脱机测试，请选中“脱机测试”旁边的复选框。 然后，从下拉菜单中选择一个声学模型。 如果尚未创建任何自定义声学模型，Microsoft 基础声学模型将是菜单中的唯一模型。 如果已选取对话 LM 基础模型，则需在此处使用对话 AM。 如果使用搜索和听写 LM 模型，则需选择搜索和听写 AM 模型。

最后，请选择要用于执行评估的声学数据集。

若已做好开始处理的准备，请按“创建”。 这样会返回到语言模型表。 表中会有一个对应于此模型的新条目。 状态反映模型的状态。将会经历多个状态，包括“正在等待”、“正在处理”和“完成”。

当模型处于“完成”状态后，就可以将其部署到终结点。 单击“查看结果”会显示脱机测试（如果已执行）的结果。

有时需要更改模型的“名称”或“说明”，可以使用语言模型表的相应行中的“编辑”链接进行更改。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何开发可以与文本配合使用的自定义语言模型。 若要创建可以与音频文件和听录配合使用的自定义声学模型，请继续阅读有关如何创建声学模型的教程。

> [!div class="nextstepaction"]
> [创建自定义声学模型](cognitive-services-custom-speech-create-acoustic-model.md)
