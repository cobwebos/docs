---
title: 快速入门：生成、部署和使用自定义模型 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何使用自定义翻译通过分步过程生成一个翻译系统。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: quickstart
ms.openlocfilehash: aa38bbf211c9d62bcb9051a7ddc7f7d404add585
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389616"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>快速入门：生成、部署和使用翻译的自定义模型

本文提供的分步说明介绍如何使用自定义翻译生成一个翻译系统。

## <a name="prerequisites"></a>先决条件

1. 若要使用[自定义翻译](https://portal.customtranslator.azure.ai)门户，需要通过 [Microsoft 帐户](https://signup.live.com)或 [Azure AD 帐户](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)（在 Azure 上托管的组织帐户）进行登录。

2. 通过 Azure 门户完成的对文本翻译 API 的订阅。 需要文本翻译 API 订阅密钥才能与自定义翻译中的工作区相关联。 请参阅[如何注册文本翻译 API](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup)。

3. 上面两项都有了以后，请登录到[自定义翻译](https://portal.customtranslator.azure.ai)门户。 进入自定义翻译门户以后，请导航到“设置”页，可以在其中将 Microsoft 文本翻译 API 订阅密钥与工作区相关联。

## <a name="create-a-project"></a>创建一个项目

在自定义翻译门户登陆页中，单击“新建项目”。 在对话框中，可以输入所需的项目名称、语言对、类别，以及其他相关字段。 然后，保存项目。 有关更多详细信息，请访问[创建项目](how-to-create-project.md)。

![创建项目](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>上传文档

接下来，上传[训练](training-and-model.md#training-dataset-for-custom-translator)、[优化](training-and-model.md#tuning-dataset-for-custom-translator)和[测试](training-and-model.md#testing-dataset-for-custom-translator)文档设置。 可以上传[并行](what-are-parallel-documents.md)文档和组合文档。 还可以上传[字典](what-is-dictionary.md)。

可以通过文档选项卡或特定项目的页面上传文档。

![上传文档](media/quickstart/ct-how-to-upload.png)

上传文档时，请选择文档类型（训练、优化或测试）以及语言对。 上传并行文档时，需额外指定文档名称。 有关更多详细信息，请访问[上传文档](how-to-upload-document.md)。

## <a name="create-a-model"></a>创建模型

所有必需文档都上传以后，下一步是生成模型。

选择已创建的项目。 将会看到已上传的所有文档，这些文档与此项目共享一个语言对。 选择要包括在模型中的文档。 可以选择[训练](training-and-model.md#training-dataset-for-custom-translator)、[优化](training-and-model.md#tuning-dataset-for-custom-translator)和[测试](training-and-model.md#testing-dataset-for-custom-translator)数据，或者只选择训练数据，让自定义翻译自动为模型生成优化和测试设置。

![创建模型](media/quickstart/ct-how-to-train.png)

选择完所需文档以后，请单击“创建模型”按钮，创建模型并开始训练。 可以在“模型”选项卡中查看训练的状态，以及所有模型的详细信息。

有关更多详细信息，请访问[创建模型](how-to-train-model.md)。

## <a name="analyze-your-model"></a>分析模型

训练成功完成以后，请检查结果。 BLEU 分数是一个指标，指示翻译的质量。 也可手动比较通过自定义模型进行的翻译与测试集中提供的翻译，方法是导航到“测试”选项卡，然后单击“系统结果”。 手动检查这其中的一些翻译可以很好地了解系统的翻译质量。 有关更多详细信息，请访问[系统测试结果](how-to-view-system-test-results.md)。

## <a name="deploy-a-trained-model"></a>部署训练的模型

准备部署训练的模型时，请单击“部署”按钮。 每个项目可以有一个部署的模型。可以在“状态”列中查看部署的状态。 有关更多详细信息，请访问[模型部署](how-to-view-system-test-results.md#deploy-a-model)。

![部署训练的模型](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>使用已部署的模型

部署的模型可以通过 Microsoft 文本[翻译 API V3 进行访问，方法是指定 CategoryID](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)。 有关文本翻译 API 的详细信息，可查看 [API 参考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)网页。

## <a name="next-steps"></a>后续步骤

- 了解如何导航[自定义翻译工作区并管理项目](workspace-and-project.md)。
