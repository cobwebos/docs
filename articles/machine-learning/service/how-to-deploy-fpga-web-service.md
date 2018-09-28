---
title: 使用 Azure 机器学习将模型部署为 FPGA 上的 Web 服务
description: 了解如何使用 Azure 机器学习通过在 FPGA 上运行模型部署 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 09/24/2018
ms.openlocfilehash: ee67585a523ab96b1442d9eee3e9dfd55a758d32
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971478"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>使用 Azure 机器学习将模型部署为 FPGA 上的 Web 服务

可在[现场可编程门阵列 (FPGA)](concept-accelerate-with-fpgas.md) 上将模型部署为 Web 服务。  使用 FPGA 可实现超低的延迟推断，即使只有一个批数据大小。   

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 已安装 Azure 机器学习工作区以及用于 Python 的 Azure 机器学习 SDK。 了解如何通过[如何配置开发环境](how-to-configure-environment.md)一文满足这些先决条件。
 
  - 工作区需要位于美国东部 2 区域。

  - 安装附加 contrib：

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>创建和部署模型
创建一个管道来预处理输入图像，在 FPGA 上使用 ResNet 50 对其进行特征化，然后通过在 ImageNet 数据集上训练的分类器来运行这些功能。

遵照说明操作：

* 定义模型管道
* 部署模型
* 使用已部署的模型
* 删除已部署的服务

> [!IMPORTANT]
> 为优化延迟和吞吐量，客户端应位于终结点所在的同一 Azure 区域中。  目前是在美国东部 Azure 区域创建 API。

### <a name="get-the-notebook"></a>获取 Notebook

为方便起见，本教程以 Jupyter 笔记本的形式提供。 使用以下任一方法运行 `project-brainwave/project-brainwave-quickstart.ipynb` 笔记本：

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

### <a name="preprocess-image"></a>预处理图像
管道的第一个阶段是预处理图像。

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```
### <a name="add-featurizer"></a>添加特征化器
初始化模型并下载 ResNet50 的量化版本的 TensorFlow 检查点以用作特征化器。

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>添加分类器
已在 ImageNet 数据集上训练该分类器。

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>创建服务定义
已经定义了在服务上运行的图像预处理、特征化器和分类器，现可创建服务定义。 服务定义是从部署到 FPGA 服务的模型生成的一组文件。 服务定义包括管道。 管道是一系列按顺序运行的阶段。  支持 TensorFlow 阶段、Keras 阶段和 BrainWave 阶段。  这些阶段在服务上按顺序运行，将每个阶段的输出输入到下个阶段。

要创建 TensorFlow 阶段，请指定包含图形的会话（在本例中使用默认图形）以及此阶段的输入和输出张量。  此信息用于保存图表，以便在服务上运行。

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>部署模型
通过服务定义创建服务。  工作区需要位于美国东部 2 位置。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>测试服务
要将图像发送到 API 并测试响应，请将映射从输出类 ID 添加到 ImageNet 类名称。

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

调用服务，并将下面的“your-image.jpg”文件名替换为计算机的图像。 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>清理服务
删除服务。

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>保护 FPGA Web 服务

在 FPGA 上运行的 Azure 机器学习模型提供 SSL 支持和基于密钥的身份验证。 这样可以限制对服务的访问权限并保护由客户端提交的数据。

> [!IMPORTANT]
> 只有提供了 SSL 证书和密钥的服务才启用身份验证。 
>
> 如果未启用 SSL，Internet 上的任何用户都能调用该服务。
>
> 如果启用了 SSL，则还需要提供身份验证密钥才能访问该服务。

SSL 会对在客户端和服务之间发送的数据进行加密。 客户端还可用它来验证服务器的身份。

可以在部署服务时启用 SSL，也可以通过更新已部署的服务来启用 SSL。 步骤相同：

1. 获取域名。

2. 获取 SSL 证书。

3. 在启用 SSL 的情况下部署或更新服务。

4. 更新 DNS，使其指向该服务。

### <a name="acquire-a-domain-name"></a>获取域名

如果还没有属于自己的域名，可从域名注册机构购买一个。 不同注册机构的流程不同，价格也不同。 注册机构还会提供管理域名的工具。 这些工具可用于将完全限定域名（如 www.contoso.com）映射到托管服务的 IP 地址。

### <a name="acquire-an-ssl-certificate"></a>获取 SSL 证书

获取 SSL 证书有多种方式。 最常用的方式是从证书颁发机构 (CA) 购买。 无论证书来自哪里，都需要以下文件：

* 证书。 证书必须包含完整的证书链，并必须使用 PEM 编码。
* 密钥。 密钥必须使用 PEM 编码。

> [!TIP]
> 如果证书颁发机构不能以 PEM 编码文件的方式提供证书和密钥，可使用 [OpenSSL](https://www.openssl.org/) 等实用程序来更改格式。

> [!IMPORTANT]
> 自签名证书只能用于开发。 不应在产品中使用。
>
> 如果使用的是自签名证书，请参阅[使用具备自签名证书的服务](#self-signed)部分了解具体说明。

> [!WARNING]
> 请求证书时，必须提供服务计划使用的地址的完全限定域名 (FQDN)。 例如 www.contoso.com。 验证服务身份时，会对比证书上标记的地址和客户端使用的地址。
>
> 如果两个地址不匹配，客户端将收到错误。 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>在启用 SSL 的情况下部署或更新服务

若要在部署服务时启用 SSL，请将 `ssl_enabled` 参数设置为 `True`。 将 `ssl_certificate` 参数设置为证书文件的值，将 `ssl_key` 设置为密钥文件的值。 以下示例演示了如何在部署服务时启用 SSL：

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

`create_service` 操作的响应包含服务的 IP 地址。 将 DNS 名称映射到服务的 IP 地址时需使用该 IP 地址。

该响应还包含使用服务所需的主要密钥和辅助密钥。

### <a name="update-your-dns-to-point-to-the-service"></a>更新 DNS，使其指向服务

使用域名注册机构提供的工具来更新域名的 DNS 记录。 该记录必须指向服务的 IP 地址。

> [!NOTE]
> 可能几分钟到几小时之后客户端才能解析域名，具体取决于注册机构和为域名配置的生存时间 (TTL)。

### <a name="consume-authenticated-services"></a>使用已经过身份验证的服务

以下示例演示如何通过 Python 和 C# 使用已经过身份验证的服务：

> [!NOTE]
> 使用创建服务时返回的主要或辅助密钥替换 `authkey`。

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

其他 gRPC 客户端可通过设置授权标头对请求进行身份验证。 常规方法是创建一个结合了 `SslCredentials` 和 `CallCredentials` 的 `ChannelCredentials` 对象。 该对象将添加到请求的授权标头。 如需深入了解如何实现对特定标头的支持，请参阅 [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html)。

以下示例演示如何在 C# 和 Go 中设置标头：

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>使用具备自签名证书的服务

可通过两种方式让客户端实现对使用自签名证书进行安全保护的服务器进行身份验证：

* 在客户端系统上，将客户端系统上的 `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 环境变量设置为指向证书文件。

* 构造 `SslCredentials` 对象时，将证书文件的内容传递给构造函数。

两种方法都会让 gRPC 将证书用作根证书。

> [!IMPORTANT]
> gRPC 不接受不受信任的证书。 使用不受信任的证书将失败，并显示 `Unavailable` 状态代码。 失败的详细信息中包含 `Connection Failed`。
