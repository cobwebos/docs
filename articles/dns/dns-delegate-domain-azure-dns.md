---
title: 教程 - 在 Azure DNS 中托管域和子域
description: 本教程展示了如何配置 Azure DNS 来托管 DNS 区域。
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: 44f5bf9a28d56e85bae1d50136c50868ec96eb4e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205435"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>教程：在 Azure DNS 中托管域

可以使用 Azure DNS 来托管 DNS 域并管理 DNS 记录。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 

例如，假设你从某个域名注册机构购买了域 contoso.net，然后在 Azure DNS 中创建了名为 contoso.net 的区域。 由于你是域的所有者，因此，注册机构将为你提供选项来配置域的名称服务器 (NS) 记录。 注册机构将这些 NS 记录存储在 .net 父区域中。 然后，世界各地的 Internet 用户在尝试解析 contoso.net 中的 DNS 记录时，会被定向到 Azure DNS 区域中的域。


本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建 DNS 区域
> * 检索名称服务器的列表
> * 委托域
> * 验证委托是否正常工作


如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-dns-zone"></a>创建 DNS 区域

1. 登录到 Azure 门户。
1. 在左上方，选择“创建资源” > “网络” > “DNS 区域”以打开“创建 DNS 区域”页面。

   ![DNS 区域](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. 在“创建 DNS 区域”页上输入以下值，然后选择“创建”：

   | **设置** | **值** | **详细信息** |
   |---|---|---|
   |**名称**|[你的域名] |你购买的域名。 本教程使用 contoso.net 作为示例。|
   |**订阅**|[你的订阅]|选择要在其中创建区域的订阅。|
   |**资源组**|**新建：** contosoRG|创建资源组。 资源组名称必须在所选订阅中唯一。 |
   |**位置**|美国东部||

> [!NOTE]
> 资源组的位置对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

## <a name="retrieve-name-servers"></a>检索名称服务器

在将 DNS 区域委托给 Azure DNS 之前，需要知道区域的名称服务器。 每次创建区域时，Azure DNS 都会分配某个池中的名称服务器。

1. 创建 DNS 区域以后，在 Azure 门户的“收藏夹”窗格中选择“所有资源”。 在“所有资源”页中，选择你的 DNS 区域。 如果所选订阅中已包含多个资源，则可在“按名称筛选”框中键入你的域名，轻松访问应用程序网关。 

1. 从“DNS 区域”页中检索名称服务器。 在本示例中，为区域 contoso.net 分配了名称服务器 *ns1-01.azure-dns.com*、*ns2-01.azure-dns.net*、*ns3-01.azure-dns.org* 和 *ns4-01.azure-dns.info*：

   ![名称服务器的列表](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS 自动在你的区域中为所分配的名称服务器创建权威 NS 记录。


## <a name="delegate-the-domain"></a>委托域

创建 DNS 区域且有了名称服务器以后，需使用 Azure DNS 名称服务器更新父域。 每个注册机构都有自身的 DNS 管理工具，可以更改域的名称服务器记录。 在注册机构的 DNS 管理页中，编辑 NS 记录并将 NS 记录替换为 Azure DNS 名称服务器。

将域委托给 Azure DNS 时，必须使用 Azure DNS 提供的名称服务器。 建议使用所有 4 个名称服务器，不管域名是什么。 域委托不需要名称服务器即可使用相同的顶级域作为域。

Azure DNS 目前不支持使用你自己区域中的名称服务器的委托（有时称为“虚构名称服务器”）。

## <a name="verify-that-the-delegation-is-working"></a>验证委托是否正常工作

完成委托后，可以使用 *nslookup* 等工具来查询区域的授权起始点 (SOA) 记录，验证名称解析是否正常工作。 SOA 记录是在创建区域时自动创建的。 在完成委托后，你可能需要等待 10 分钟或更长时间，然后才能成功验证它是否正常工作。 更改可能需要花费一段时间才能通过 DNS 系统进行传播。

无需指定 Azure DNS 名称服务器。 如果正确设置了委托，正常的 DNS 解析过程会自动找到名称服务器。

从命令提示符下，键入类似于以下内容的 nslookup 命令：

```
nslookup -type=SOA contoso.net
```

下面是来自前一命令的示例响应：

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.net
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="clean-up-resources"></a>清理资源

如果打算继续执行下一教程，可以保留 **contosoRG** 资源组。 否则，请删除 **contosoRG** 资源组以删除在本教程中创建的资源。 若要执行此操作，请单击 **contosoRG** 资源组，然后单击“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你为你的域创建了一个 DNS 区域并将其委托给了 Azure DNS。 若要了解 Azure DNS 和 Web 应用，请继续学习适用于 Web 应用的教程。

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)
