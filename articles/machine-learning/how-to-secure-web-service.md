---
title: 使用 TLS 保护 Web 服务
titleSuffix: Azure Machine Learning
description: 了解如何启用 HTTPS，以保护通过 Azure 机器学习部署的 Web 服务。 Azure 机器学习使用 TLS 版本 1.2 来保护部署为 Web 服务的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f2d874925cdad05ec7979575c9b87608da542927
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661432"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>使用 TLS 保护通过 Azure 机器学习部署的 Web 服务
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何保护通过 Azure 机器学习部署的 Web 服务。

使用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 来限制对 Web 服务的访问并保护客户端提交的数据。 HTTPS 对客户端和 Web 服务之间的通信进行加密来帮助保护两者之间的通信。 加密使用[传输层安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 TLS 有时仍称为安全套接字层 (SSL)，这是 TLS 的前身。

> [!TIP]
> Azure 机器学习 SDK 使用术语“SSL”表示与安全通信相关的属性。 这并不意味着 Web 服务不会使用 TLS。 SSL 只是更广泛公认的术语。
>
> 具体而言，通过 Azure 机器学习部署的 web 服务仅支持 TLS 版本1。1

TLS 和 SSL 均依赖数字证书，这有助于加密和身份验证。 有关数字证书工作原理的详细信息，请参阅维基百科主题[公钥基础结构](https://en.wikipedia.org/wiki/Public_key_infrastructure)。

> [!WARNING]
> 如果 Web 服务未使用 HTTPS，则服务上收发的数据可能对 Internet 上的其他人可见。
>
> HTTPS 还允许客户端验证它连接到的服务器的真实性。 此功能可以防止客户端免受[中间人](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)攻击。

保护 Web 服务的常规过程如下：

1. 获取域名。

2. 获取数字证书。

3. 在启用 TLS 的情况下部署或更新 Web 服务。

4. 更新 DNS，使其指向该 Web 服务。

> [!IMPORTANT]
> 如果要部署到 Azure Kubernetes 服务 (AKS)，可以购买属于自己的证书或使用 Microsoft 提供的证书。 如果使用 Microsoft 的证书，则无需获取域名或 TLS/SSL 证书。 有关详细信息，请参阅本文的[启用 TLS 并进行部署](#enable)部分。

跨[部署目标](how-to-deploy-and-where.md)进行保护时，步骤稍有不同。

## <a name="get-a-domain-name"></a>获取域名

如果还没有属于自己的域名，请从域名注册机构购买一个。 各注册机构的购买过程和价格有所不同。 注册机构提供管理域名的工具。 可使用这些工具将完全限定的域名 (FQDN)（如 www\.contoso.com）映射到托管 Web 服务的 IP 地址。

## <a name="get-a-tlsssl-certificate"></a>获取 TLS/SSL 证书

可通过多种方式获取 TLS/SSL 证书（数字证书）。 最常用的方式是从证书颁发机构 (CA) 购买。 无论证书来自哪里，都需要以下文件：

* 证书。 证书必须包含完整的证书链，并必须使用“PEM 编码”。
* 密钥。 密钥也必须使用 PEM 编码。

请求证书时，必须提供计划用于 Web 服务的地址的 FQDN（例如 www\.contoso.com）。 会对比证书上标记的地址和客户端使用的地址，以验证 Web 服务的身份。 如果这两个地址不匹配，客户端会收到一条错误消息。

> [!TIP]
> 如果证书颁发机构不能以 PEM 编码文件的方式提供证书和密钥，可使用 [OpenSSL](https://www.openssl.org/) 等实用程序来更改格式。

> [!WARNING]
> 自签名证书只能用于开发。 请勿在生产环境中使用这些证书。 自签名证书可能会导致客户端应用程序出现问题。 有关详细信息，请参阅客户端应用程序使用的网络库的文档。

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> 启用 TLS 并进行部署

若要部署（或重新部署）启用了 TLS 的服务，请在适当的位置将 ssl_enabled 参数设置为“True”。 将 ssl_certificate 参数设置为证书文件的值 。 将 ssl_key 设置为密钥文件的值 。

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>在 AKS 和现场可编程门阵列 (FPGA) 上进行部署

  > [!NOTE]
  > 为设计器部署安全的 Web 服务时，此部分中的信息也适用。 如果不熟悉如何使用 Python SDK，请参阅[什么是适用于 Python 的 Azure 机器学习 SDK？](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)。

部署到 AKS 时，可以创建新的 AKS 群集或附加现有群集。 有关创建或附加群集的详细信息，请参阅[将模型部署到 Azure Kubernetes 服务群集](how-to-deploy-azure-kubernetes-service.md)。
  
-  如果创建新群集，请使用 **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** 。
- 如果附加现有群集，请使用 **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** 。 这两个方法都返回包含 enable_ssl 方法的配置对象。

enable_ssl 方法可以使用 Microsoft 提供的证书或你购买的证书。

  * 使用 Microsoft 的证书时，必须使用 leaf_domain_label 参数。 此参数生成服务的 DNS 名称。 例如，使用值“contoso”将创建域名“contoso\<six-random-characters>.\<azureregion>.cloudapp.azure.com”，其中 \<azureregion> 是包含该服务的区域。 或者，可使用 overwrite_existing_domain 参数覆盖现有的 leaf_domain_label 。

    若要部署（或重新部署）启用了 TLS 的服务，请在适当的位置将 ssl_enabled 参数设置为“True”。 将 ssl_certificate 参数设置为证书文件的值 。 将 ssl_key 设置为密钥文件的值 。

    > [!IMPORTANT]
    > 如果使用 Microsoft 的证书，则无需购买属于自己的证书或域名。

    下面的示例演示如何创建一个配置，以启用 Microsoft 提供的 TLS/SSL 证书：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * 使用你购买的证书 时，请使用 ssl_cert_pem_file、ssl_key_pem_file、ssl_cname 参数   。 下面的示例演示如何使用 .pem 文件创建使用所购买的 TLS/SSL 证书的配置：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

有关 enable_ssl 的详细信息，请参阅 [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) 和 [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)。

### <a name="deploy-on-azure-container-instances"></a>在 Azure 容器实例上进行部署

部署到 Azure 容器实例时，需要为与 TLS 相关的参数提供值，如以下代码片段所示：

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

有关详细信息，请参阅 [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)。

## <a name="update-your-dns"></a>更新 DNS

接下来，必须更新 DNS，使其指向该 Web 服务。

+ **对于容器实例：**

  使用域名注册机构的工具来更新域名的 DNS 记录。 该记录必须指向服务的 IP 地址。

  可能延迟几分钟到几小时之后客户端才能解析域名，具体取决于注册机构和为域名配置的“生存时间”(TTL)。

+ **对于 AKS：**

  > [!WARNING]
  > 如果使用了 leaf_domain_label 通过 Microsoft 的证书创建服务，请不要手动更新群集的 DNS 值。 应自动设置该值。

  在左侧窗格中“设置”下的“配置”选项卡上更新 AKS 群集公共 IP 地址 DNS 。 （参看下图。）公共 IP 地址是在包含 AKS 代理节点和其他网络资源的资源组下创建的资源类型。

  [![Azure 机器学习：使用 TLS 保护 Web 服务](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>更新 TLS/SSL 证书

TLS/SSL 证书已过期，必须续订。 通常每年都会发生这种情况。 使用以下部分中的信息为部署到 Azure Kubernetes 服务的模型更新和续订证书：

### <a name="update-a-microsoft-generated-certificate"></a>更新 Microsoft 生成的证书

如果证书最初由 Microsoft 生成（使用 leaf_domain_label 创建服务），请使用以下某个示例更新证书：

> [!IMPORTANT]
> * 如果现有证书仍然有效，请使用 `renew=True` (SDK) 或 `--ssl-renew` (CLI) 强制执行配置以续订该证书。 例如，如果现有证书在 10 天内仍然有效，并且你不使用 `renew=True`，则可能不会续订该证书。
> * 最初部署服务时，使用了 `leaf_domain_label`，目的是使用模式 `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` 来创建 DNS 名称。 若要保留现有名称（包括最初生成的 6 位数字），请使用原始 `leaf_domain_label` 值。 不包括生成的 6 位数字。

**使用 SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**使用 CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

有关详细信息，请参阅以下参考文档：

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

### <a name="update-custom-certificate"></a>更新自定义证书

如果证书最初由证书颁发机构生成，请使用以下步骤：

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

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

## <a name="disable-tls"></a>禁用 TLS

若要为部署到 Azure Kubernetes 服务的模型禁用 TLS，请创建 `SslConfiguration` 并设置 `status="Disabled"`，然后执行更新：

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>后续步骤
了解如何：
+ [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
+ [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)
