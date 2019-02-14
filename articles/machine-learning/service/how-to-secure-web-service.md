---
title: 使用 SSL 保护 Web 服务
titleSuffix: Azure Machine Learning service
description: 了解如何保护使用 Azure 机器学习服务部署的 Web 服务。 可以使用安全套接字层 (SSL) 和基于密钥的身份验证来限制对 Web 服务的访问并保护客户端提交的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 2f21c54100a46d2f6ba28d2063bea91b84ea06d4
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769315"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>通过 Azure 机器学习服务使用 SSL 保护 Web 服务

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

> [!WARNING]
> 自签名证书只能用于开发。 不应在产品中使用。 自签名证书可能会导致客户端应用程序出现问题。 有关详细信息，客户端应用程序中使用的网络库的文档。

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

## <a name="update-your-dns"></a>更新 DNS

接下来，必须更新 DNS，使其指向该 Web 服务。

+ **对于 ACI**：

  使用域名注册机构提供的工具来更新域名的 DNS 记录。 该记录必须指向服务的 IP 地址。

  可能几分钟到几小时之后客户端才能解析域名，具体取决于注册机构和为域名配置的生存时间 (TTL)。

+ **对于 AKS**：

  在 AKS 群集的“公用 IP 地址”的“配置”选项卡下更新 DNS，如下图中所示。 可以在包含 AKS 代理节点和其他网络资源的资源组下找到公用 IP 地址，它是该资源组中创建的资源类型之一。

  ![Azure 机器学习服务：使用 SSL 保护 Web 服务](./media/how-to-secure-web-service/aks-public-ip-address.png)

+ **对于 FPGA**：

目前不支持在部署到 FPGA 的服务中使用 SSL。

## <a name="next-steps"></a>后续步骤
了解如何：
+ [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
+ [在 Azure 虚拟网络中安全运行试验和推断](how-to-enable-virtual-network.md)
