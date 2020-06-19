---
title: Apache Kafka REST 代理 - Azure HDInsight
description: 了解如何在 Azure HDInsight 上使用 Kafka REST 代理执行 Apache Kafka 操作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: has-adal-ref
ms.date: 04/03/2020
ms.openlocfilehash: 5e46e50da67559f69302357804f6f98fee70d4ad
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773298"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST 代理与 Azure HDInsight 中的 Apache Kafka 群集进行交互

Kafka REST 代理可让你使用 HTTP 通过 REST API 来与 Kafka 群集交互。 此操作表示你的 Kafka 客户端可位于虚拟网络之外。 客户端可以对 Kafka 群集进行简单的 HTTP 调用，而不必依赖 Kafka 库。 本文将演示如何创建启用了 REST 代理的 Kafka 群集。 另外还提供了一个示例代码，演示如何调用 REST 代理。

## <a name="rest-api-reference"></a>REST API 参考

有关 Kafka REST API 支持的操作，请参阅 [HDInsight Kafka REST 代理 API 参考](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

## <a name="background"></a>背景

![Kafka REST 代理设计](./media/rest-proxy/rest-proxy-architecture.png)

有关 API 支持的操作的完整规范，请参阅 [Apache Kafka REST 代理 API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="rest-proxy-endpoint"></a>REST 代理终结点

使用 REST 代理创建 HDInsight Kafka 群集会为群集创建新的公共终结点，你可以在 Azure 门户的 HDInsight 群集“属性”中找到该终结点。

### <a name="security"></a>安全性

对 Kafka REST 代理的访问通过 Azure Active Directory 安全组进行管理。 创建 Kafka 群集时，请为 Azure AD 安全组提供 REST 终结点访问权限。 需要访问 REST 代理的 Kafka 客户端应由组所有者注册到此组。 组所有者可通过门户或 PowerShell 注册。

对于 REST 代理终结点请求，客户端应用程序应获取 OAuth 令牌。 令牌用于验证安全组成员身份。 查找下面的[客户端应用程序示例](#client-application-sample)，其中演示了如何获取 OAuth 令牌。 客户端应用程序会在 HTTP 请求中将 OAuth 令牌传递给 REST 代理。

> [!NOTE]
> 请参阅[使用 Azure Active Directory 组管理应用和资源访问](../../active-directory/fundamentals/active-directory-manage-groups.md)，了解有关 AAD 安全组的详细信息。 有关 OAuth 令牌工作原理的详细信息，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../../active-directory/develop/v1-protocols-oauth-code.md)。

## <a name="kafka-rest-proxy-with-network-security-groups"></a>包含网络安全组的 Kafka REST 代理
如果你引入自己的 VNet 并通过网络安全组控制网络流量，则除端口 443 外，还应允许端口 9400 上的入站流量。 这将确保 Kafka REST 代理服务器可以访问。

## <a name="prerequisites"></a>先决条件

1. 将应用程序注册到 Azure AD。 编写的用来与 Kafka REST 代理交互的客户端应用程序将使用此应用程序的 ID 和机密对 Azure 进行身份验证。

1. 创建 Azure AD 安全组。 将已通过 Azure AD 注册的应用程序作为安全组的成员添加到该组中。 此安全组用于控制允许哪些应用程序与 REST 代理交互。 有关创建 Azure AD 组的详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

    验证该组的类型是否为“安全”。
    ![安全组](./media/rest-proxy/rest-proxy-group.png)

    验证该应用程序是否为该组的成员。
    ![检查成员身份](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>创建已启用 REST 代理的 Kafka 群集

以下步骤使用 Azure 门户。 有关使用 Azure CLI 的示例，请参阅[使用 Azure CLI 创建 Apache Kafka REST 代理群集](tutorial-cli-rest-proxy.md)。

1. 在 Kafka 群集创建工作流期间，在“安全 + 网络”选项卡中，选中“启用 Kafka REST 代理”选项 。

     ![启用 Kafka REST 代理并选择安全组](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 单击“选择安全组”。 从安全组列表中，选择希望有权访问 REST 代理的安全组。 可以使用搜索框查找相应的安全组。 单击底部的“选择”按钮。

     ![启用 Kafka REST 代理并选择安全组](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 按照[使用 Azure 门户在 Azure HDInsight 中创建 Apache Kafka 群集](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)中的说明，完成创建群集的其余步骤。

1. 创建群集后，转到群集属性，记录 Kafka REST 代理 URL。

     ![查看 REST 代理 URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>客户端应用程序示例

可以使用以下 Python 代码与 Kafka 群集上的 REST 代理交互。 若要使用代码示例，请执行以下步骤：

1. 将示例代码保存在安装了 Python 的计算机上。
1. 通过执行 `pip3 install msal` 安装所需的 Python 依赖项。
1. 修改代码部分“配置这些属性”，并为你的环境更新以下属性：

    |properties |说明 |
    |---|---|
    |租户 ID|订阅所在的 Azure 租户。|
    |客户端 ID|在安全组中注册的应用程序的 ID。|
    |客户端机密|在安全组中注册的应用程序的机密。|
    |Kafkarest_endpoint|从群集概述的“属性”选项卡中获取此值，如[部署部分](#create-a-kafka-cluster-with-rest-proxy-enabled)所述。 该值应采用以下格式：`https://<clustername>-kafkarest.azurehdinsight.net`|

1. 在命令行中，通过执行 `sudo python3 <filename.py>` 来执行 Python 文件

此代码执行以下操作：

1. 从 Azure AD 提取 OAuth 令牌。
1. 演示如何向 Kafka REST 代理发出请求。

有关如何使用 Python 获取 OAuth 令牌的详细信息，请参阅 [Python AuthenticationContext 类](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)。 如果不是通过 Kafka REST 代理创建或删除的 `topics` 在该处有所反映，则可能会出现延迟。 此延迟是因为缓存刷新。

```python
#Required python packages
#pip3 install msal

import msal

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

下面是另外一个示例，说明如何使用 curl 命令从 Azure 获取用于 REST 代理的令牌。 请注意，我们需要在获取令牌时指定 `scope=https://hib.azurehdinsight.net/.default`。

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>后续步骤

* [Kafka REST 代理 API 参考文档](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
