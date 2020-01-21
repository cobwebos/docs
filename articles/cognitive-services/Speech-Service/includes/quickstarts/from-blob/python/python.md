---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: bfecfa1918d2e9199971b2f9738530dc1b4e3c4c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942815"
---
## <a name="prerequisites"></a>必备条件

在开始之前，请务必：

> [!div class="checklist"]
> * [设置开发环境](../../../../quickstarts/setup-platform.md)
> * [创建空示例项目](../../../../quickstarts/create-project.md)
> * [创建 Azure 语音资源](../../../../get-started.md)
> * [将源文件上传到 Azure blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>下载并安装 API 客户端库

若要执行该示例，需要为通过 [Swagger](https://swagger.io) 生成的 REST API 生成 Python 库。

请按照以下步骤进行安装：

1. 转到  https://editor.swagger.io 。
1. 单击“文件”，然后单击“导入 URL”   。
1. 输入 Swagger URL，包括语音服务订阅的区域：`https://<your-region>.cris.ai/docs/v2.0/swagger`。
1. 单击“生成客户端”，然后选择“Python”   。
1. 保存客户端库。
1. 将下载的 python-client-generated.zip 提取到文件系统中的某个位置。
1. 使用 pip：`pip install path/to/package/python-client` 在 Python 环境中安装提取的 python-client 模块。
1. 已安装的名为 `swagger_client` 的包。 可以使用命令 `python -c "import swagger_client"` 来检查安装是否正常工作。

> [!NOTE]
> 由于 `swagger_client`Swagger 自动生成中的已知 bug](https://github.com/swagger-api/swagger-codegen/issues/7541)，导入 `swagger_client` 包时可能会遇到错误。
> 这些错误可以通过从已安装的包中删除文件 中包含以下内容的行
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> 和文件 `swagger_client/models/model.py` 中包含以下内容的行
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> 来进行修复。 错误消息将告知安装这些文件的位置。

## <a name="install-other-dependencies"></a>安装其他依赖项

该示例使用 `requests` 库。 可以通过以下命令进行安装

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手

添加一些代码作为项目的框架。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>创建和配置 Http 客户端
首先，我们需要具有正确的基本 URL 和身份验证集的 Http 客户端。
将此代码插入 `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>生成听录请求
接下来，我们将生成听录请求。 将此代码添加到 `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>发送请求并查看其状态
现在我们将请求发布到语音服务并检查初始响应代码。 此响应代码将仅指示服务是否已收到请求。 该服务将在响应标头中返回一个 URL，这是它将存储听录状态的位置。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>等待听录完成
由于服务以异步方式处理听录，因此需要时常轮询其状态。 我们每 5 秒查看一次。

我们将枚举此语音服务资源正在处理的所有听录，并查找我们创建的听录。

下面是一个轮询代码，其中显示了除成功完成之外的所有状态，我们会在下一步完成该操作。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>显示听录结果
服务成功完成听录后，结果将存储在可从状态响应中获取的其他 URL 中。

此处我们获取并显示了 JSON 结果。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>查看代码
此时，代码应如下所示：（我们已向此版本添加了一些注释）[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>生成并运行应用

现在，可以使用语音服务构建应用并测试语音识别。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
