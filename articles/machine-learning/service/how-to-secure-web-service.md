---
title: 使用 SSL 保护 web 服务
titleSuffix: Azure Machine Learning
description: 了解如何通过启用 HTTPS 来保护通过 Azure 机器学习部署的 web 服务。 HTTPS 使用传输层安全性（TLS）来保护客户端中的数据，这是一种用于安全套接字层（SSL）的替换。 客户端还使用 HTTPS 来验证 web 服务的身份。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: ce60806c26359ae682f5ab468e4f4265d3572c87
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034381"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>通过 SSL 使用 SSL 来保护 web 服务 Azure 机器学习

本文介绍如何保护通过 Azure 机器学习部署的 web 服务。

使用[HTTPS](https://en.wikipedia.org/wiki/HTTPS)限制对 web 服务的访问并保护客户端提交的数据。 HTTPS 通过对两者之间的通信进行加密来帮助保护客户端和 web 服务之间的通信。 加密使用[传输层安全性（TLS）](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 TLS 有时仍称为*安全套接字层*（SSL），这是 tls 的前身。

> [!TIP]
> Azure 机器学习 SDK 对与安全通信相关的属性使用术语 "SSL"。 这并不意味着 web 服务不使用*TLS*。 SSL 只是一种更常见的公认术语。

TLS 和 SSL 均依赖于*数字证书*，这有助于加密和身份验证。 有关数字证书工作方式的详细信息，请参阅维基百科主题[公钥基础结构](https://en.wikipedia.org/wiki/Public_key_infrastructure)。

> [!WARNING]
> 如果没有为 web 服务使用 HTTPS，则发送到服务或从服务发送的数据可能对 internet 上的其他人可见。
>
> HTTPS 还允许客户端验证它连接到的服务器的真实性。 此功能保护客户端免受[中间人](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)攻击。

这是保护 web 服务的一般过程：

1. 获取域名。

2. 获取数字证书。

3. 部署或更新启用了 SSL 的 web 服务。

4. 更新 DNS，使其指向该 Web 服务。

> [!IMPORTANT]
> 如果要部署到 Azure Kubernetes Service （AKS），可以购买自己的证书或使用 Microsoft 提供的证书。 如果使用 Microsoft 的证书，则无需获取域名或 SSL 证书。 有关详细信息，请参阅本文的[启用 SSL 和部署](#enable)部分。

跨[部署目标](how-to-deploy-and-where.md)保护 web 服务时，略有不同。

## <a name="get-a-domain-name"></a>获取域名

如果还没有域名，请从*域名注册机构*购买一个。 此过程和价格在注册机构之间有所不同。 注册机构提供管理域名的工具。 使用这些工具可以将完全限定的域名（FQDN）（例如 www\.contoso.com）映射到托管 web 服务的 IP 地址。

## <a name="get-an-ssl-certificate"></a>获取 SSL 证书

有多种方法可获取 SSL 证书（数字证书）。 最常见的是从*证书颁发机构*（CA）购买证书。 无论你获取证书的位置如何，你都需要以下文件：

* 证书。 证书必须包含完整的证书链，并且必须是 "PEM 编码的"。
* 密钥。 此密钥还必须为 PEM 编码。

请求证书时，必须提供你计划用于 web 服务的地址的 FQDN （例如，www\.contoso.com）。 与客户端使用的地址相比较的地址与客户端使用的地址相比较，以验证 web 服务的身份。 如果这些地址不匹配，则客户端将收到一条错误消息。

> [!TIP]
> 如果证书颁发机构无法提供作为 PEM 编码文件的证书和密钥，则可以使用[OpenSSL](https://www.openssl.org/)之类的实用工具来更改格式。

> [!WARNING]
> 仅将*自签名*证书用于开发。 不要在生产环境中使用它们。 自签名证书可能会导致客户端应用程序出现问题。 有关详细信息，请参阅客户端应用程序使用的网络库的文档。

## <a id="enable"></a>启用 SSL 并部署

若要部署（或重新部署）启用了 SSL 的服务，请将*ssl_enabled*参数设置为 "True" （无论适用）。 将*ssl_certificate*参数设置为*证书*文件的值。 将*ssl_key*设置为*密钥*文件的值。

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>在 AKS 和现场可编程入口阵列（FPGA）上部署

  > [!NOTE]
  > 此部分中的信息也适用于为视觉对象界面部署安全 web 服务的情况。 如果不熟悉如何使用 Python SDK，请参阅[适用于 python 的 AZURE 机器学习 SDK 是什么？](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

部署到 AKS 时，可以创建新的 AKS 群集或附加现有群集。 有关创建或附加群集的详细信息，请参阅[将模型部署到 Azure Kubernetes 服务群集](how-to-deploy-azure-kubernetes-service.md)。
  
-  如果创建新群集，请使用 **[provisionining_configuration （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** 。
- 如果附加现有群集，则可以使用 **[AksCompute. attach_configuration （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** 。 两者都返回具有**enable_ssl**方法的配置对象。

**Enable_ssl**方法可使用 Microsoft 提供的证书或你购买的证书。

  * 使用 Microsoft 的证书时，必须使用*leaf_domain_label*参数。 此参数生成服务的 DNS 名称。 例如，值为 "myservice" 时，将创建域名 "myservice\<六个随机字符 >。\<azureregion >. p p. "，其中\<azureregion > 是包含该服务的区域。 或者，可以使用*overwrite_existing_domain*参数覆盖现有的*leaf_domain_label*。

    若要部署（或重新部署）启用了 SSL 的服务，请将*ssl_enabled*参数设置为 "True" （无论适用）。 将*ssl_certificate*参数设置为*证书*文件的值。 将*ssl_key*设置为*密钥*文件的值。

    > [!IMPORTANT]
    > 使用 Microsoft 的证书时，无需购买自己的证书或域名。

    下面的示例演示如何创建一个配置，以启用 Microsoft 提供的 SSL 证书：

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

  * 使用购买的*证书*时，请使用*ssl_cert_pem_file*、 *ssl_key_pem_file*和*ssl_cname*参数。 下面的示例演示如何使用*pem*文件创建使用你购买的 SSL 证书的配置：

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

有关*enable_ssl*的详细信息，请参阅[enable_ssl （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)和[AksAttachConfiguration （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)。

### <a name="deploy-on-azure-container-instances"></a>在 Azure 容器实例上部署

部署到 Azure 容器实例时，需要为与 SSL 相关的参数提供值，如以下代码片段所示：

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

有关详细信息，请参阅[AciWebservice. deploy_configuration （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)。

## <a name="update-your-dns"></a>更新 DNS

接下来，必须更新 DNS，使其指向该 Web 服务。

+ **对于容器实例：**

  使用域名注册机构提供的工具来更新域名的 DNS 记录。 记录必须指向服务的 IP 地址。

  可能会有几分钟或几小时的延迟，客户端可以解析域名，具体取决于为域名配置的注册机构和 "生存时间" （TTL）。

+ **对于 AKS：**

  > [!WARNING]
  > 如果使用了*leaf_domain_label*通过 Microsoft 的证书创建服务，请不要手动更新群集的 DNS 值。 应自动设置该值。

  在左侧窗格中的 "**设置**" 下的 "**配置**" 选项卡上，更新 AKS 群集的公共 IP 地址的 DNS。 （请参阅下图。）公共 IP 地址是一种资源类型，它是在包含 AKS 代理节点和其他网络资源的资源组下创建的。

  [![Azure 机器学习：保护具有 SSL 的 web 服务](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-ssl-certificate"></a>更新 SSL 证书

SSL 证书已过期，必须续订。 通常每年都会发生这种情况。 使用以下部分中的信息为部署到 Azure Kubernetes 服务的模型更新和续订证书：

### <a name="update-a-microsoft-generated-certificate"></a>更新 Microsoft 生成的证书

如果证书最初由 Microsoft 生成（使用*leaf_domain_label*创建服务时），请使用以下示例之一更新证书：

**使用 SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**使用 CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

有关详细信息，请参阅以下参考文档：

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>更新自定义证书

如果证书最初是由证书颁发机构生成的，请使用以下步骤：

1. 使用证书颁发机构提供的文档来续订证书。 此过程将创建新的证书文件。

1. 使用 SDK 或 CLI 通过新证书更新服务：

    **使用 SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **使用 CLI**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

有关详细信息，请参阅以下参考文档：

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-ssl"></a>禁用 SSL

若要为部署到 Azure Kubernetes 服务的模型禁用 SSL，请`SslConfiguration`创建`status="Disabled"`一个，然后执行更新：

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable SSL
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>后续步骤
了解如何：
+ [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
+ [安全地在 Azure 虚拟网络中运行试验和推理](how-to-enable-virtual-network.md)
