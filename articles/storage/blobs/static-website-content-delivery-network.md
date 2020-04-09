---
title: 将静态网站与 Azure CDN - Azure 存储集成
description: 了解如何使用 Azure 内容传递网络 （CDN） 从 Azure 存储帐户缓存静态网站内容。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: 4516e9f48174a0f1f5201c46cf114badf13d99d6
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878795"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>将静态网站与 Azure CDN 集成

您可以启用[Azure 内容传递网络 （CDN）](../../cdn/cdn-overview.md)从托管在 Azure 存储帐户中的[静态网站](storage-blob-static-website.md)缓存内容。 可以使用 Azure CDN 为静态网站配置自定义域终结点、预配自定义 TLS/SSL 证书以及配置自定义重写规则。 配置 Azure CDN 需额外付费，但用户从世界上的任何位置访问你的网站时，其延迟会始终很低。 Azure CDN 还提供带有您自己的证书的 TLS 加密。 

有关 Azure CDN 定价的详细信息，请参阅 [Azure CDN 定价](https://azure.microsoft.com/pricing/details/cdn/)。

## <a name="enable-azure-cdn-for-your-static-website"></a>为静态网站启用 Azure CDN

可以直接从存储帐户为静态网站启用 Azure CDN。 若要为 CDN 终结点指定高级配置设置（例如[大文件下载优化](../../cdn/cdn-optimization-overview.md#large-file-download)），可以改用 [Azure CDN 扩展](../../cdn/cdn-create-new-endpoint.md)来创建 CDN 配置文件和终结点。

1. 在 Azure 门户中找到存储帐户并显示帐户概览。

1. 在**Blob 服务**菜单下，选择**Azure CDN**以打开**Azure CDN**页面：

    ![创建 CDN 终结点](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. 在**CDN 配置文件**部分中，指定是创建新 CDN 配置文件还是使用现有 CDN 配置文件。 CDN 配置文件是共享定价层和提供程序的 CDN 终结点的集合。 然后输入订阅中唯一的 CDN 的名称。

1. 指定 CDN 终结点的定价层。 要了解有关定价的更多信息，请参阅[内容交付网络定价](https://azure.microsoft.com/pricing/details/cdn/)。 有关每个层可用的功能的详细信息，请参阅比较 Azure [CDN 产品功能](../../cdn/cdn-features.md)。

1. 在“CDN 终结点名称”字段中，指定 CDN 终结点的名称。**** CDN 终结点必须在 Azure 中是唯一的，并提供终结点 URL 的第一部分。 该窗体验证终结点名称是否唯一。

1. 在 **"原点"主机名**字段中指定静态网站终结点。 

   若要查找静态网站终结点，请导航到存储帐户的“静态网站”设置。****  复制主终结点并将其粘贴到 CDN 配置中。

   > [!IMPORTANT]
   > 请确保删除 URL 中的协议标识符（*例如*HTTPS）和尾随斜杠。 例如，如果静态网站终结点为`https://mystorageaccount.z5.web.core.windows.net/`，则将在`mystorageaccount.z5.web.core.windows.net`**"原点"主机名**字段中指定。

   下图显示了一个终结点配置示例：

   ![显示 CDN 终结点配置示例的屏幕截图](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. 选择 **"创建**"，然后等待 CDN 进行预配。 创建终结点之后，它会出现在终结点列表中。 （如果窗体中有任何错误，则该字段旁边会显示一个感叹号。

1. 若要验证 CDN 终结点是否已正确配置，请单击该终结点，导航到其设置。 在存储帐户的 CDN 概览中找到终结点主机名，导航到终结点，如下图所示。 CDN 终结点的格式将类似于 `https://staticwebsitesamples.azureedge.net`。

    ![显示 CDN 终结点概览的屏幕截图](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. 预配 CDN 终结点后，导航到 CDN 终结点将显示您以前上载到静态网站的 index.html 文件的内容。

1. 若要查看 CDN 终结点的源设置，请导航到 CDN 终结点的“设置”部分下的“源”。******** 此时会看到“源类型”字段设置为“自定义源”，“源主机名”字段显示静态网站终结点。**********

    ![显示 CDN 终结点的源设置的屏幕截图](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>删除 CDN 中的内容

如果不再需要在 Azure CDN 中缓存对象，则可以执行以下步骤之一：

* 将容器设为专用容器而不是公用容器。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](storage-manage-access-to-resources.md)。
* 通过 Azure 门户禁用或删除 CDN 终结点。
* 将托管服务修改为不再响应此对象的请求。

已在 Azure CDN 中缓存的对象将保持缓存状态，直到对象的生存时间过期或清除[终结点为止。](../../cdn/cdn-purge-endpoint.md) 当生存时间到期时，Azure CDN 会确定 CDN 终结点是否仍有效，以及是否仍可对该对象进行匿名访问。 如果不能，则不再缓存该对象。

## <a name="next-steps"></a>后续步骤

（可选）将自定义域添加到 Azure CDN 终结点。 请参阅[教程：将自定义域添加到 Azure CDN 终结点](../../cdn/cdn-map-content-to-custom-domain.md)。
