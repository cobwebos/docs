---
title: Apache Kafka REST 代理 - Azure HDInsight
description: 了解如何在 Azure HDInsight 上使用 Kafka REST 代理执行 Apache Kafka 操作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: has-adal-ref
ms.openlocfilehash: affdbfba125b7e9b3f3fe250a56af30e9efe816e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611000"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST 代理与 Azure HDInsight 中的 Apache Kafka 群集交互

使用 Kafka REST 代理可以通过基于 HTTP 的 REST API 来与 Kafka 群集交互。 此操作表示你的 Kafka 客户端可以在你的虚拟网络之外。 客户端可以向 Kafka 群集发出简单的 HTTP 调用，而不是依赖 Kafka 库。 本文将演示如何创建启用 REST 代理的 Kafka 群集。 还提供了一个示例代码，演示如何调用 REST 代理。

## <a name="rest-api-reference"></a>REST API 参考

有关 Kafka REST API 支持的操作，请参阅[HDInsight KAFKA REST 代理 API 参考](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

## <a name="background"></a>背景

![Kafka REST 代理设计](./media/rest-proxy/rest-proxy-architecture.png)

有关 API 支持的操作的完整规范，请参阅[APACHE KAFKA REST 代理 API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="rest-proxy-endpoint"></a>REST 代理终结点

使用 REST 代理创建 HDInsight Kafka 群集将为群集创建新的公共终结点，你可以在 Azure 门户的 HDInsight 群集**属性**中找到该终结点。

### <a name="security"></a>安全性

使用 Azure Active Directory 安全组来管理对 Kafka REST 代理的访问。 创建 Kafka 群集时，请提供具有 REST 终结点访问权限的 Azure AD 安全组。 需要访问 REST 代理的 Kafka 客户端应由组所有者注册到此组。 组所有者可以通过门户或 PowerShell 进行注册。

对于 REST 代理终结点请求，客户端应用程序应获取 OAuth 令牌。 令牌用于验证安全组成员身份。 在下面找到一个演示如何获取 OAuth 令牌的[客户端应用程序示例](#client-application-sample)。 客户端应用程序将 HTTP 请求中的 OAuth 令牌传递给 REST 代理。

> [!NOTE]
> 有关 AAD 安全组的详细信息，请参阅[使用 Azure Active Directory 组管理应用和资源访问](../../active-directory/fundamentals/active-directory-manage-groups.md)。 有关 OAuth 令牌工作原理的详细信息，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../../active-directory/develop/v1-protocols-oauth-code.md)。

## <a name="prerequisites"></a>先决条件

1. 将应用程序注册到 Azure AD。 编写的用来与 Kafka REST 代理交互的客户端应用程序将使用此应用程序的 ID 和机密对 Azure 进行身份验证。

1. 创建 Azure AD 安全组。 将已注册的应用程序添加到作为组**成员**的安全组 Azure AD。 此安全组将用于控制允许哪些应用程序与 REST 代理交互。 有关创建 Azure AD 组的详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

    验证该组的类型为 "**安全**"。
    ![安全组](./media/rest-proxy/rest-proxy-group.png)

    验证该应用程序是否为组的成员。
    ![检查成员身份](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>创建启用 REST 代理的 Kafka 群集

1. 在 Kafka 群集创建工作流的过程中，在 "**安全 + 网络**" 选项卡中，选中 "**启用 Kafka REST 代理**" 选项。

     ![启用 Kafka REST 代理并选择安全组](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 单击“选择安全组”。**** 从安全组列表中，选择你要允许其访问 REST 代理的安全组。 可以使用搜索框查找适当的安全组。 单击底部的“选择”按钮****。

     ![启用 Kafka REST 代理并选择安全组](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 根据[使用 Azure 门户在 Azure HDInsight 中创建 Apache Kafka 群集](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)中所述，完成创建群集的剩余步骤。

1. 创建群集后，转到群集属性并记下 Kafka REST 代理 URL。

     ![查看 REST 代理 URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>客户端应用程序示例

可使用以下 Python 代码来与 Kafka 群集上的 REST 代理交互。 若要使用代码示例，请执行以下步骤：

1. 在装有 Python 的计算机上保存示例代码。
1. 通过执行`pip3 install msal`来安装所需的 python 依赖项。
1. 修改代码部分：**配置这些属性**，并更新环境的下列属性：

    |属性 |说明 |
    |---|---|
    |租户 ID|订阅所在的 Azure 租户。|
    |客户端 ID|在安全组中注册的应用程序的 ID。|
    |客户端机密|在安全组中注册的应用程序的机密。|
    |Kafkarest_endpoint|在群集概述的 "**属性**" 选项卡中获取此值，如 "[部署" 部分](#create-a-kafka-cluster-with-rest-proxy-enabled)中所述。 此属性应采用以下格式 – `https://<clustername>-kafkarest.azurehdinsight.net`|

1. 在命令行中，通过执行 `sudo python3 <filename.py>` 来执行 Python 文件

此代码执行以下操作：

1. 从 Azure AD 中获取 OAuth 令牌。
1. 演示如何请求 Kafka REST 代理。

有关在 Python 中获取 OAuth 令牌的详细信息，请参阅 [Python AuthenticationContext 类](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)。 你可能会看到延迟， `topics`而不是通过 Kafka REST 代理来创建或删除该延迟。 此延迟是因为缓存刷新。

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

在下面的示例中查找如何使用卷曲命令从 Azure 获取用于 REST 代理的令牌。 **请注意，在获取`scope=https://hib.azurehdinsight.net/.default`令牌时需要指定。**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>后续步骤

* [Kafka REST 代理 API 参考文档](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
