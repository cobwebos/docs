---
title: Apache Kafka REST 代理-Azure HDInsight
description: 了解如何在 Azure HDInsight 上使用 Kafka REST 代理执行 Apache Kafka 操作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b53fc3af71ce872c9ca9f513139c8179fd4165ed
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031371"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST 代理与 Azure HDInsight 中的 Apache Kafka 群集进行交互

Kafka REST 代理允许通过 HTTP 上的 REST API 与 Kafka 群集进行交互。 这意味着你的 Kafka 客户端可以在虚拟网络之外。 此外，客户端可以发出简单的 HTTP 调用来发送和接收消息到 Kafka 群集，而不是依赖 Kafka 库。  

## <a name="background"></a>背景

### <a name="architecture"></a>体系结构

如果没有 REST 代理，Kafka 客户端需要与 Kafka 群集或对等互连 VNet 位于同一 VNet 中。 REST 代理使你可以连接位于任何位置的数据创建者或使用者。 部署 REST 代理会为你的群集创建新的公共终结点，你可以在门户设置中找到此终结点。

有关 API 支持的操作的完整规范，请参阅[APACHE KAFKA REST 代理 API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="security"></a>安全性

可以使用 Azure Active Directory 安全组来管理对 Kafka REST 代理的访问。 有关详细信息，请参阅[使用 Azure Active Directory 组管理应用和资源访问](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)。

在启用 REST 代理的情况下创建 Kafka 群集时，你将提供应有权访问 REST 终结点的 AAD 安全组。 需要访问 REST 代理的 Kafka 客户端（应用程序）应由组所有者注册到此组。 组所有者可以通过门户或 Powershell 执行此操作。

在向 REST 代理终结点发出请求之前，客户端应用程序应获取 OAuth 令牌来验证正确安全组的成员身份。 有关 OAuth 令牌工作方式的详细信息，请参阅[使用 OAuth 2.0 代码授予流授予对 Azure Active Directory web 应用程序的访问权限](../../active-directory/develop/v1-protocols-oauth-code.md)。 有关在 python 中获取 OAuth 令牌的示例，请参阅[客户端应用程序示例](#client-application-sample)

当客户端应用程序具有 OAuth 令牌后，它们必须在向 REST 代理发出的 HTTP 请求中传递该令牌。

## <a name="prerequisites"></a>先决条件

1. 将应用程序注册到 Azure AD。 你编写的用于与 Kafka REST 代理交互的客户端应用程序将使用此应用程序的 ID 和机密对 Azure 进行身份验证。
1. 创建 Azure AD 安全组，并将你向 Azure AD 注册的应用程序添加到安全组。 此安全组将用于控制允许哪些应用程序与 REST 代理交互。 有关创建 Azure AD 组的详细信息，请参阅[使用 Azure Active Directory 创建基本组和添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>创建启用了 REST 代理的 Kafka 群集

1. 在 Kafka 群集创建工作流的 "安全性 + 网络" 选项卡中，选中 "启用 Kafka REST 代理" 选项。

     ![启用 Kafka REST 代理并选择安全组](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 单击 "**选择安全组**"。 从安全组列表中，选择要有权访问 REST 代理的安全组。 您可以使用 "搜索" 框查找相应的安全组。 单击底部的 "**选择**" 按钮。

     ![启用 Kafka REST 代理并选择安全组](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 按照[使用 Azure 门户在 Azure HDInsight 中创建 Apache Kafka 群集](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)中所述，完成剩余步骤来创建群集。

1. 创建群集后，请进入群集属性以记录 Kafka REST 代理 URL。

     ![查看 REST 代理 URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>客户端应用程序示例

你可以使用以下 python 代码与 Kafka 群集上的 REST 代理交互。 此代码将执行以下操作：

1. 从 Azure AD 中获取 OAuth 令牌
1. 创建指定的主题
1. 向该主题发送消息
1. 使用来自该主题的消息

有关在 python 中获取 OAuth 令牌的详细信息，请参阅[Python AuthenticationContext class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)。 你可能会看到延迟，而未通过 Kafka REST 代理创建或删除的主题将反映在此处。 此延迟是由于缓存刷新造成的。

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

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

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

若要使用代码示例，请执行以下步骤：

1. 将示例代码保存在安装了 Python 的计算机上。
1. 通过执行 `pip3 install adal` 和 `pip install msrestazure`来安装所需的 python 依赖项。
1. 修改代码并为您的环境更新以下属性：
    1.  *租户 ID* -你的订阅所在的 Azure 租户。
    1.  *客户端 id* -你在安全组中注册的应用程序的 id。
    1.  *客户端机密*-你在安全组中注册的应用程序的机密
    1.  *Kafkarest_endpoint* –在群集概述的 "属性" 选项卡中获取此值，如 "[部署" 一节](#create-a-kafka-cluster-with-rest-proxy-enabled)中所述。 它应采用以下格式– `https://<clustername>-kafkarest.azurehdinsight.net`
3. 在命令行中，通过执行 `python <filename.py>` 来执行 python 文件

## <a name="next-steps"></a>后续步骤

* [Kafka REST 代理 API 参考文档](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [教程：使用 Apache Kafka 制造者和使用者 Api](apache-kafka-producer-consumer-api.md)