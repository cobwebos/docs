---
title: 人脸 Go 客户端库快速入门
description: 适用于 Go 的人脸客户端库入门。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 4a96f0e887bb04aea6d451e08bd5d26d1cc6edca
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82587891"
---
适用于 Go 的人脸客户端库入门。 请按照以下步骤安装库并试用基本任务的示例。 通过人脸服务，可以访问用于检测和识别图像中的人脸的高级算法。

使用适用于 Go 的人脸服务客户端库可以：

* [检测图像中的人脸](#detect-faces-in-an-image)
* [查找相似人脸](#find-similar-faces)
* [创建和训练人员组](#create-and-train-a-person-group)
* [识别人脸](#identify-a-face)
* [创建用于数据迁移的快照](#take-a-snapshot-for-data-migration)

[参考文档](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [库源代码](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK 下载](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Go](https://golang.org/dl/)

## <a name="set-up"></a>设置

### <a name="create-a-face-azure-resource"></a>创建人脸 Azure 资源 

通过创建 Azure 资源开始使用人脸服务。 选择适合你的资源类型：

* [试用资源](https://azure.microsoft.com/try/cognitive-services/#decision)（不需要 Azure 订阅）： 
    * 有效期为七天，免费。 注册后，试用密钥和终结点将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上可用。 
    * 如果你想尝试人脸服务，但没有 Azure 订阅，则这是一个很好的选项。
* 一个[人脸服务资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)：
    * 在删除资源前，可通过 Azure 门户使用。
    * 使用免费定价层试用该服务，稍后升级到用于生产的付费层。
* 一个[多服务资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)：
    * 在删除资源前，可通过 Azure 门户使用。  
    * 在多个认知服务中对应用程序使用相同的密钥和终结点。

### <a name="create-an-environment-variable"></a>创建环境变量

>[!NOTE]
> 在 2019 年 7 月 1 日之后创建的非试用资源的终结点使用如下所示的自定义子域格式。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

从创建的资源使用密钥和终结点，创建两个用于身份验证的环境变量：
* `FACE_SUBSCRIPTION_KEY` - 用于验证请求的资源密钥。
* `FACE_ENDPOINT` - 用于发送 API 请求的资源终结点。 它将如下所示： 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

使用操作系统的说明。
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

添加环境变量后，请重启控制台窗口。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。

#### <a name="macos"></a>[macOS](#tab/unix)

编辑 `.bash_profile`，然后添加环境变量：

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source .bash_profile`，使更改生效。
***

### <a name="create-a-go-project-directory"></a>创建 Go 项目目录

在控制台窗口（cmd、PowerShell、终端、Bash）中，为 Go 项目创建一个名为 `my-app` 的新工作区并导航到该工作区。

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

工作区包含三个文件夹：

* **src** - 此目录包含源代码和包。 使用 `go get` 命令安装的任何包都驻留在此文件夹中。
* **pkg** - 此目录包含编译的 Go 包对象。 这些文件的扩展名为 `.a`。
* **bin** - 此目录包含运行 `go install` 时创建的二进制可执行文件。

> [!TIP]
> 若要了解有关 Go 工作区结构的详细信息，请参阅 [Go 语言文档](https://golang.org/doc/code.html#Workspaces)。 本指南包含有关设置 `$GOPATH` 和 `$GOROOT` 的信息。

### <a name="install-the-client-library-for-go"></a>安装适用于 Go 的客户端库

接下来，安装适用于 Go 的客户端库：

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

或者，如果使用 dep，则在存储库中运行：

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>创建 Go 应用程序

接下来，在 src 目录中创建名为 `sample-app.go` 的文件  ：

```bash
cd src
touch sample-app.go
```

在首选 IDE 或文本编辑器中打开 `sample-app.go`。 然后添加包名称并导入以下库：

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

接下来，开始添加代码以执行不同的人脸服务操作。

## <a name="object-model"></a>对象模型

以下类和接口用于处理人脸服务 Go 客户端库的某些主要功能。

|名称|说明|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | 此类代表使用人脸服务的授权，使用所有人脸功能时都需要用到它。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。 |
|[客户端](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|此类处理可对人脸执行的基本检测和识别任务。 |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|此类代表已从图像中的单个人脸检测到的所有数据。 可以使用它来检索有关人脸的详细信息。|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|此类管理云中存储的 **FaceList** 构造，这些构造存储各种不同的人脸。 |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| 此类管理云中存储的 **Person** 构造，这些构造存储属于单个人员的一组人脸。|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| 此类管理云中存储的 **PersonGroup** 构造，这些构造存储各种不同的 **Person** 对象。 |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|此类管理快照功能。 可以使用它来暂时保存所有基于云的人脸数据，并将这些数据迁移到新的 Azure 订阅。 |

## <a name="code-examples"></a>代码示例

这些代码示例演示如何使用适用于 Go 的人脸服务客户端库来完成基本任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [检测图像中的人脸](#detect-faces-in-an-image)
* [查找相似人脸](#find-similar-faces)
* [创建和训练人员组](#create-and-train-a-person-group)
* [识别人脸](#identify-a-face)
* [创建用于数据迁移的快照](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE] 
> 本快速入门假设已经为人脸密钥和终结点（分别名为 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT`）[创建了环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

创建 **main** 函数，并在其中添加以下代码，以使用终结点和密钥实例化客户端。 使用密钥创建 **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** 对象，然后在终结点上使用该对象创建 **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** 对象。 此代码还将实例化一个上下文对象，创建客户端对象时需要该上下文对象。 它还会定义一个远程位置，可在其中找到本快速入门中的一些示例图像。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>在图像中检测人脸

在 **main** 方法中添加以下代码。 此代码定义一个远程示例图像，并指定要从该图像中提取哪些人脸特征。 它还会指定要使用哪个 AI 模型从检测到的人脸中提取数据。 有关这些选项的信息，请参阅[指定识别模型](../../Face-API-How-to-Topics/specify-recognition-model.md)。 最后， **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** 方法针对图像执行人脸检测操作，并将结果保存到程序内存中。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>显示检测到的人脸数据

下一个代码块采用 **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** 对象数组中的第一个元素，并将其特性输出到控制台。 如果使用了包含多个人脸的图像，则应改为迭代该数组。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>查找相似人脸

以下代码采用检测到的单个人脸（源），并搜索其他一组人脸（目标），以找到匹配项。 找到匹配项后，它会将匹配的人脸的 ID 输出到控制台。

### <a name="detect-faces-for-comparison"></a>检测人脸以进行比较

首先，保存对[检测图像中的人脸](#detect-faces-in-an-image)部分中检测到的人脸的引用。 此人脸将是源。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

然后输入以下代码，以检测不同图像中的一组人脸。 这些人脸将是目标。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>查找匹配项

以下代码使用 **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** 方法来查找与源人脸匹配的所有目标人脸。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>输出匹配项

以下代码将匹配详细信息输出到控制台。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>创建和训练人员组

若要逐步完成此方案，需将以下图像保存到项目的根目录： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 。

此图像组包含三组单一人脸图像（对应于三个不同的人）。 该代码定义三个 **PersonGroup Person** 对象，并将其关联到以 `woman`、`man` 和 `child` 开头的图像文件。

### <a name="create-persongroup"></a>创建 PersonGroup

下载图像后，请将以下代码添加到 **main** 方法的底部。 此代码对 **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** 对象进行身份验证，然后使用它来定义新的 **PersonGroup**。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>创建 PersonGroup Person

下一个代码块对 **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** 进行身份验证，并使用它来定义三个新的 **PersonGroup Person** 对象。 其中的每个对象表示图像集中的一个人。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>将人脸添加到 Person

以下代码按图像前缀排序图像，检测人脸，并根据图像文件名将人脸分配到每个相关的 **PersonGroup Person** 对象。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>训练 PersonGroup

分配人脸后，请训练 **PersonGroup**，使其能够识别与其每个 **Person** 对象关联的视觉特征。 以下代码调用异步 **train** 方法并轮询结果，然后将状态输出到控制台。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>识别人脸

以下代码采用包含多个人脸的图像，并尝试在该图像中查找每个人的标识。 它将每个检测到的人脸与某个 **PersonGroup**（面部特征已知的不同 **Person** 对象的数据库）进行比较。

> [!IMPORTANT]
> 若要运行此示例，必须先运行[创建和训练人员组](#create-and-train-a-person-group)中的代码。

### <a name="get-a-test-image"></a>获取测试图像

以下代码在项目根目录中查找图像 _test-image-person-group.jpg_，并将其载入程序内存。 可以在[创建和训练人员组](#create-and-train-a-person-group)中使用的图像所在的同一个存储库中找到此图像： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>检测测试图像中的源人脸

下一个代码块针对测试图像执行普通的人脸检测，以检索所有人脸并将其保存到数组中。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>标识人脸

**[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** 方法采用检测到的人脸的数组，并将其与给定的 **PersonGroup**（已在前一部分定义并训练）进行比较。 如果检测到的某个人脸与组中的某个人相匹配，则它会保存结果。 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

然后，此代码将详细的匹配结果输出到控制台。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>验证人脸

验证操作采用某个人脸 ID 和其他人脸 ID 或 Person 对象，并确定它们是否属于同一个人  。

以下代码检测两个源图像中的人脸，然后根据目标图像中检测到的人脸来验证源图像中的每个人脸。

### <a name="get-test-images"></a>获取测试图像

以下代码块声明将指向验证操作的目标和源图像的变量。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>检测人脸进行验证

以下代码检测源和目标图像中的人脸并将其保存到变量中。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>获取验证结果

以下代码将每个源图像与目标图像进行比较并打印出一条消息，指示它们是否属于同一个人。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>创建用于数据迁移的快照

利用快照功能，可将已保存的人脸数据（例如训练的 **PersonGroup**）移到不同的 Azure 认知服务人脸订阅。 例如，如果你使用免费试用订阅创建了一个 **PersonGroup** 对象，现在想要将其迁移到付费订阅，则可以使用此功能。 有关快照功能的大致概述，请参阅[迁移人脸数据](../../Face-API-How-to-Topics/how-to-migrate-face-data.md)。

此示例将迁移你在[创建和训练人员组](#create-and-train-a-person-group)中创建的 **PersonGroup**。 可以先完成该部分，或者使用你自己的人脸数据构造。

### <a name="set-up-target-subscription"></a>设置目标订阅

首先，必须有另一个已包含人脸资源的 Azure 订阅；为此，可以重复[设置](#set-up)部分中的步骤。 

然后在 **main** 方法的顶部附近创建以下变量。 还需要为 Azure 帐户的订阅 ID 以及新（目标）帐户的密钥、终结点和订阅 ID 创建新的环境变量。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

然后，将订阅 ID 值放入某个数组，以便在后续步骤中使用。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>对目标客户端进行身份验证

稍后需要在脚本中将原始客户端对象保存为源客户端，并对目标订阅的新客户端对象进行身份验证。 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>生成快照

下一步是使用 **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** 创建快照，以将原始订阅的人脸数据保存到临时云位置。 此方法返回用于查询操作状态的 ID。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

接下来，不断查询该 ID，直到操作完成。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>应用快照

使用 **[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** 操作将新上传的人脸数据写入目标订阅。 此方法也会返回一个 ID。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

同样，请不断查询该 ID，直到操作完成。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

完成这些步骤后，即可从新的（目标）订阅访问你的人脸数据构造。

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `go run [arguments]` 命令运行 Go 应用程序。

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

如果你在本快速入门中创建了一个 **PersonGroup** 但想要删除它，请调用 **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** 方法。 如果你在本快速入门中使用快照功能迁移了数据，则还需要删除保存到目标订阅的 **PersonGroup**。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何使用适用于 Go 的人脸库来执行基本任务。 接下来，请在参考文档中详细了解该库。

> [!div class="nextstepaction"]
> [人脸 API 参考 (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [什么是人脸服务？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go) 上找到此示例的源代码。
