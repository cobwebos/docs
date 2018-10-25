---
title: 使用 SSL 保护 Azure 机器学习 Web 服务
description: 了解如何保护使用 Azure 机器学习服务部署的 Web 服务。 可以使用安全套接字层 (SSL) 和基于密钥的身份验证来限制对 Web 服务的访问并保护客户端提交的数据。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 885d867d0733ef923d327d8d6a36fc1588fd4961
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801006"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>使用 SSL 保护 Azure 机器学习 Web 服务

在本文中，你将了解如何保护使用 Azure 机器学习服务部署的 Web 服务。 可以使用安全套接字层 (SSL) 和基于密钥的身份验证来限制对 Web 服务的访问并保护客户端提交的数据。

> [!Warning]
> 如果未启用 SSL，Internet 上的任何用户都能调用该 Web 服务。

SSL 会对在客户端和 Web 服务之间发送的数据进行加密。 客户端还可用它来验证服务器的身份。 只有提供了 SSL 证书和密钥的服务才启用身份验证。  如果启用了 SSL，则还需要提供身份验证密钥才能访问该 Web 服务。

无论是部署启用了 SSL 的 Web 服务，还是为现有的已部署 Web 服务启用 SSL，步骤都是相同的：

1. 获取域名。

2. 获取 SSL 证书。

3. 部署或更新启用了 SSL 设置的 Web 服务。

4. 更新 DNS，使其指向该 Web 服务。

跨[部署目标](how-to-deploy-and-where.md)保护 Web 服务时，步骤稍有不同。 

## <a name="get-a-domain-name"></a>获取域名

如果还没有属于自己的域名，可从域名注册机构购买一个。 不同注册机构的流程不同，价格也不同。 注册机构还会提供管理域名的工具。 这些工具可用于将完全限定域名（如 www.contoso.com）映射到托管着你的 Web 服务的 IP 地址。

## <a name="get-an-ssl-certificate"></a>获取 SSL 证书

获取 SSL 证书有多种方式。 最常用的方式是从证书颁发机构 (CA) 购买。 无论证书来自哪里，都需要以下文件：

* 证书。 证书必须包含完整的证书链，并必须使用 PEM 编码。
* 密钥。 密钥必须使用 PEM 编码。

请求证书时，必须提供 Web 服务打算使用的地址的完全限定域名 (FQDN)。 例如 www.contoso.com。 验证 Web 服务的身份时，会对比证书上标记的地址和客户端使用的地址。 如果两个地址不匹配，客户端将收到错误。 

> [!TIP]
> 如果证书颁发机构不能以 PEM 编码文件的方式提供证书和密钥，可使用 [OpenSSL](https://www.openssl.org/) 等实用程序来更改格式。

> [!IMPORTANT]
> 自签名证书只能用于开发。 不应在产品中使用。 如果使用的是自签名证书，请参阅[使用具备自签名证书的 Web 服务](#self-signed)部分了解具体说明。


## <a name="enable-ssl-and-deploy"></a>启用 SSL 并进行部署

若要部署（或重新部署）启用了 SSL 的服务，请根据情况将 `ssl_enabled` 参数设置为 `True`。 将 `ssl_certificate` 参数设置为证书文件的值，将 `ssl_key` 设置为密钥文件的值。 

+ **在 Azure Kubernetes 服务 (AKS) 上进行部署**
  
  预配 AKS 群集时，请为 SSL 相关参数提供值，如以下代码片段中所示：

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **在 Azure 容器实例 (ACI) 上进行部署**
 
  部署到 ACI 时，请为 SSL 相关参数提供值，如以下代码片段中所示：

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **在现场可编程门阵列 (FPGA) 上进行部署**

  `create_service` 操作的响应包含服务的 IP 地址。 将 DNS 名称映射到服务的 IP 地址时需使用该 IP 地址。 该响应还包含使用服务所需的主要密钥和辅助密钥。 请为 SSL 相关参数提供值，如以下代码片段中所示：

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

## <a name="update-your-dns"></a>更新 DNS

接下来，必须更新 DNS，使其指向该 Web 服务。

+ **对于 ACI 和 FPGA**：  

  使用域名注册机构提供的工具来更新域名的 DNS 记录。 该记录必须指向服务的 IP 地址。  

  可能几分钟到几小时之后客户端才能解析域名，具体取决于注册机构和为域名配置的生存时间 (TTL)。

+ **对于 AKS**： 

  在 AKS 群集的“公用 IP 地址”的“配置”选项卡下更新 DNS，如下图中所示。 可以在包含 AKS 代理节点和其他网络资源的资源组下找到公用 IP 地址，它是该资源组中创建的资源类型之一。

  ![Azure 机器学习服务：使用 SSL 保护 Web 服务](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="consume-authenticated-services"></a>使用已经过身份验证的服务

### <a name="how-to-consume"></a>如何使用 
+ **对于 ACI 和 AKS**： 

  对于 ACI 和 AKS Web 服务，请通过以下文章了解如何使用 Web 服务：
  + [如何部署到 ACI](how-to-deploy-to-aci.md)

  + [如何部署到 AKS](how-to-deploy-to-aks.md)

+ **对于 FPGA**：  

  以下示例演示了如何通过 Python 和 C# 使用已经过身份验证的 FPGA 服务：
  将 `authkey` 替换为使用部署服务时返回的主要或辅助密钥。

  Python 示例：
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  C# 示例：
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>设置授权标头
其他 gRPC 客户端可通过设置授权标头对请求进行身份验证。 常规方法是创建一个结合了 `SslCredentials` 和 `CallCredentials` 的 `ChannelCredentials` 对象。 该对象将添加到请求的授权标头。 如需深入了解如何实现对特定标头的支持，请参阅 [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html)。

以下示例演示如何在 C# 和 Go 中设置标头：

+ 使用 C# 设置标头：
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ 使用 Go 设置标头：
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

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>使用具备自签名证书的服务

可通过两种方式让客户端实现对使用自签名证书进行安全保护的服务器进行身份验证：

* 在客户端系统上，将客户端系统上的 `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 环境变量设置为指向证书文件。

* 构造 `SslCredentials` 对象时，将证书文件的内容传递给构造函数。

两种方法都会让 gRPC 将证书用作根证书。

> [!IMPORTANT]
> gRPC 不接受不受信任的证书。 使用不受信任的证书将失败，并显示 `Unavailable` 状态代码。 失败的详细信息中包含 `Connection Failed`。
