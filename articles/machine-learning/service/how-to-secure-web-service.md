---
title: 使用 SSL 保护 web 服务
titleSuffix: Azure Machine Learning service
description: 了解如何通过启用 HTTPS 部署通过 Azure 机器学习服务的 web 服务安全。 HTTPS 使用传输层安全性 (TLS) 安全套接字层 (SSL) 的替换可以保护客户端中的数据。 客户端还使用 HTTPS 来验证 web 服务的标识。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 779a34800057284ce77b6d76e030ddca5fadc25f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393828"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>使用 SSL 保护通过 Azure 机器学习 web 服务

本文介绍如何通过 Azure 机器学习服务部署的 web 服务安全。

您使用[HTTPS](https://en.wikipedia.org/wiki/HTTPS)来限制对 web 服务的访问和保护客户端将提交的数据。 HTTPS 通过加密两者之间的通信，帮助客户端和 web 服务之间的通信安全。 加密使用[传输层安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 TLS 仍有时称为*安全套接字层*(SSL)，这是 TLS 的前身。

> [!TIP]
> Azure 机器学习 SDK 使用术语"SSL"来确保通信安全相关的属性。 这并不意味着不会使用你的 web 服务*TLS*。 SSL 是只是一个可识别的更常用术语。

TLS 和 SSL 都依赖*数字证书*，它使用加密和身份验证帮助。 有关数字证书的工作方式的详细信息，请参阅 Wikipedia 主题[公钥基础结构](https://en.wikipedia.org/wiki/Public_key_infrastructure)。

> [!WARNING]
> 如果在未使用 HTTPS 进行 web 服务，发往和来自该服务的数据可能对其他用户在 internet 上可见。
>
> HTTPS 还使客户端，以验证连接到服务器的真实性。 此功能可避免对客户端[拦截](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)攻击。

这是 web 服务安全的一般过程：

1. 获取域名。

2. 获取数字证书。

3. 部署或更新已启用 ssl 的 web 服务。

4. 更新 DNS，使其指向该 Web 服务。

> [!IMPORTANT]
> 如果你正在部署到 Azure Kubernetes 服务 (AKS)，可以购买自己的证书或使用由 Microsoft 提供的证书。 如果使用来自 Microsoft 的证书，您不必获取域名或 SSL 证书。 有关详细信息，请参阅[启用 SSL 并部署](#enable)本文的部分。

在保护 web 服务，贯穿于整个时存在一些细微的差别[部署目标](how-to-deploy-and-where.md)。

## <a name="get-a-domain-name"></a>获取域名

如果你尚未拥有的域名，从购买一个*域名注册机构*。 各个注册机构的过程和价格的不同。 在注册机构提供了工具来管理的域名。 使用这些工具将映射完全限定的域名 (FQDN) (例如 www\.contoso.com) 到承载 web 服务的 IP 地址。

## <a name="get-an-ssl-certificate"></a>获取 SSL 证书

有许多方法来获取的 SSL 证书 （数字证书）。 最常见方法是购买一个来自*证书颁发机构*(CA)。 无论其中获取证书，需要以下文件：

* 证书  。 该证书必须包含完整的证书链，并且它必须是"PEM 编码。"
* 密钥  。 该密钥还必须是 PEM 编码。

在请求证书时，必须提供你打算使用 web 服务的地址的 FQDN (例如，www\.contoso.com)。 标记到证书的地址和客户端使用的地址进行比较来验证 web 服务的标识。 如果这些地址不匹配，则客户端获取一条错误消息。

> [!TIP]
> 如果证书颁发机构无法提供的证书和密钥作为 PEM 编码的文件，您可以使用一个实用程序，如[OpenSSL](https://www.openssl.org/)更改的格式。

> [!WARNING]
> 使用*自签名*只能用于开发证书。 不要在生产环境中使用它们。 自签名证书可能会导致客户端应用程序出现问题。 有关详细信息，请参阅客户端应用程序使用的网络库的文档。

## <a id="enable"></a> 启用 SSL 和部署

若要部署 （或重新部署） 服务已启用 ssl，设置*ssl_enabled*为"True"只要适用的参数。 设置*ssl_certificate*的值的参数*证书*文件。 设置*ssl_key*的值*密钥*文件。

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>部署 AKS 和现场可编程门列阵 (FPGA)

  > [!NOTE]
  > 部署安全的 web 服务的可视界面时，此部分中的信息也适用。 如果您不熟悉如何使用 Python SDK，请参阅[什么是 Azure 机器学习 SDK for Python？](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

在部署到 AKS 时，可以创建新的 AKS 群集或附加现有。
  
-  如果创建新的群集，则使用 **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-)** 。
- 如果附加一个现有的群集，则使用 **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-)** 。 两者都返回一个配置对象，具有**enable_ssl**方法。

**Enable_ssl**方法可以使用由 Microsoft 提供的证书或你购买的证书。

  * 当使用来自 Microsoft 的证书时，必须使用*leaf_domain_label*参数。 此参数生成该服务的 DNS 名称。 例如，值为"myservice"创建的域名"myservice\<六个随机字符 >。\<azureregion >。 cloudapp.azure.com"，其中\<azureregion > 是包含该服务的区域。 （可选） 可以使用*overwrite_existing_domain*参数来覆盖现有*leaf_domain_label*。

    若要部署 （或重新部署） 服务已启用 ssl，设置*ssl_enabled*为"True"只要适用的参数。 设置*ssl_certificate*的值的参数*证书*文件。 设置*ssl_key*的值*密钥*文件。

    > [!IMPORTANT]
    > 当使用来自 Microsoft 的证书时，不需要购买自己的证书或域的名称。

    下面的示例演示如何创建配置，使来自 Microsoft 的 SSL 证书：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * 当你使用*购买的证书*，则使用*ssl_cert_pem_file*， *ssl_key_pem_file*，并*ssl_cname*参数。 下面的示例演示如何使用 *.pem*要创建使用购买的 SSL 证书配置文件：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

有关详细信息*enable_ssl*，请参阅[AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)并[AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)。

### <a name="deploy-on-azure-container-instances"></a>在 Azure 容器实例上部署

在部署到 Azure 容器实例时，需要提供值的与 SSL 相关的参数，如以下代码片段所示：

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

有关详细信息，请参阅[AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-)。

## <a name="update-your-dns"></a>更新 DNS

接下来，必须更新 DNS，使其指向该 Web 服务。

+ **有关容器实例：**

  使用的工具从你的域名注册机构以更新你的域名的 DNS 记录。 记录必须指向该服务的 IP 地址。

  可以有分钟或小时之前的客户端可以解析的域名，取决于注册机构和"生存时间"(TTL) 为域名配置的延迟。

+ **适用于 AKS:**

  > [!WARNING]
  > 如果您使用了*leaf_domain_label*若要使用来自 Microsoft 的证书创建服务，不要手动更新群集的 DNS 值。 应会自动设置的值。

  在更新 DNS**配置**AKS 群集的公共 IP 地址的选项卡。 （请参阅下图。）公共 IP 地址是包含 AKS 代理节点和其他网络资源的资源组下创建的资源类型。

  ![Azure 机器学习服务：使用 SSL 保护 Web 服务](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>后续步骤
了解如何：
+ [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
+ [安全地运行试验和在 Azure 虚拟网络内的推理](how-to-enable-virtual-network.md)
