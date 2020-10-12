---
title: 将静态网站与 Azure CDN-Azure 存储集成
description: 了解如何使用 Azure 内容分发网络 (CDN) 从 Azure 存储帐户缓存静态网站内容。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: 02b7e02c33161db33420e2efe1ef4b70a138d127
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465212"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>将静态网站与 Azure CDN 集成

可以 [ (CDN) 启用 Azure 内容交付网络 ](../../cdn/cdn-overview.md) ，以便从 azure 存储帐户中托管的 [静态网站](storage-blob-static-website.md) 缓存内容。 你可以使用 Azure CDN 为你的静态网站配置自定义域终结点，设置自定义的 TLS/SSL 证书，并配置自定义重写规则。 配置 Azure CDN 需额外付费，但用户从世界上的任何位置访问你的网站时，其延迟会始终很低。 Azure CDN 还可以通过自己的证书提供 TLS 加密。 

有关 Azure CDN 定价的详细信息，请参阅 [Azure CDN 定价](https://azure.microsoft.com/pricing/details/cdn/)。

## <a name="enable-azure-cdn-for-your-static-website"></a>为静态网站启用 Azure CDN

你可以直接从你的存储帐户启用静态网站 Azure CDN。 若要为 CDN 终结点指定高级配置设置（例如[大文件下载优化](../../cdn/cdn-optimization-overview.md#large-file-download)），可以改用 [Azure CDN 扩展](../../cdn/cdn-create-new-endpoint.md)来创建 CDN 配置文件和终结点。

1. 在 Azure 门户中找到存储帐户并显示帐户概览。

1. 在 " **Blob 服务** " 菜单下，选择 " **Azure CDN** " 以打开 **Azure CDN** 页面：

    ![创建 CDN 终结点](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. 在 " **CDN 配置文件** " 部分中，指定是创建新的 CDN 配置文件还是使用现有的 CDN 配置文件。 CDN 配置文件是共享定价层和提供者的 CDN 终结点的集合。 然后输入订阅中唯一的 CDN 的名称。

1. 指定 CDN 终结点的定价层。 若要了解有关定价的详细信息，请参阅 [内容分发网络定价](https://azure.microsoft.com/pricing/details/cdn/)。 有关每个层提供的功能的详细信息，请参阅 [比较 Azure CDN 产品功能](../../cdn/cdn-features.md)。

1. 在“CDN 终结点名称”字段中，指定 CDN 终结点的名称。**** CDN 终结点在 Azure 中必须是唯一的，并提供终结点 URL 的第一部分。 此窗体验证终结点名称是否是唯一的。

1. 在 " **源主机名** " 字段中指定静态网站终结点。 

   若要查找静态网站终结点，请导航到存储帐户的“静态网站”设置。****  复制主终结点，并将其粘贴到 CDN 配置中。

   > [!IMPORTANT]
   > 请确保删除协议标识符 (*例如*，HTTPS) ，以及 URL 中的尾部反斜杠。 例如，如果静态网站终结点为 `https://mystorageaccount.z5.web.core.windows.net/` ，则需 `mystorageaccount.z5.web.core.windows.net` 在 " **源主机名** " 字段中指定。

   下图显示了一个终结点配置示例：

   ![显示 CDN 终结点配置示例的屏幕截图](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. 选择 " **创建**"，然后等待 CDN 预配。 创建终结点之后，它会出现在终结点列表中。  (如果窗体中有任何错误，该字段旁边会出现一个感叹号。 ) 

1. 若要验证 CDN 终结点是否已正确配置，请单击该终结点，导航到其设置。 在存储帐户的 CDN 概览中找到终结点主机名，导航到终结点，如下图所示。 CDN 终结点的格式将类似于 `https://staticwebsitesamples.azureedge.net`。

    ![显示 CDN 终结点概览的屏幕截图](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. 预配 CDN 终结点后，导航到 CDN 终结点会显示你之前上传到静态网站的 index.html 文件的内容。

1. 若要查看 CDN 终结点的源设置，请导航到 CDN 终结点的“设置”部分下的“源”。******** 此时会看到“源类型”字段设置为“自定义源”，“源主机名”字段显示静态网站终结点。**********

    ![显示 CDN 终结点的源设置的屏幕截图](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>删除 CDN 中的内容

如果不再需要在 Azure CDN 中缓存对象，则可以执行以下步骤之一：

* 将容器设为专用容器而不是公用容器。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](storage-manage-access-to-resources.md)。
* 通过 Azure 门户禁用或删除 CDN 终结点。
* 将托管服务修改为不再响应此对象的请求。

已在 Azure CDN 中缓存的对象保持缓存状态，直至对象的生存时间到期，或直至[清除](../../cdn/cdn-purge-endpoint.md)终结点为止。 当生存时间到期时，Azure CDN 会确定 CDN 终结点是否仍有效，以及是否仍可对该对象进行匿名访问。 如果不能，则不再缓存该对象。

## <a name="next-steps"></a>后续步骤

 (可选) 将自定义域添加到 Azure CDN 终结点。 请参阅 [教程：将自定义域添加到 Azure CDN 终结点](../../cdn/cdn-map-content-to-custom-domain.md)。
