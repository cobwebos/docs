---
title: 快速入门：创建 DNS 区域和记录 - Azure 门户
titleSuffix: Azure DNS
description: 使用此分步快速入门指南了解如何使用 Azure 门户创建 Azure DNS 区域和记录。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 26e5386f1c9730f1600e59a002ea7845b82ffe06
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937133"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建 Azure DNS 区域和记录

可以将 Azure DNS 配置为解析公共域中的主机名。 例如，如果从某个域名注册机构购买了 contoso.xyz 域名，则可配置 Azure DNS 来托管 contoso.xyz 域，并将 www.contoso.xyz 解析为 Web 服务器或 Web 应用的 IP 地址    。

在本快速入门中，你将创建一个测试域，然后创建一个地址记录来将 *www* 解析为 IP 地址 *10.10.10.10*。

>[!IMPORTANT]
>本快速入门中的所有名称和 IP 地址都是示例，不代表实际场景。

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

对于所有门户步骤，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-dns-zone"></a>创建 DNS 区域

DNS 区域包含域的 DNS 条目。 若要开始在 Azure DNS 中托管域，请为该域名创建一个 DNS 区域。 

**创建 DNS 区域：**

1. 在左上角，选择“创建资源”  ，然后依次选择“网络”和“DNS 区域”。  

1. 在“创建 DNS 区域”  页上，键入或选择以下值：

   - **Name**：对于本快速入门示例，键入 contoso.xyz  。 DNS 区域名称可以是尚未在 Azure DNS 服务器上配置的任何值。 实际值可以是从域名注册机构购买的域。
   - **资源组**：选择“新建”，输入 MyResourceGroup，然后选择“确定”    。 资源组名称在 Azure 订阅中必须唯一。 

1. 选择“创建”  。

   ![DNS 区域](./media/dns-getstarted-portal/openzone650.png)

创建区域可能需要几分钟。

## <a name="create-a-dns-record"></a>创建 DNS 记录

在 DNS 区域中为你的域创建 DNS 条目或记录。 创建一个新的地址记录或“A”记录以将主机名解析为 IPv4 地址。

**创建“A”记录：**

1. 在 Azure 门户中，在“所有资源”下，打开 MyResourceGroup 资源组中的 contoso.xyz DNS 区域    。 可以在“按名称筛选”框中输入 *contoso.xyz* 来更轻松地找到它。 

1. 在“DNS 区域”  页面顶部，选择“+ 记录集”  。

1. 在“添加记录集”  页上，键入或选择以下值：

   - **Name**：键入 www  。 记录名称是你要解析为指定 IP 地址的主机名。
   - **类型**：选择 A  。“A”记录是最常见的，但是也有其他适用于邮件服务器（“MX”）、IP v6 地址（“AAAA”）等的记录类型。 
   - **TTL**：键入 1  。 DNS 请求的*存活时间*指定 DNS 服务器和客户端可以将响应缓存多长时间。
   - **TTL 单位**：选择“小时”  。 这是用于 **TTL** 值的时间单位。 
   - **IP 地址**：对于本快速入门示例，键入 10.10.10.10  。 此值是记录名称解析为的 IP 地址。 在现实场景中，则应输入 Web 服务器的公共 IP 地址。

因为本快速入门仅用于快速测试目的，因此不需要在域名注册机构那里配置 Azure DNS 名称服务器。 对于现实生产域，你希望 Internet 上的任何人都能够解析主机名，以便连接到你的 Web 服务器或应用。 你将访问域名注册机构来将名称服务器记录替换为 Azure DNS 名称服务器。 有关详细信息，请参阅[教程：在 Azure DNS 中托管域](dns-delegate-domain-azure-dns.md#delegate-the-domain)。

## <a name="test-the-name-resolution"></a>测试名称解析

现在，你已有一个测试 DNS 区域且其中有一个测试“A”记录，可以使用名为 *nslookup* 的工具来测试名称解析了。 

**测试 DNS 名称解析：**

1. 在 Azure 门户中，在“所有资源”下，打开 MyResourceGroup 资源组中的 contoso.xyz DNS 区域    。 可以在“按名称筛选”框中输入 *contoso.xyz* 来更轻松地找到它。 

1. 在“概述”页面上从名称服务器列表中复制其中一个名称服务器名称。  

   ![区域](./media/dns-getstarted-portal/viewzonens500.png)

1. 打开一个命令提示符，并运行以下命令：

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   例如：

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   应当会看到类似以下屏幕的内容：

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

主机名 www\.contoso.xyz 解析为 10.10.10.10，正如你配置的那样   。 此结果表明名称解析正常工作。 

## <a name="clean-up-resources"></a>清理资源

当不再需要你在本快速入门中创建的资源时，通过删除 MyResourceGroup 资源组将它们删除  。 打开 MyResourceGroup 资源组，并选择“删除资源组”   。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)