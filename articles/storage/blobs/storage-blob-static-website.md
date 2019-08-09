---
title: Azure 存储中的静态网站托管
description: Azure 存储静态网站托管提供经济高效、可缩放的解决方案用于托管新式 Web 应用程序。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 9a751956f73ca4a88545e034a32d699c0766dd1d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855378"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure 存储中的静态网站托管

可以直接从名为 *$web*的存储容器提供静态内容 (HTML、CSS、JavaScript 和映像文件)。 在 Azure 存储中托管内容使你可以使用包含[Azure Functions](/azure/azure-functions/functions-overview)和其他平台即服务 (PaaS) 服务的无服务器体系结构。

> [!NOTE]
> 如果站点依赖于服务器端代码, 请改用[Azure App Service](/azure/app-service/overview) 。

## <a name="setting-up-a-static-website"></a>设置静态网站

静态网站宿主是必须在存储帐户上启用的一项功能。

若要启用静态网站宿主, 请选择默认文件的名称, 然后根据需要提供自定义404页的路径。 如果帐户中尚不存在名为 **$web**的 blob 存储容器, 则会为你创建一个。 将站点的文件添加到此容器。

有关分步指南, 请参阅[在 Azure 存储中承载静态网站](storage-blob-static-website-how-to.md)。

![Azure 存储静态网站指标 - 指标](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$Web**容器中的文件区分大小写, 通过匿名访问请求提供服务, 只能通过读取操作来使用。

## <a name="uploading-content"></a>正在上载内容

你可以使用这些工具中的任何一种将内容上载到 **$web**容器:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell 模块](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 扩展](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>查看内容

用户可以使用网站的公共 URL 查看浏览器中的站点内容。 可以使用 Azure 门户、Azure CLI 或 PowerShell 查找 URL。 使用此表作为指南。

|Tool| 指南 |
|----|----|
|**Azure 门户** | [使用 Azure 门户查找网站 URL](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [使用 Azure CLI 查找网站 URL](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell 模块** | [使用 PowerShell 查找网站 URL](storage-blob-static-website-how-to.md#powershell-find-url) |

网站的 URL 包含区域代码。 例如, URL `https://contosoblobaccount.z22.web.core.windows.net/`包含地区代码`z22`。

尽管该代码必须保留 URL, 但它仅供内部使用, 无需以其他任何方式使用这些代码。

当你启用静态网站宿主时指定的索引文档将在用户打开网站但未指定特定文件时显示 (例如: `https://contosoblobaccount.z22.web.core.windows.net`)。  

如果服务器返回404错误, 并且你在启用网站时未指定错误文档, 则会向用户返回默认404页。

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>设置 web 容器的公共访问级别的影响

你可以修改 **$web**容器的公共访问级别, 但这不会影响主静态网站终结点, 因为这些文件是通过匿名访问请求提供的。 这意味着对所有文件的公共 (只读) 访问。

以下屏幕截图显示了 "Azure 门户中的" 公共访问级别 "设置:

![显示如何在门户中设置公共访问级别的屏幕截图](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

尽管主静态网站终结点不受影响, 但对公共访问级别的更改会影响主 blob 服务终结点。

例如, 如果你将 **$web**容器的公共访问级别从**私有 (无匿名访问)** 更改为**blob (仅限对 blob 进行匿名读取访问)** , 则将访问主静态网站终结点`https://contosoblobaccount.z22.web.core.windows.net/index.html`的公共访问级别不会更改。

但是, 对主 blob 服务终结点`https://contosoblobaccount.blob.core.windows.net/$web/index.html`的公共访问权限会从 private 更改为 public。 现在用户可以使用这两个终结点中的任意一个来打开该文件。

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>内容交付网络 (CDN) 和安全套接字层 (SSL) 支持

若要通过自定义域和 HTTPS 使静态网站文件可用, 请参阅[使用 Azure CDN 通过 HTTPS 访问包含自定义域的 blob](storage-https-custom-domain-cdn.md)。 作为此过程的一部分, 你需要将 CDN 指向主*静态网站*终结点, 而不是主*blob 服务*终结点。 你可能需要等待几分钟时间, 你的内容才可见, 因为 CDN 配置不会立即执行。

更新静态网站时, 请确保通过清除 CDN 终结点来清除 CDN 边缘服务器上的缓存内容。 有关详细信息，请参阅[清除 Azure CDN 终结点](../../cdn/cdn-purge-endpoint.md)。

> [!NOTE]
> HTTPS 通过帐户 web 终结点以本机方式受到支持, 因此可通过 HTTP 和 HTTPS 访问 web 终结点。 但是, 如果将存储帐户配置为需要通过 HTTPS 进行安全传输, 则用户必须使用 HTTPS 终结点。 有关详细信息, 请参阅[在 Azure 存储中要求安全传输](../common/storage-require-secure-transfer.md)。
>
> 使用 HTTPS 上的自定义域需要目前使用 Azure CDN。

## <a name="custom-domain-names"></a>自定义域名

可以通过自定义域使静态网站可用。 若要了解详细信息, 请参阅[为 Azure 存储帐户配置自定义域名](storage-custom-domain-name.md)。

若要深入了解如何在 Azure 上托管你的域, 请参阅[在 Azure DNS 中托管你的域](../../dns/dns-delegate-domain-azure-dns.md)。

## <a name="pricing"></a>定价

可以免费启用静态网站托管。 仅对你的站点利用的 blob 存储和运营成本计费。 如需详细了解 Azure Blob 存储价格，请参阅 [Azure Blob 存储定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="metrics"></a>指标

可以在静态网站页面上启用指标。 一旦启用了指标, 就会在 "指标" 仪表板中报告 **$web**容器中的文件的流量统计信息。

若要在静态网站页面上启用指标, 请参阅[在静态网站页面上启用指标](storage-blob-static-website-how-to.md#metrics)。

## <a name="next-steps"></a>后续步骤

* [在 Azure 存储中托管静态网站](storage-blob-static-website-how-to.md)
* [使用 Azure CDN 通过 HTTPS 访问包含自定义域的 blob](storage-https-custom-domain-cdn.md)
* [为 blob 或 Web 终结点配置自定义域名](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure 应用服务](/azure/app-service/overview)
* [生成首个无服务器 Web 应用程序](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教程：在 Azure DNS 中托管域](../../dns/dns-delegate-domain-azure-dns.md)
