---
title: Azure 存储中的静态网站托管（预览版）| Microsoft Docs
description: Azure 存储现提供静态网站托管（预览版），这是方便托管新式 Web 应用程序的经济高效、可缩放解决方案。
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: e53b573a27f0b1462ccf1170bbde2f8af01d0d3a
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397469"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Azure 存储中的静态网站托管（预览版）
Azure 存储现提供静态网站托管（预览版），可便于用户在 Azure 上部署经济高效、可缩放的新式 Web 应用程序。 在静态网站上，网页包含静态内容和 JavaScript 或其他客户端代码。 相比之下，动态网站依赖服务器端代码，可以使用 [Azure Web 应用程序](/app-service/app-service-web-overview.md)托管动态网站。

随着部署向经济高效的弹性模型转变，能否无需管理服务器即可传递 Web 内容变得至关重要。 在 Azure 存储中引入静态网站托管让这成为可能，通过利用 [Azure Functions](/azure-functions/functions-overview.md) 以及其他 PaaS 服务的无服务器体系结构带来了丰富的后端功能。

## <a name="how-does-it-work"></a>工作原理
对存储帐户启用静态网站时，将以 `<account-name>.<zone-name>.web.core.windows.net` 形式新建 Web 服务终结点。

Web 服务终结点始终允许匿名读取访问，返回格式化 HTML 页面来响应服务错误，并仅允许执行对象读取操作。 Web 服务终结点在请求获取的目录中返回根目录和所有子目录的索引文档。 如果存储服务返回 404 错误，Web 终结点返回自定义错误文档（如果已配置的话）。

静态网站内容托管在特殊容器“$web”中。 在启用过程中，系统会为你创建“$web”（若无）。 可以使用 Web 终结点在帐户根处访问“$web”内容。 例如，如果具有相应名称的文档位于 $web 的根目录中，`https://contoso.z4.web.core.windows.net/` 返回你为网站配置的索引文档。

若要将内容上传到网站，请使用 blob 存储终结点。 若要上传可以在帐户根处访问的 blob“image.jpg”，请使用以下 URL：`https://contoso.blob.core.windows.net/$web/image.jpg`。 可以在相应的 Web 终结点 `https://contoso.z4.web.core.windows.net/image.jpg` 处通过 Web 浏览器查看已上传的图像。


## <a name="custom-domain-names"></a>自定义域名
可以使用自定义域来托管 Web 内容。 为此，请按照[为 Azure 存储帐户配置自定义域名](storage-custom-domain-name.md)中的说明操作。 若要通过 HTTPS 访问在自定义域名处托管的网站，请参阅[使用 Azure CDN 通过 HTTPS 使用自定义域访问 blob](storage-https-custom-domain-cdn.md)。

## <a name="pricing-and-billing"></a>定价和计费
静态网站托管无需额外付费。 如需详细了解 Azure Blob 存储价格，请参阅 [Azure Blob 存储定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="quickstart"></a>快速入门
### <a name="azure-portal"></a>Azure 门户
若要开始在 Azure 存储中托管 Web 应用程序，可以使用 Azure 门户配置此功能，并单击左侧导航栏中“设置”下的“静态网站(预览)”。 单击“已启用”，并输入索引文档名称和（可选）自定义错误文档路径。

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

将 Web 资产上传到在静态网站启用过程中创建的“$web”容器。 可以直接在 Azure 门户中这样做，也可以利用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)上传整个目录结构。 请务必添加采用已配置命名的索引文档。 在此示例中，索引文档的名称为“index.html”。

> [!NOTE]
> 文档名称区分大小写，因此需要完全匹配存储中文件的名称。

最后，转到 Web 终结点来测试网站。

## <a name="faq"></a>常见问题解答
**静态网站是否适用于所有存储帐户类型？**  
否，静态网站托管仅适用于 GPv2 标准存储帐户。

**新 Web 终结点是否支持存储 VNET 和防火墙规则？**  
是，新 Web 终结点遵循为存储帐户配置的 VNET 和防火墙规则。

## <a name="next-steps"></a>后续步骤
* [使用 Azure CDN 通过 HTTPS 访问包含自定义域的 Blob](storage-https-custom-domain-cdn.md)
* [为 blob 或 Web 终结点配置自定义域名](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web 应用](/app-service/app-service-web-overview.md)
* [生成首个无服务器 Web 应用程序](https://aka.ms/static-serverless-webapp)
