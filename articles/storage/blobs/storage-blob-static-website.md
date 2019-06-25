---
title: Azure 存储中的静态网站托管
description: Azure 存储静态网站托管提供经济高效、可缩放的解决方案用于托管新式 Web 应用程序。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427984"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure 存储中的静态网站托管

您可以提供静态内容 （HTML、 CSS、 JavaScript 和图像文件） 直接从名为的存储容器 *$web*。 托管 Azure 存储中的内容，您可以使用无服务器体系结构，包括[Azure Functions](/azure/azure-functions/functions-overview)和其他平台即服务 (PaaS) 服务。

> [!NOTE]
> 如果你的站点取决于服务器端代码，使用[Azure 应用服务](/azure/app-service/overview)相反。

## <a name="setting-up-a-static-website"></a>静态网站设置

静态网站托管是具有存储帐户上启用的功能。

若要启用静态网站托管，选择默认的文件的名称，然后根据需要提供自定义 404 页的路径。 如果名为的 blob 存储容器 **$web**尚不存在在帐户中，为您创建一个。 将你的站点的文件添加到此容器。

有关分步指南，请参阅[托管在 Azure 存储中的静态网站](storage-blob-static-website-how-to.md)。

![Azure 存储静态网站指标 - 指标](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

中的文件 **$web**容器，区分大小写，匿名访问请求，并且只通过读取操作可以通过提供服务。

## <a name="uploading-content"></a>上传内容

可以使用任何一种工具上传到的内容 **$web**容器：

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell 模块](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 扩展](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>查看内容

通过使用网站的公共 URL，用户可以查看从浏览器的站点内容。 可以通过使用 Azure 门户、 Azure CLI 或 PowerShell 找到的 URL。 使用此表作为指南。

|Tool| 指南 |
|----|----|
|**Azure 门户** | [使用 Azure 门户中查找网站的 URL](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [使用 Azure CLI 查找网站的 URL](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell 模块** | [使用 PowerShell 查找网站的 URL](storage-blob-static-website-how-to.md#powershell-find-url) |

你的网站 URL 会包含区域代码。 例如 URL`https://contosoblobaccount.z22.web.core.windows.net/`包含区域代码`z22`。

虽然该代码必须保留该 URL，它是仅供内部使用，并无需任何其他方式使用该代码。

索引指定文档的情况下，启用静态网站托管，用户打开站点，并且未指定特定文件时显示 (例如： `https://contosoblobaccount.z22.web.core.windows.net`)。  

如果服务器将返回 404 错误，并且你尚未启用该网站时指定的错误文档，则默认 404 页返回给用户。

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>设置的影响 web 容器的公共访问级别

您可以修改的公共访问级别 **$web**容器，但这没有任何影响主静态网站终结点因为这些文件通过匿名访问请求提供服务。 这意味着公共 （只读） 访问的所有文件。

下面的屏幕截图显示了在 Azure 门户中的公共访问级别设置：

![显示如何在门户中设置公共访问级别的屏幕截图](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

虽然主静态网站终结点不受影响，对公共访问级别的更改会影响主 blob 服务终结点。

例如，如果您更改的公共访问级别 **$web**容器中的通过**专用 （无匿名访问权限）** 到**Blob （仅限对 blob 的匿名读取访问）** ，则对主静态网站终结点的公共访问级别`https://contosoblobaccount.z22.web.core.windows.net/index.html`不会更改。

但是，公共访问权限到主 blob 服务终结点`https://contosoblobaccount.blob.core.windows.net/$web/index.html`does 从私有更改为公共。 现在用户可以使用这些两个终结点之一来打开该文件。

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>内容分发网络 (CDN) 和安全套接字层 (SSL) 支持

若要使你的静态网站文件通过自定义域和 HTTPS 上可用，请参阅[使用 Azure CDN 通过 HTTPS 访问包含自定义域的 blob](storage-https-custom-domain-cdn.md)。 作为此过程的一部分，你需要指向 CDN 的主数据库*静态网站*而不是主终结点*blob 服务*终结点。 您可能需要等待几分钟之后你的内容是可见的因为 CDN 配置不会被立即执行。

当更新静态网站时，请确保通过清除 CDN 终结点清除缓存在 CDN 边缘服务器上的内容。 有关详细信息，请参阅[清除 Azure CDN 终结点](../../cdn/cdn-purge-endpoint.md)。

> [!NOTE]
> HTTPS 通过帐户的 web 终结点，因此可通过 HTTP 和 HTTPS 访问 web 终结点是本机支持。 但是，如果存储帐户配置为通过 HTTPS 需要安全传输，用户必须使用 HTTPS 终结点。 有关详细信息，请参阅[需要在 Azure 存储中的安全传输](../common/storage-require-secure-transfer.md)。
>
> 通过 HTTPS 自定义域使用要求在此时间的 Azure CDN 使用。

## <a name="custom-domain-names"></a>自定义域名

您可以提供静态网站通过自定义域。 若要了解详细信息，请参阅[配置 Azure 存储帐户的自定义域名](storage-custom-domain-name.md)。

深入了解 Azure 上托管域，请参阅[托管在 Azure DNS 中的域](../../dns/dns-delegate-domain-azure-dns.md)。

## <a name="pricing"></a>定价

可以让静态网站托管免费。 计费仅适用于你的站点使用的 blob 存储和操作成本。 如需详细了解 Azure Blob 存储价格，请参阅 [Azure Blob 存储定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="metrics"></a>度量值

你可以启用静态网站页上的度量值。 一旦您启用指标，流量中的文件的统计信息 **$web**容器报告指标仪表板中。

若要启用静态网站页上的指标，请参阅[启用静态网站页上的度量值](storage-blob-static-website-how-to.md#metrics)。

## <a name="next-steps"></a>后续步骤

* [托管在 Azure 存储中的静态网站](storage-blob-static-website-how-to.md)
* [使用 Azure CDN 通过 HTTPS 访问包含自定义域的 blob](storage-https-custom-domain-cdn.md)
* [为 blob 或 Web 终结点配置自定义域名](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure 应用服务](/azure/app-service/overview)
* [生成首个无服务器 Web 应用程序](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教程：在 Azure DNS 中托管域](../../dns/dns-delegate-domain-azure-dns.md)
