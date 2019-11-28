---
title: 快速入门：适用于 Python 的内容审查器客户端库 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 了解如何从 Azure 认知服务内容审查器的适用于 Python 的客户端库开始操作。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: ee3f59bc9e9f8c7950d005b16433cde9267fea9a
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539006"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>快速入门：适用于 Python 的内容审查器客户端库

适用于 Python 的内容审查器客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 内容审查器是一项认知服务，用于检查文本、图像和视频中是否存在可能的冒犯性内容、有风险内容或其他令人不适的内容。 找到此类内容时，此服务会将相应的标签（标记）应用到该内容。 然后，应用会处理标记的内容，使之符合法规的要求，或者为用户维持一个理想的环境。

使用适用于 Python 的内容审查器客户端库可以：

* [审查文本](#moderate-text)
* [使用自定义字词列表](#use-a-custom-terms-list)
* [审查图像](#moderate-images)
* [使用自定义图像列表](#use-a-custom-image-list)
* [创建评审](#create-a-review)

[参考文档](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [包 (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>设置

### <a name="create-a-content-moderator-azure-resource"></a>创建内容审查器 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建内容审查器的资源。 还可以：

* 免费获取在七天内有效的[试用密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，[Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上会提供此密钥。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源

获取试用订阅或资源的密钥后，请为该密钥和终结点 URL [创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别名为 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 和 `CONTENT_MODERATOR_ENDPOINT`。
 
### <a name="create-a-python-script"></a>创建 Python 脚本

创建新的 Python 脚本，并在喜好的编辑器或 IDE 中打开它。 将以下 `import` 语句添加到该文件的顶部。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

接下来，为资源的终结点位置创建变量，并将密钥创建为环境变量。 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

### <a name="install-the-client-library"></a>安装客户端库

可使用以下命令安装内容审查器客户端库：

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>对象模型

以下类将处理内容审查器 Python SDK 的某些主要功能。

|Name|说明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|所有内容审查器功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|此类提供用于分析成人内容、个人信息或人脸的功能。|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|此类提供用于在文本中分析语言、猥亵内容、错误和个人信息的功能。|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|此类提供评审 API 的功能，包括用于创建作业、自定义工作流和人工评审的方法。|

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的内容审查器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [审查文本](#moderate-text)
* [使用自定义字词列表](#use-a-custom-terms-list)
* [审查图像](#moderate-images)
* [使用自定义图像列表](#use-a-custom-image-list)
* [创建评审](#create-a-review)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 本快速入门假定你已为内容审查器密钥和终结点[创建了环境变量](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

使用终结点和密钥实例化某个客户端。 使用密钥创建 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 对象，然后在终结点上使用该对象创建 [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) 对象。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>审查文本

以下代码使用内容审查器客户端分析文本的正文，并将结果输出到控制台。 首先，在项目的根目录中创建 **text_files/** 文件夹，并在其中添加 *content_moderator_text_moderation.txt* 文件。 将你自己的文本添加到此文件中，或使用以下示例文本：

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

添加对新文件夹的引用。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

然后，将以下代码添加到 Python 脚本中。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>使用自定义字词列表

以下代码演示如何管理用于审查文本的自定义字词列表。 可以使用 [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) 类创建字词列表、管理各个字词，并根据该列表筛选其他文本正文。

### <a name="get-sample-text"></a>获取示例文本

若要使用此示例，必须在项目的根目录中创建 **text_files/** 文件夹，并在其中添加 *content_moderator_term_list.txt* 文件。 此文件应包含要根据字词列表检查的有序文本。 可使用以下示例文本：

```
This text contains the terms "term1" and "term2".
```

添加对该文件夹的引用（如果尚未定义引用）。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>创建列表

将以下代码添加到 Python 脚本以创建自定义字词列表，并保存其 ID 值。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>定义列表详细信息

可以使用列表的 ID 来编辑其名称和说明。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>将字词添加到列表

以下代码将字词 `"term1"` 和 `"term2"` 添加到列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>获取列表中的所有字词

可以使用列表 ID 返回列表中的所有字词。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>刷新列表索引

每当在列表中添加或删除字词后，都必须先刷新索引，然后才能使用更新的列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>根据列表筛选文本

自定义字词列表的主要功能就是将文本正文与该列表进行比较，找出是否存在任何匹配的字词。 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>从列表中删除字词

以下代码从列表中删除字词 `"term1"`。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>从列表中删除所有字词

使用以下代码可清除列表中的所有字词。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>删除列表

使用以下代码可删除自定义字词列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>审查图像

以下代码使用内容审查器客户端以及 [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) 对象在图像中分析成人内容和猥亵内容。

### <a name="get-images"></a>获取图像

定义对要分析的某些图像的引用。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

然后添加以下代码来循环访问图像。 本部分的余下代码将进入此循环。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>检查成人/猥亵内容

以下代码在给定 URL 上的图像中检查成人内容或猥亵，然后将结果输出到控制台。 有关这些术语的含义，请参阅 [图像审查的概念](./image-moderation-api.md) 指南。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>检查可见文本

以下代码在图像中检查可见的文本内容，并将结果输出到控制台。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>检查人脸

以下代码在图像中检查人脸，并将结果输出到控制台。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>使用自定义图像列表

以下代码演示如何管理用于图像审查的自定义图像列表。 如果你的平台经常收到一组你要筛选出的相同图像的实例，则此功能很有用。维护这些特定图像的列表可以提高性能。 使用 [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) 类可以创建图像列表、管理该列表中的各个图像，并将这些图像与其他图像进行比较。

创建以下文本变量用于存储此方案中使用的图像 URL。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> 此列表本身并不适当，而是要添加到代码的 `add images` 节中的非正式图像列表。


### <a name="create-an-image-list"></a>创建图像列表

添加以下代码以创建图像列表，并保存对其 ID 的引用。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>将图像添加到列表。

以下代码将所有图像添加到列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

在脚本中的其他某个位置定义 **add_images** 帮助器函数。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>获取列表中的图像

以下代码输出列表中所有图像的名称。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>更新列表详细信息

可以使用列表 ID 来更新列表的名称和说明。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>获取列表详细信息

使用以下代码可输出列表的当前详细信息。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>刷新列表索引

添加或删除图像后，必须刷新列表索引才能使用它来筛选其他图像。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>将图像与列表进行匹配

图像列表的主要功能是比较新图像，确定是否存在任何匹配项。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>从列表中删除图像

以下代码从列表中删除一个项。 在本例中，该项是与列表类别不匹配的某个图像。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>从列表中删除所有图像

使用以下代码可清除图像列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>删除图像列表

使用以下代码可删除给定的图像列表。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>创建评审

可以使用内容审查器 Python SDK 将内容馈送到[评审工具](https://contentmoderator.cognitive.microsoft.com)，使审查人员可以评审该内容。 若要详细了解评审工具，请参阅[评审工具概念指南](./review-tool-user-guide/human-in-the-loop.md)。

以下代码使用 [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) 类创建评审、检索评审 ID，并在通过评审工具的 Web 门户收到人工输入后检查评审详细信息。

### <a name="get-review-credentials"></a>获取审阅凭据

首先登录到评审工具并检索团队名称。 然后将此名称分配到代码中的相应变量。 或者，可以设置一个回调终结点用于接收有关评审活动的更新。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>创建图像评审

添加以下代码，以创建并发布给定图像 URL 的评审。 该代码将保存对审阅 ID 的引用。 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>获取评论详细信息

使用以下代码检查给定评审的详细信息。 创建评审后，可以自行转到评审工具并与内容交互。 有关如何执行此操作的信息，请参阅[评审操作方法指南](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)。 完成后，可以再次运行此代码，以检索评审过程的结果。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

如果在此方案中使用了回调终结点，应会收到采用以下格式的事件：

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `python` 命令运行应用程序。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 Python 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
>[图像审查的概念](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [什么是 Azure 内容审查器？](./overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py) 上找到此示例的源代码。