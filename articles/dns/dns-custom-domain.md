---
title: 将 Azure DNS 与 Azure 资源集成 | Microsoft Docs
description: 了解如何使用 Azure DNS 来为 Azure 资源提供 DNS。
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/19/2018
ms.author: kumud
ms.openlocfilehash: cbc769cd7356b3057fd2aae295071b04d2e40d91
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
ms.locfileid: "27994376"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>使用 Azure DNS 为 Azure 服务提供自定义域设置

Azure DNS 为支持自定义域或具有完全限定域名 (FQDN) 的任何 Azure 资源的自定义域提供 DNS。 一个例子就是，现已创建一个 Azure Web 应用，并希望用户通过将 contoso.com 或 www.contoso.com 用作 FQDN 来访问它。 本文逐步讲解如何使用 Azure DNS 配置 Azure 服务，以便使用自定义域。

## <a name="prerequisites"></a>先决条件

若要对自定义域使用 Azure DNS，首先必须将该域委派给 Azure DNS。 有关如何为委派配置名称服务器的说明，请访问[将域委派给 Azure DNS](./dns-delegate-domain-azure-dns.md)。 将域委派给 Azure DNS 区域后，便可以配置所需的 DNS 记录。

可为 [Azure 函数应用](#azure-function-app)、[公共 IP 地址](#public-ip-address)、[应用服务（Web 应用）](#app-service-web-apps)、[Blob 存储](#blob-storage)和 [Azure CDN](#azure-cdn) 配置虚构域或自定义域。

## <a name="azure-function-app"></a>Azure 函数应用

若要为 Azure 函数应用配置自定义域，需要创建一条 CNAME 记录，并在该函数应用本身上进行配置。
 
导航到“其他” > “函数应用”并选择自己的函数应用。 单击“平台功能”，在“网络”下面单击“自定义域”。

![函数应用边栏选项卡](./media/dns-custom-domain/functionapp.png)

记下“自定义域”边栏选项卡上的当前 URL，此地址用作创建的 DNS 记录的别名。

![自定义域边栏选项卡](./media/dns-custom-domain/functionshostname.png)

导航到 DNS 区域，单击“+ 记录集”。 在“添加记录集”边栏选项卡上填写以下信息，单击“确定”创建该记录集。

|属性  |值  |说明  |
|---------|---------|---------|
|名称     | myfunctionapp        | 此值连同域名标签是自定义域名的 FQDN。        |
|Type     | CNAME        | 使用 CNAME 记录相当于使用别名。        |
|TTL     | 1        | 1 表示 1 小时        |
|TTL 单位     | 小时        | 小时用作时间计量单位         |
|别名     | adatumfunction.azurewebsites.net        | 为其创建别名的 DNS 名称，在本示例中，为默认提供给函数应用的 adatumfunction.azurewebsites.net DNS 名称。        |

导航回到函数应用，单击“平台功能”，在“网络”下面单击“自定义域”，在“主机名”下面单击“+ 添加主机名”。

在“添加主机名”边栏选项卡上的“主机名”文本字段中输入 CNAME 记录，单击“验证”。 如果可以找到该记录，则会出现“添加主机名”按钮。 单击“添加主机名”以添加该别名。

![函数应用添加主机名边栏选项卡](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>公共 IP 地址

若要为使用公共 IP 地址资源（例如应用程序网关、负载均衡器、云服务、资源管理器 VM 和经典 VM）的服务配置自定义域，需使用 CNAME 记录。

导航到“网络” > “公共 IP 地址”，选择“公共 IP 资源”，单击“配置”。 记下显示的 IP 地址。

![公共 IP 边栏选项卡](./media/dns-custom-domain/publicip.png)

导航到 DNS 区域，单击“+ 记录集”。 在“添加记录集”边栏选项卡上填写以下信息，单击“确定”创建该记录集。


|属性  |值  |说明  |
|---------|---------|---------|
|名称     | mywebserver        | 此值连同域名标签是自定义域名的 FQDN。        |
|Type     | A        | 由于资源是 IP 地址，因此应使用 A 记录。        |
|TTL     | 1        | 1 表示 1 小时        |
|TTL 单位     | 小时        | 小时用作时间计量单位         |
|IP 地址     | <your ip address>       | 公共 IP 地址。|

![创建 A 记录](./media/dns-custom-domain/arecord.png)

创建 A 记录后，运行 `nslookup` 来验证记录解析。

![公共 IP DNS 查找](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>应用服务（Web 应用）

下面逐步介绍如何配置应用服务 Web 应用的自定义域。

导航到“Web 和移动” > “应用服务”，选择要配置自定义域名的资源，单击“自定义域”。

记下“自定义域”边栏选项卡上的当前 URL，此地址用作创建的 DNS 记录的别名。

![自定义域边栏选项卡](./media/dns-custom-domain/url.png)

导航到 DNS 区域，单击“+ 记录集”。 在“添加记录集”边栏选项卡上填写以下信息，单击“确定”创建该记录集。


|属性  |值  |说明  |
|---------|---------|---------|
|名称     | mywebserver        | 此值连同域名标签是自定义域名的 FQDN。        |
|Type     | CNAME        | 使用 CNAME 记录相当于使用别名。 如果资源使用了 IP 地址，则会使用 A 记录。        |
|TTL     | 1        | 1 表示 1 小时        |
|TTL 单位     | 小时        | 小时用作时间计量单位         |
|别名     | webserver.azurewebsites.net        | 为其创建别名的 DNS 名称，在本示例中，为默认提供给 Web 应用的 webserver.azurewebsites.net DNS 名称。        |


![创建 CNAME 记录](./media/dns-custom-domain/createcnamerecord.png)

导航回到为自定义域名配置的应用服务。 依次单击“自定义域”、“主机名”。 若要添加所创建的 CNAME 记录，请单击“+ 添加主机名”。

![图 1](./media/dns-custom-domain/figure1.png)

完成此过程后，请运行 **nslookup** 来验证名称解析是否正常工作。

![图 1](./media/dns-custom-domain/finalnslookup.png)

若要详细了解如何将自定义域映射到应用服务，请访问[将现有的自定义 DNS 名称映射到 Azure Web 应用](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)。

如果需要购买自定义域，请访问[购买 Azure Web 应用的自定义域名](../app-service/custom-dns-web-site-buydomains-web-app.md)来详细了解应用服务域。

## <a name="blob-storage"></a>Blob 存储

下面逐步介绍如何使用 asverify 方法配置 Blob 存储帐户的 CNAME 记录。 此方法可确保不会造成停机。

导航到“存储” > “存储帐户”，选择自己的存储帐户，单击“自定义域”。 记下步骤 2 中的 FQDN，此值用于创建第一条 CNAME 记录

![Blob 存储自定义域](./media/dns-custom-domain/blobcustomdomain.png)

导航到 DNS 区域，单击“+ 记录集”。 在“添加记录集”边栏选项卡上填写以下信息，单击“确定”创建该记录集。


|属性  |值  |说明  |
|---------|---------|---------|
|名称     | asverify.mystorageaccount        | 此值连同域名标签是自定义域名的 FQDN。        |
|Type     | CNAME        | 使用 CNAME 记录相当于使用别名。        |
|TTL     | 1        | 1 表示 1 小时        |
|TTL 单位     | 小时        | 小时用作时间计量单位         |
|别名     | asverify.adatumfunctiona9ed.blob.core.windows.net        | 为其创建别名的 DNS 名称，在本示例中，为默认提供给存储帐户的 asverify.adatumfunctiona9ed.blob.core.windows.net DNS 名称。        |

单击“存储” > “存储帐户”导航回到存储帐户，选择该存储帐户，单击“自定义域”。 在文本框中键入创建的别名（不带 asverify 前缀），选中“**使用间接 CNAME 验证”，单击“保存”。 完成此步骤后，返回 DNS 区域，并创建不带 asverify 前缀的 CNAME 记录。  此后，可以安全删除带有 cdnverify 前缀的 CNAME 记录。

![Blob 存储自定义域](./media/dns-custom-domain/indirectvalidate.png)

运行 `nslookup` 验证 DNS 解析

若要详细了解如何将自定义域映射到 Blob 存储终结点，请访问[为 Blob 存储终结点配置自定义域名](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

下面逐步介绍如何使用 cdnverify 方法配置 CDN 终结点的 CNAME 记录。 此方法可确保不会造成停机。

导航到“网络” > “CDN 配置文件”，选择自己的 CDN 配置文件，单击“常规”下面的“终结点”。

选择正在使用的终结点，单击“+ 自定义域”。 记下“终结点主机名”，因为此值是该 CNAME 记录指向的记录。

![CDN 自定义域](./media/dns-custom-domain/endpointcustomdomain.png)

导航到 DNS 区域，单击“+ 记录集”。 在“添加记录集”边栏选项卡上填写以下信息，单击“确定”创建该记录集。

|属性  |值  |说明  |
|---------|---------|---------|
|名称     | cdnverify.mycdnendpoint        | 此值连同域名标签是自定义域名的 FQDN。        |
|Type     | CNAME        | 使用 CNAME 记录相当于使用别名。        |
|TTL     | 1        | 1 表示 1 小时        |
|TTL 单位     | 小时        | 小时用作时间计量单位         |
|别名     | cdnverify.adatumcdnendpoint.azureedge.net        | 为其创建别名的 DNS 名称，在本示例中，为默认提供给存储帐户的 cdnverify.adatumcdnendpoint.azureedge.net DNS 名称。        |

单击“网络” > “CDN 配置文件”导航回到 CDN 终结点，选择自己的 CDN 配置文件。 单击“+ 自定义域”并输入不带 cdnverify 前缀的 CNAME 记录别名，单击“添加”。

完成此步骤后，返回 DNS 区域，并创建不带 cdnverify 前缀的 CNAME 记录。  此后，可以安全删除带有 cdnverify 前缀的 CNAME 记录。 有关 CDN 以及如何在不执行中间注册步骤的情况下配置自定义域的详细信息，请访问[将 Azure CDN 内容映射到自定义域](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)。

## <a name="next-steps"></a>后续步骤

了解如何[为 Azure 中托管的服务配置反向 DNS](dns-reverse-dns-for-azure-services.md)。