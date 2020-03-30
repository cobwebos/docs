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
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370485"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure 存储中的静态网站托管

可以直接从名为 *$web*的存储容器提供静态内容（HTML、CSS、JavaScript 和图像文件）。 在 Azure 存储中托管内容使您能够使用包含[Azure 函数](/azure/azure-functions/functions-overview)和其他平台即服务 （PaaS） 服务的无服务器体系结构。

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> 如果站点依赖于服务器端代码，请改用[Azure 应用服务](/azure/app-service/overview)。

## <a name="setting-up-a-static-website"></a>设置静态网站

静态网站托管是您必须在存储帐户上启用的功能。

要启用静态网站托管，请选择默认文件的名称，然后选择提供自定义 404 页的路径。 如果帐户中不存在名为 **$web**的 blob 存储容器，则为您创建一个 blob 存储容器。 将站点的文件添加到此容器。

有关分步指导，请参阅[在 Azure 存储中托管静态网站](storage-blob-static-website-how-to.md)。

![Azure 存储静态网站指标 - 指标](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$web**容器中的文件区分大小写，通过匿名访问请求提供，只能通过读取操作提供。

## <a name="uploading-content"></a>上传内容

您可以使用以下任一工具将内容上载到 **$web**容器：

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell 模块](storage-blob-static-website-how-to.md#powershell)
> * [阿兹比贝](../common/storage-use-azcopy-v10.md)
> * [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 扩展](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>查看内容

用户可以使用网站的公共 URL 从浏览器查看网站内容。 您可以使用 Azure 门户、Azure CLI 或 PowerShell 查找 URL。 使用此表作为指南。

|工具| 指南 |
|----|----|
|**Azure 门户** | [使用 Azure 门户查找网站 URL](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [使用 Azure CLI 查找网站 URL](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell 模块** | [使用 PowerShell 查找网站网址](storage-blob-static-website-how-to.md#powershell-find-url) |

站点的 URL 包含区域代码。 例如，URL`https://contosoblobaccount.z22.web.core.windows.net/`包含区域代码`z22`。

虽然该代码必须保留在 URL 中，但它仅供内部使用，您不必以任何其他方式使用该代码。

启用静态网站托管时指定的索引文档，在用户打开网站时显示，并且不指定特定文件（例如： `https://contosoblobaccount.z22.web.core.windows.net`  

如果服务器返回 404 错误，并且您在启用网站时未指定错误文档，则默认 404 页将返回给用户。

> [!NOTE]
> 静态网站不支持[CORS。](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>设置 Web 容器的公共访问级别的影响

您可以修改 **$web**容器的公共访问级别，但这不会影响主静态网站终结点，因为这些文件是通过匿名访问请求提供的。 这意味着对所有文件的公共（只读）访问。

以下屏幕截图显示了 Azure 门户中的公共访问级别设置：

![显示如何在门户中设置公共访问级别的屏幕截图](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

虽然主静态网站终结点不受影响，但公共访问级别的更改确实会影响主 Blob 服务终结点。

例如，如果将 **$web**容器的公共访问级别从**专用（无匿名访问）** 更改为**Blob（仅限 Blob 的匿名读取访问），** 则对主静态网站终结点`https://contosoblobaccount.z22.web.core.windows.net/index.html`的公共访问级别不会更改。

但是，对主 Blob 服务终结点`https://contosoblobaccount.blob.core.windows.net/$web/index.html`的公共访问确实会从私有更改为公共。 现在，用户可以使用这两个终结点之一打开该文件。

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>将自定义域映射到静态网站 URL

您可以通过自定义域使静态网站可用。 

对于自定义域启用 HTTP 访问更容易，因为 Azure 存储本机支持它。 要启用 HTTPS，必须使用 Azure CDN，因为 Azure 存储尚未在本机上支持具有自定义域的 HTTPS。 请参阅[将自定义域映射到 Azure Blob 存储终结点](storage-custom-domain-name.md)，以便获得分步指导。

如果存储帐户配置为需要通过 HTTPS[安全传输](../common/storage-require-secure-transfer.md)，则用户必须使用 HTTPS 终结点。 

> [!TIP]
> 请考虑在 Azure 上托管域。 有关详细信息，请参阅在[Azure DNS 中托管域](../../dns/dns-delegate-domain-azure-dns.md)。

## <a name="adding-http-headers"></a>添加 HTTP 标头

无法将标头配置为静态网站功能的一部分。 但是，可以使用 Azure CDN 添加标头和追加（或覆盖）标头值。 请参阅[Azure CDN 的标准规则引擎引用](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference)。

如果要使用标头来控制缓存，请参阅[使用缓存规则控制 Azure CDN 缓存行为](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)。

## <a name="pricing"></a>定价

您可以免费启用静态网站托管。 您仅对站点使用的 Blob 存储和运营成本收费。 如需详细了解 Azure Blob 存储价格，请参阅 [Azure Blob 存储定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="metrics"></a>指标

您可以在静态网站页面上启用指标。 启用指标后 **，$web**容器中文件的流量统计信息将报告在指标仪表板中。

要在静态网站页面上启用指标，请参阅[在静态网站页面上启用指标](storage-blob-static-website-how-to.md#metrics)。

## <a name="next-steps"></a>后续步骤

* [在 Azure 存储中托管静态网站](storage-blob-static-website-how-to.md)
* [将自定义域映射到 Azure Blob 存储终结点](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure 应用服务](/azure/app-service/overview)
* [生成首个无服务器 Web 应用程序](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [教程：在 Azure DNS 中托管域](../../dns/dns-delegate-domain-azure-dns.md)
