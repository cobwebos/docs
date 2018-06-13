---
title: 如何使用 Azure 机器学习将模型部署为 FPGA 上的 Web 服务
description: 了解如何使用 Azure 机器学习通过在 FPGA 上运行模型部署 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33784466"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>使用 Azure 机器学习将模型部署为 FPGA 上的 Web 服务

阅读此文档，了解如何设置工作站环境和将模型部署为[现场可编程门阵列 (FPGA)](concept-accelerate-with-fpgas.md) 上的 Web 服务。 该 Web 服务使用 Project Brainwave 在 FPGA 上运行模型。

使用 FPGA 可实现超低的延迟推断，即使只有一个批数据大小。

## <a name="create-an-azure-machine-learning-model-management-account"></a>创建 Azure 机器学习模型管理帐户

1. 转到 [Azure 门户](https://aka.ms/aml-create-mma)中的模型管理帐户创建页。

2. 在该门户中，在“美国东部 2”区域创建模型管理帐户。

   ![创建模型管理帐户的屏幕图像](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. 为模型管理帐户命名、选择订阅和资源组。

   >[!IMPORTANT]
   >在“位置”部分，必须选择“美国东部 2”作为区域。  目前不支持其他区域。

4. 选择定价层（S1 够用，但也可选择 S2 和 S3）。  不支持“开发测试”层。  

5. 单击“选择”确认定价层。

6. 单击左侧 ML 模型管理上的“创建”。

## <a name="get-model-management-account-information"></a>获取模型管理帐户信息

若要获取有关模型管理帐户 (MMA) 的信息，请在 Azure 门户中单击“模型管理帐户”。

复制以下项的值：

+ 模型管理帐户名称（位于左上角）
+ 资源组名称
+ 订阅 ID
+ 位置（使用“美国东部 2”）

![模型管理帐户信息](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>设置计算机

通过以下步骤设置工作站，以便进行 FPGA 部署：

1. 下载并安装最新版本的 [Git](https://git-scm.com/downloads)。

2. 安装 [Anaconda (Python 3.6)](https://conda.io/miniconda.html)。

3. 若要下载 Anaconda 环境，请在 Git 提示中使用以下命令：

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. 若要创建环境，请打开“Anaconda 提示”（不是 Azure Machine Learning Workbench 提示），并运行以下命令：

    > [!IMPORTANT]
    > `environment.yml` 文件在前面步骤中克隆的 git 存储库中。 根据需要更改路径，使其指向工作站上的文件。

    ```
    conda env create -f environment.yml
    ```

5. 使用以下命令激活环境：

    ```
    conda activate amlrealtimeai
    ```

6. 使用以下命令启动 Jupyter Notebook 服务器：

    ```
    jupyter notebook
    ```

    此命令的输出类似于以下文本：

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > 每次运行命令时都会得到不同的令牌。

    如果浏览器没有自动打开 Jupyter Notebook，请使用之前命令返回的 HTTP URL 打开此页。

    ![Jupyter Notebook 网页的图像](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>部署模型

在 Jupyter Notebook 中，从 `notebooks/resnet50` 目录打开 `00_QuickStart.ipynb` 笔记本。 根据笔记本中的说明进行以下操作：

* 定义服务
* 部署模型
* 使用已部署的模型
* 删除已部署的服务

> [!IMPORTANT]
> 为优化延迟和吞吐量，工作站应位于终结点所在的 Azure 区域中。  目前是在美国东部 Azure 区域创建 API。

## <a name="ssltls-and-authentication"></a>SSL/TLS 和身份验证

Azure 机器学习提供 SSL 支持和基于密钥的身份验证。 这样可以限制对服务的访问权限并保护由客户端提交的数据。

> [!NOTE]
> 本部分中所述的步骤仅适用于 Azure 机器学习硬件加速模型。 有关标准的 Azure 机器学习服务，请参阅[如何在 Azure 机器学习计算上设置 SSL](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc) 文档。

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
> 请求证书时，必须提供服务计划使用的地址的完全限定域名 (FQDN)。 例如 www.contoso.com。验证服务身份时，会对比证书上标记的地址和客户端使用的地址。
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

### <a name="consuming-authenticated-services"></a>使用已经过身份验证的服务

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
