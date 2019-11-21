---
title: 在区域顶点托管负载均衡的 Azure Web 应用
description: 使用 Azure DNS 别名记录在区域顶点托管负载均衡的 Web 应用
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: allensu
ms.openlocfilehash: a673a74f8f6f919e7ebb7fc3b065ee0742ab3a10
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212373"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>在区域顶点托管负载均衡的 Azure Web 应用

DNS 协议可防止分配区域顶点的 A 或 AAAA 记录之外的任何内容。 contoso.com 是区域顶点的示例。 这种限制将为流量管理器背后拥有负载均衡应用程序的应用程序所有者带来问题。 无法从区域顶点记录指向流量管理器配置文件。 因此，应用程序所有者必须使用一种解决方法。 应用程序层的重定向必须从区域顶点重定向到另一个域。 An example is a redirect from contoso.com to www\.contoso.com. 这种方案会给重定向功能带来单一故障点。

使用别名记录，此问题将不再存在。 应用程序所有者现在可将其区域顶点记录指向具有外部终结点的流量管理器配置文件。 应用程序所有者可以指向用于其 DNS 区域中任何其他域的相同流量管理器配置文件。

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. 只要流量管理器配置文件仅配置了外部终结点，就会出现这种情况。

本文介绍如何为域顶点创建别名记录，以及为 Web 应用配置流量管理器配置文件终结点。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

必须具有可用于在 Azure DNS 中托管以供测试的域名。 必须能够完全控制此域。 完全控制包括能够为域设置名称服务器 (NS) 记录。

有关在 Azure DNS 中托管域的说明，请参阅[教程：在 Azure DNS 中托管域](dns-delegate-domain-azure-dns.md)。

本教程中使用的示例域为 contoso.com，但请使用自己的域名。

## <a name="create-a-resource-group"></a>创建资源组

创建一个资源组用于保存本文中使用的所有资源。

## <a name="create-app-service-plans"></a>创建应用服务计划

使用下表中的配置信息在该资源组中创建两个 Web 应用服务计划。 有关创建应用服务计划的详细信息，请参阅[在 Azure 中管理应用服务计划](../app-service/app-service-plan-manage.md)。


|名称  |操作系统  |Location  |定价层  |
|---------|---------|---------|---------|
|ASP-01     |Windows|美国东部|Dev/Test D1-Shared|
|ASP-02     |Windows|美国中部|Dev/Test D1-Shared|

## <a name="create-app-services"></a>创建应用服务

创建两个 Web 应用，在每个应用服务计划中各创建一个。

1. On upper left corner of the Azure portal page, select **Create a resource**.
2. 在搜索栏中键入“Web 应用”并按 Enter 键。
3. Select **Web App**.
4. 选择**创建**。
5. 接受默认值，并参考下表配置两个 Web 应用：

   |名称<br>（在 .azurewebsites.net 中必须唯一）|资源组 |运行时堆栈|地区|应用服务计划/位置
   |---------|---------|-|-|-------|
   |App-01|使用现有项<br>选择你的资源组|.NET Core 2.2|美国东部|ASP-01(D1)|
   |App-02|使用现有项<br>选择你的资源组|.NET Core 2.2|美国中部|ASP-02(D1)|

### <a name="gather-some-details"></a>收集一些详细信息

Now you need to note the IP address and host name for the web apps.

1. Open your resource group and select your first web app (**App-01** in this example).
2. In the left column, select **Properties**.
3. 记下“URL”下面的地址，并在“出站 IP 地址”下面，记下列表中的第一个 IP 地址。 You'll use this information later when you configure your Traffic Manager end points.
4. 针对 **App-02** 重复上述步骤。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

在资源组中创建流量管理器配置文件。 使用默认值，并键入在 trafficmanager.net 命名空间中唯一的名称。

有关创建流量管理器配置文件的信息，请参阅[快速入门：为高度可用的 Web 应用程序创建流量管理器配置文件](../traffic-manager/quickstart-create-traffic-manager-profile.md)。

### <a name="create-endpoints"></a>创建终结点

现在，可以创建两个 Web 应用的终结点。

1. Open your resource group and select your Traffic Manager profile.
2. In the left column, select **Endpoints**.
3. 选择 **添加** 。
4. 参考下表配置终结点：

   |Type  |名称  |确定目标  |Location  |自定义标头设置|
   |---------|---------|---------|---------|---------|
   |外部终结点     |End-01|为 App-01 记下的 IP 地址|美国东部|主机：\<为 App-01 记下的 URL\><br>示例：**host:app-01.azurewebsites.net**|
   |外部终结点     |End-02|为 App-02 记下的 IP 地址|美国中部|主机：\<为 App-02 记下的 URL\><br>示例：**host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>创建 DNS 区域

可以使用现有 DNS 区域进行测试，或者创建新区域。 若要在 Azure 中创建和委托新的 DNS 区域，请参阅[教程：在 Azure DNS 中托管域](dns-delegate-domain-azure-dns.md)。

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Add a TXT record for custom domain validation

When you add a custom hostname to your web apps, it will look for a specific TXT record to validate your domain.

1. Open your resource group and select the DNS zone.
2. 选择“记录集”。
3. Add the record set using the following table. For the value, use the actual web app URL that you previously recorded:

   |名称  |Type  |Value|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>添加自定义域

Add a custom domain for both web apps.

1. Open your resource group and select your first web app.
2. In the left column, select **Custom domains**.
3. Under **Custom Domains**, select **Add custom domain**.
4. Under **Custom domain**, type your custom domain name. 例如 contoso.com。
5. 选择“验证”。

   Your domain should pass validation and show green check marks next to **Hostname availability** and **Domain ownership**.
5. 选择“添加自定义域”。
6. 若要在“分配到站点的主机名”下面查看新主机名，请刷新浏览器。 The refresh on the page doesn't always show changes right away.
7. 针对第二个 Web 应用重复上述过程。

## <a name="add-the-alias-record-set"></a>添加别名记录集

Now add an alias record for the zone apex.

1. Open your resource group and select the DNS zone.
2. 选择“记录集”。
3. 参考下表添加记录集：

   |名称  |Type  |别名记录集  |别名类型  |Azure 资源|
   |---------|---------|---------|---------|-----|
   |@     |A|是|Azure 资源|流量管理器 - 你的配置文件|


## <a name="test-your-web-apps"></a>测试 Web 应用

Now you can test to make sure you can reach your web app and that it's being load balanced.

1. 打开 Web 浏览器并浏览到你的域。 例如 contoso.com。 应会看到默认的 Web 应用页面。
2. 停止第一个 Web 应用。
3. 关闭 Web 浏览器并等待几分钟。
4. 启动 Web 浏览器并浏览到你的域。 看到的应该仍是默认 Web 应用页面。
5. 停止第二个 Web 应用。
6. 关闭 Web 浏览器并等待几分钟。
7. 启动 Web 浏览器并浏览到你的域。 应会看到错误 403，指出 Web 应用已停止。
8. 启动第二个 Web 应用。
9. 关闭 Web 浏览器并等待几分钟。
10. 启动 Web 浏览器并浏览到你的域。 应会再次看到默认的 Web 应用页面。

## <a name="next-steps"></a>后续步骤

若要详细了解别名记录，请参阅以下文章：

- [教程：配置引用 Azure 公共 IP 地址的别名记录](tutorial-alias-pip.md)
- [教程：使用流量管理器支持顶点域名的别名记录](tutorial-alias-tm.md)
- [DNS 常见问题](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

To learn how to migrate an active DNS name, see [Migrate an active DNS name to Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
