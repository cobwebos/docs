---
title: Apache Kafka REST 代理 - Azure HDInsight
description: 了解如何在 Azure HDInsight 上使用 Kafka REST 代理执行 Apache Kafka 操作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d99a3b803b80dc41990a63e647d3ba928deb31af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198899"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>使用 REST 代理与 Azure HDInsight 中的 Apache Kafka 群集交互

Kafka REST 代理使您能够通过 REST API 通过 HTTP 与卡夫卡群集进行交互。 这意味着您的 Kafka 客户端可以位于虚拟网络之外。 此外，客户端可以发出简单的 HTTP 调用来与 Kafka 群集相互发送和接收消息，而无需依赖 Kafka 库。 本教程将介绍如何创建启用 REST 代理的 Kafka 群集，并提供示例代码，演示如何调用 REST 代理。

## <a name="rest-api-reference"></a>REST API 参考

有关卡夫卡 REST API 支持的操作的完整规范，请参阅[HDInsight Kafka REST 代理 API 参考](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

## <a name="background"></a>背景

![卡夫卡 REST 代理体系结构](./media/rest-proxy/rest-proxy-architecture.png)

有关 API 支持的操作的完整规范，请参阅 [Apache Kafka REST 代理 API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)。

### <a name="rest-proxy-endpoint"></a>REST 代理终结点

使用 REST 代理创建 HDInsight Kafka 群集会为群集创建新的公共终结点，您可以在 Azure 门户上的 HDInsight 群集"属性"中找到该终结点。

### <a name="security"></a>安全性

使用 Azure Active Directory 安全组来管理对 Kafka REST 代理的访问。 在启用 REST 代理的情况下创建 Kafka 群集时，将提供应有权访问 REST 终结点的 Azure 活动目录安全组。 组所有者应将需要访问 REST 代理的 Kafka 客户端（应用程序）注册到此组。 组所有者可以通过门户或 Powershell 执行此操作。

在向 REST 代理终结点发出请求之前，客户端应用程序应获取 OAuth 令牌来验证适当安全组的成员身份。 请在下面找到[一个客户端应用程序示例](#client-application-sample)，其中演示如何获取 OAuth 令牌。 客户端应用程序获取 OAuth 令牌后，必须在向 REST 代理发出的 HTTP 请求中传递该令牌。

> [!NOTE]  
> 请参阅[使用 Azure 活动目录组管理应用和资源访问](../../active-directory/fundamentals/active-directory-manage-groups.md)，以了解有关 AAD 安全组详细信息。 有关 OAuth 令牌工作原理的详细信息，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../../active-directory/develop/v1-protocols-oauth-code.md)。

## <a name="prerequisites"></a>先决条件

1. 将应用程序注册到 Azure AD。 编写的用来与 Kafka REST 代理交互的客户端应用程序将使用此应用程序的 ID 和机密对 Azure 进行身份验证。
1. 创建一个 Azure AD 安全组，并将已注册到 Azure AD 的应用程序添加到该安全组。 此安全组将用于控制允许哪些应用程序与 REST 代理交互。 有关创建 Azure AD 组的详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>创建启用 REST 代理的 Kafka 群集

1. 在运行 Kafka 群集创建工作流期间，在“安全性 + 网络”选项卡中，选中“启用 Kafka REST 代理”选项。

     ![启用 Kafka REST 代理并选择安全组](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. 单击“选择安全组”。**** 从安全组列表中，选择你要允许其访问 REST 代理的安全组。 可以使用搜索框查找适当的安全组。 单击底部的“选择”按钮****。

     ![启用 Kafka REST 代理并选择安全组](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 根据[使用 Azure 门户在 Azure HDInsight 中创建 Apache Kafka 群集](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)中所述，完成创建群集的剩余步骤。

1. 创建群集后，转到群集属性并记下 Kafka REST 代理 URL。

     ![查看 REST 代理 URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>客户端应用程序示例

可使用以下 Python 代码来与 Kafka 群集上的 REST 代理交互。 若要使用代码示例，请执行以下步骤：

1. 在装有 Python 的计算机上保存示例代码。
1. 执行 `pip3 install adal` 和 `pip install msrestazure` 安装所需的 Python 依赖项。
1. 修改代码部分*配置这些属性*并更新环境的以下属性：
    1.  *租户 ID* – 订阅所在的 Azure 租户。
    1.  *客户端 ID* – 在安全组中注册的应用程序的 ID。
    1.  *客户端机密* – 在安全组中注册的应用程序的机密。
    1.  *Kafkarest_endpoint* – 根据[部署部分](#create-a-kafka-cluster-with-rest-proxy-enabled)所述，从群集概述中的“属性”选项卡获取此值。 此属性应采用以下格式 – `https://<clustername>-kafkarest.azurehdinsight.net`
3. 在命令行中，通过执行 `python <filename.py>` 来执行 Python 文件

此代码将执行以下操作：

1. 从 Azure AD 提取 OAuth 令牌
1. 演示如何向卡夫卡 REST 代理发出请求

有关在 Python 中获取 OAuth 令牌的详细信息，请参阅 [Python AuthenticationContext 类](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)。 未创建的或已通过 Kafka REST 代理删除的主题可能会经过一段延迟时间才反映此状态。 此延迟是缓存刷新造成的。

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

## <a name="next-steps"></a>后续步骤

* [Kafka REST 代理 API 参考文档](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
