---
title: 快速入门：适用于 Python 的人脸客户端库
description: 本快速入门将帮助你开始使用适用于 Python 的人脸客户端库来检测、查找相似内容、识别、验证等。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: e41817e3409f79aab3fbb3d57195b6b7ff13e7af
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448210"
---
# <a name="quickstart-face-client-library-for-python"></a>快速入门：适用于 Python 的人脸客户端库

适用于 Python 的人脸客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 人脸 API 服务可让你访问用于检测和识别图像中的人脸的高级算法。

使用适用于 Python 的人脸客户端库可以：

* 在图像中检测人脸
* 查找相似人脸
* 创建和训练人员组
* 识别人脸
* 验证人脸
* 创建用于数据迁移的快照

[参考文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [包 (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [示例](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>设置

### <a name="create-a-face-azure-resource"></a>创建人脸 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建人脸资源。 你还可以：

* 免费获取在七天内有效的[试用密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，[Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上会提供此密钥。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源

获取试用订阅或资源的密钥后，请为该密钥创建名为 `FACE_SUBSCRIPTION_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。
 
### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

创建新的 Python 脚本 &mdash; 例如 *quickstart-file.py*。 在喜好的编辑器或 IDE 中打开该文件，并导入以下库。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

然后，为该资源的 Azure 终结点和密钥创建变量。 可能需要更改终结点的第一个组成部分 (`westus`)，使之与你的订阅相匹配。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

### <a name="install-the-client-library"></a>安装客户端库

可使用以下方式安装客户端库：

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>对象模型

以下类和接口将处理人脸 Python SDK 的某些主要功能。

|名称|说明|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | 此类代表使用人脸服务的授权，使用所有人脸功能时都需要用到它。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。 |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|此类处理可对人脸执行的基本检测和识别任务。 |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|此类代表已从图像中的单个人脸检测到的所有数据。 可以使用它来检索有关人脸的详细信息。|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|此类管理云中存储的 **FaceList** 构造，这些构造存储各种不同的人脸。 |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| 此类管理云中存储的 **Person** 构造，这些构造存储属于单个人员的一组人脸。|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| 此类管理云中存储的 **PersonGroup** 构造，这些构造存储各种不同的 **Person** 对象。 |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|此类管理快照功能；可以使用它来暂时保存所有基于云的人脸数据，并将这些数据迁移到新的 Azure 订阅。 |

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的人脸客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [检测图像中的人脸](#detect-faces-in-an-image)
* [查找相似人脸](#find-similar-faces)
* [创建和训练人员组](#create-and-train-a-person-group)
* [识别人脸](#identify-a-face)
* [验证人脸](#verify-faces)
* [创建用于数据迁移的快照](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 本快速入门假设你已为人脸密钥[创建了名为 `FACE_SUBSCRIPTION_KEY` 的环境变量](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

使用终结点和密钥实例化某个客户端。 使用密钥创建 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 对象，然后在终结点上使用该对象创建 [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) 对象。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>在图像中检测人脸

以下代码检测远程图像中的人脸。 它将检测到的人脸 ID 输出到控制台，并将其存储在程序内存中。 然后，它在包含多个人员的图像中检测人脸，并将其 ID 输出到控制台。 更改 [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) 方法中的参数可以返回包含每个 [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) 对象的不同信息。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

有关更多检测方案，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) 上的示例代码。

### <a name="display-and-frame-faces"></a>显示和定格人脸

下面的代码使用 DetectedFace.faceRectangle 属性将给定的图像输出到显示屏并在人脸周围绘制矩形。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![一位年轻的妇女，其脸部周围绘制了一个红色矩形](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>查找相似人脸

以下代码采用检测到的单个人脸，并搜索其他一组人脸，以找到匹配项。 找到匹配项后，它会将匹配的人脸的矩形坐标输出到控制台。 

### <a name="find-matches"></a>查找匹配项

首先，运行上一部分（[检测图像中的人脸](#detect-faces-in-an-image)）所示的代码，以保存对单个人脸的引用。 然后运行以下代码，以获取对图像组中多个人脸的引用。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

然后添加以下代码块，以查找该组中第一个人脸的实例。 若要了解如何修改此行为，请参阅 [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) 方法。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>输出匹配项

使用以下代码将匹配详细信息输出到控制台。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>创建和训练人员组

以下代码创建包含三个不同 **Person** 对象的 **PersonGroup**。 它将每个 **Person** 与一组示例图像相关联，然后进行训练以便能够识别每个人。 

### <a name="create-persongroup"></a>创建 PersonGroup

若要逐步完成此方案，需将以下图像保存到项目的根目录： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 。

此图像组包含三组人脸图像，这些图像对应于三个不同的人。 该代码定义三个 **Person** 对象，并将其关联到以 `woman`、`man` 和 `child` 开头的图像文件。

设置图像后，在脚本的顶部为要创建的 **PersonGroup** 对象定义一个标签。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

然后将以下代码添加到脚本的底部。 此代码创建一个 **PersongGroup** 和三个 **Person** 对象。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>将人脸添加到 Person

以下代码按图像前缀对图像排序、检测人脸，然后将人脸分配到每个 **Person** 对象。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>训练 PersonGroup

分配人脸后，必须训练 **PersonGroup**，使其能够识别与其每个 **Person** 对象关联的视觉特征。 以下代码调用异步 **train** 方法并轮询结果，然后将状态输出到控制台。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>识别人脸

以下代码采用包含多个人脸的图像，并尝试在该图像中查找每个人的标识。 它将每个检测到的人脸与某个 **PersonGroup**（面部特征已知的不同 **Person** 对象的数据库）进行比较。

> [!IMPORTANT]
> 若要运行此示例，必须先运行[创建和训练人员组](#create-and-train-a-person-group)中的代码。

### <a name="get-a-test-image"></a>获取测试图像

以下代码在项目根目录中查找图像 _test-image-person-group.jpg_，并检测该图像中的人脸。 可以使用用于 **PersonGroup** 管理的图像查找此图像： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>标识人脸

**identify** 方法采用检测到的人脸数组，并将其与 **PersonGroup** 进行比较。 如果检测到的某个人脸与某个人相匹配，则它会保存结果。  此代码将详细的匹配结果输出到控制台。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>验证人脸

验证操作采用某个人脸 ID 和其他人脸 ID 或 Person 对象，并确定它们是否属于同一个人  。

以下代码检测两个源图像中的人脸，然后针对从目标图像检测到的人脸来验证它们。

### <a name="get-test-images"></a>获取测试图像

以下代码块声明将指向验证操作的源和目标图像的变量。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>检测人脸进行验证

以下代码检测源和目标图像中的人脸并将其保存到变量中。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>获取验证结果

以下代码将每个源图像与目标图像进行比较并打印出一条消息，指示它们是否属于同一个人。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>创建用于数据迁移的快照

利用快照功能，可将已保存的人脸数据（例如训练的 **PersonGroup**）移到不同的 Azure 认知服务人脸订阅。 例如，如果你使用免费试用订阅创建了一个 **PersonGroup** 对象，现在想要将其迁移到付费订阅，则可以使用此功能。 有关快照功能的大致概述，请参阅[迁移人脸数据](../Face-API-How-to-Topics/how-to-migrate-face-data.md)。

此示例将迁移你在[创建和训练人员组](#create-and-train-a-person-group)中创建的 **PersonGroup**。 可以先完成该部分，或者使用你自己的人脸数据构造。

### <a name="set-up-target-subscription"></a>设置目标订阅

首先，必须有另一个已包含人脸资源的 Azure 订阅；可以遵循[设置](#setting-up)部分中的步骤做好此准备。 

然后在脚本顶部附近创建以下变量。 还需要为 Azure 帐户的订阅 ID 以及新（目标）帐户的密钥、终结点和订阅 ID 创建新的环境变量。 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>对目标客户端进行身份验证

稍后需要在脚本中将当前客户端对象保存为源客户端，然后对目标订阅的新客户端对象进行身份验证。 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>使用快照

剩余的快照操作将在异步函数中进行。 

1. 第一步是**创建**快照，以将原始订阅的人脸数据保存到临时云位置。 此方法返回用于查询操作状态的 ID。

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. 接下来，不断查询该 ID，直到操作完成。

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    此代码使用应单独定义的 `wait_for_operation` 函数：

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. 返回到异步函数。 使用 **apply** 操作将人脸数据写入目标订阅。 此方法也会返回一个 ID。

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. 再次使用 `wait_for_operation` 函数查询该 ID，直到操作完成。

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

完成这些步骤后，即可从新的（目标）订阅访问你的人脸数据构造。

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `python` 命令运行应用程序。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

如果你在本快速入门中创建了 **PersonGroup** 并想要删除它，请在脚本中运行以下代码：

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

如果你在本快速入门中使用快照功能迁移了数据，则还需要删除保存到目标订阅的 **PersonGroup**。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用适用于 Python 的人脸库来执行基本任务。 接下来，请在参考文档中详细了解该库。

> [!div class="nextstepaction"]
> [人脸 API 参考 (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [什么是人脸 API？](../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) 上找到此示例的源代码。