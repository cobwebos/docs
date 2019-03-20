---
title: 使用 SSL 保护 Web 服务
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务部署通过启用 HTTPS 的 web 服务安全。 HTTPS 保护通过使用传输层安全性 (TLS) 安全套接字层 (SSL) 的替换客户端提交的数据。 它还用于通过客户端验证 web 服务的标识。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1a6aa75f3d25cd88cd1edb9b2cdcfabc3b4ec8f9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103887"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>通过 Azure 机器学习服务使用 SSL 保护 Web 服务

在本文中，你将了解如何保护使用 Azure 机器学习服务部署的 Web 服务。 可以限制对 web 服务的访问和保护通过使用客户端提交的数据[超文本传输协议安全 (HTTPS)](https://en.wikipedia.org/wiki/HTTPS)。

使用 HTTPS 来保护客户端和 web 服务之间的通信进行加密两者之间的通信。 使用处理加密[传输层安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 有时这仍称为作为安全套接字层 (SSL)，这是 TLS 的前身。

> [!TIP]
> Azure 机器学习 SDK 使用术语 SSL 属性与启用安全通信。 这并不意味着您的 web 服务不使用 TLS，只是该 SSL 是为许多读者更易识别的术语。

TLS 和 SSL 都依赖__数字证书__，用于执行加密和身份验证。 数字的证书的工作方式的详细信息，请参阅 Wikipedia 条目上[公钥基础结构 (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure)。

> [!Warning]
> 如果不执行操作启用并针对您的 web 服务使用 HTTPS，发往和来自该服务的数据可能会给其他人在 internet 上可见。
>
> HTTPS 还使客户端，以验证连接到服务器的真实性。 这可避免对客户端[拦截](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)攻击。

保护新的 web 服务或现有的过程如下所示：

1. 获取域名。

2. 获取数字证书。

3. 部署或更新启用了 SSL 设置的 Web 服务。

4. 更新 DNS，使其指向该 Web 服务。

跨[部署目标](how-to-deploy-and-where.md)保护 Web 服务时，步骤稍有不同。

## <a name="get-a-domain-name"></a>获取域名

如果还没有属于自己的域名，可从域名注册机构购买一个。 不同注册机构的流程不同，价格也不同。 注册机构还会提供管理域名的工具。 这些工具用于映射的完全限定的域名 (例如 www\.contoso.com) 到承载 web 服务的 IP 地址。

## <a name="get-an-ssl-certificate"></a>获取 SSL 证书

有许多方法来获取的 SSL 证书 （数字证书）。 最常用的方式是从证书颁发机构 (CA) 购买。 无论证书来自哪里，都需要以下文件：

* 证书。 证书必须包含完整的证书链，并必须使用 PEM 编码。
* 密钥。 密钥必须使用 PEM 编码。

请求证书时，必须提供 Web 服务打算使用的地址的完全限定域名 (FQDN)。 例如，www\.contoso.com。 验证 Web 服务的身份时，会对比证书上标记的地址和客户端使用的地址。 如果两个地址不匹配，客户端将收到错误。

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

+ **在现场可编程门阵列 (FPGA) 上进行部署**

  部署到 FPGA 时，请为 SSL 相关参数提供值，如以下代码片段中所示：

    ```python
    from azureml.contrib.brainwave import BrainwaveWebservice

    deployment_config = BrainwaveWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem")
    ```

## <a name="update-your-dns"></a>更新 DNS

接下来，必须更新 DNS，使其指向该 Web 服务。

+ **对于 ACI**：

  使用域名注册机构提供的工具来更新域名的 DNS 记录。 该记录必须指向服务的 IP 地址。

  可能几分钟到几小时之后客户端才能解析域名，具体取决于注册机构和为域名配置的生存时间 (TTL)。

+ **对于 AKS**：

  在 AKS 群集的“公用 IP 地址”的“配置”选项卡下更新 DNS，如下图中所示。 可以在包含 AKS 代理节点和其他网络资源的资源组下找到公用 IP 地址，它是该资源组中创建的资源类型之一。

  ![Azure 机器学习服务：使用 SSL 保护 Web 服务](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>后续步骤
了解如何：
+ [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
+ [在 Azure 虚拟网络中安全运行试验和推断](how-to-enable-virtual-network.md)
