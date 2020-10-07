---
title: 快速入门 - 将 Azure 存储帐户与 Azure CDN 集成
description: 了解如何使用 Azure 内容分发网络 (CDN) 通过缓存 Azure 存储中的 blob 来分发高带宽内容。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 4086a8f354e5e906325d9c324410f3546a32f658
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "82996178"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>快速入门：将 Azure 存储帐户与 Azure CDN 集成

在此快速入门中，你将启用 [Azure 内容分发网络 (CDN)](cdn-overview.md) 来缓存 Azure 存储中的内容。 Azure CDN 为开发人员提供了一个用于交付高带宽内容的全局解决方案。 它可以在遍布美国、欧洲、亚洲、澳大利亚和南美洲的众多物理节点上缓存 blob 和计算实例的静态内容。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-storage-account"></a>创建存储帐户

通过存储帐户可访问 Azure 存储服务。 存储帐户表示用于访问每个 Azure 存储器服务组件的最高级别的命名空间：Azure Blob、队列和表存储。 有关详细信息，请参阅 [Microsoft Azure 存储简介](../storage/common/storage-introduction.md)。

要创建存储帐户，必须是相关订阅的服务管理员或联合管理员。

1. 在 Azure 门户的左上角，选择“创建资源”。 此时会显示“新建”窗格。

1. 搜索“存储帐户”，然后从下拉列表中选择“存储帐户 - blob、文件、表、队列”。 然后选择“创建”：
    
    ![选择存储资源](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

1. 在“创建存储帐户”窗格中，输入以下详细信息：

    | 设置 | 值 | 
    | --- | --- |
    | “项目详细信息”>“资源组” | 选择“新建”，并使用名称“CDNQuickstart-rg”。 如果你愿意，也可以使用现有资源组。 |
    | “实例详细信息”>“存储帐户名称” | 输入仅使用 3 到 24 个小写字母和数字的帐户名称。 该名称在 Azure 上必须独一无二，将成为用于对订阅的 blob、队列或表资源进行寻址的 URL 中的主机名。 若要对 Blob 存储中的容器资源寻址，请使用以下格式的 URI： http://&lt;storageaccountname&gt;.blob.core.windows.net/&lt;container-name&gt;。
    | “实例详细信息”>“位置” | 从下拉列表中选择附近的 Azure 区域。 |
    
    将所有其他详细信息设置为默认值，然后选择“查看 + 创建”。

1. 创建存储帐户可能需要几分钟时间才能完成。 创建操作完成后，选择“转到资源”以打开存储帐户的页面，以便进行下一步操作。

## <a name="enable-azure-cdn-for-the-storage-account"></a>为存储帐户启用 Azure CDN

1. 在存储帐户的页面上，从左侧菜单中选择“Blob 服务” > “Azure CDN”。 “Azure CDN”页面随即显示。

    ![创建 CDN 终结点](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)
    
1. 在“新建终结点”部分中，输入以下信息：

    | 设置  | 值 |
    | -------- | ----- |
    | **CDN 配置文件** | 选择“新建”并输入配置文件名称，例如“cdn-profile-123”。 配置文件是终结点的集合。 |
    | **定价层** | 选择“标准”选项之一，如“标准 Microsoft”。 |
    | **CDN 终结点名称** | 输入终结点主机名，如“cdn-endpoint-123”。 此名称必须在 Azure 中全局独一无二，因为它用于访问 URL _&lt;endpoint-name&gt;_ .azureedge.net 中的已缓存资源。 |
    | **源服务器主机名** | 默认情况下，新的 CDN 终结点使用存储帐户的主机名作为源服务器。 |

1. 选择“创建”。 创建终结点之后，它会出现在终结点列表中。

    ![存储新的 CDN 终结点](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!TIP]
> 若要为 CDN 终结点指定高级配置设置（例如[大文件下载优化](cdn-optimization-overview.md#large-file-download)），可以改用 [Azure CDN 扩展](cdn-create-new-endpoint.md)来创建 CDN 配置文件和终结点。


## <a name="enable-additional-cdn-features"></a>启用其他 CDN 功能

在存储帐户的“Azure CDN”页面中，从列表中选择 CDN 终结点以打开 CDN 终结点配置页面。

从此页面中，可以为交付内容启用其他 CDN 功能，例如[压缩](cdn-improve-performance.md)、[查询字符串缓存](cdn-query-string.md)和[地区筛选](cdn-restrict-access-by-country.md)。 
    
## <a name="enable-sas"></a>启用 SAS

如果要授予对专用存储容器的有限访问权限，可以使用 Azure 存储帐户的共享访问签名 (SAS) 功能。 SAS 是授予对 Azure 存储资源进行有限访问权限的 URI，而无需公开你的帐户密钥。 有关详细信息，请参阅[将 Azure CDN 与 SAS 一起使用](cdn-sas-storage-support.md)。

## <a name="access-cdn-content"></a>访问 CDN 内容

若要访问 CDN 上的缓存内容，请使用门户中提供的 CDN URL。 缓存 blob 的地址采用以下格式：

http://<*endpoint-name*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> 启用对存储帐户的 Azure CDN 访问之后，所有公开可用的对象即有资格获得 CDN POP 缓存。 如果修改了当前缓存在 CDN 中的对象，则除非 Azure CDN 在缓存内容的生存时间到期后刷新其内容，否则无法通过 Azure CDN 访问新内容。

## <a name="remove-content-from-azure-cdn"></a>删除 CDN 中的内容

如果不再需要在 Azure CDN 中缓存对象，则可以执行以下步骤之一：

- 将容器设为专用容器而不是公用容器。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../storage/blobs/storage-manage-access-to-resources.md)。
- 通过 Azure 门户禁用或删除 CDN 终结点。
- 将托管服务修改为不再响应此对象的请求。

已在 Azure CDN 中缓存的对象保持缓存状态，直至对象的生存时间到期，或直至[清除](cdn-purge-endpoint.md)终结点为止。 当生存时间到期时，Azure CDN 会确定 CDN 终结点是否仍有效，以及是否仍可对该对象进行匿名访问。 如果不能，则不再缓存该对象。

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，在资源组中创建了 CDN 配置文件和终结点。 若要转到[后续步骤](#next-steps)，以便了解如何向终结点添加自定义域，请保存这些资源。 但是，如果认为将来不需要这些资源，可以通过删除资源组来删除它们，避免额外付费：

1. 在 Azure 门户的左侧菜单中，选择“资源组”，然后选择“CDNQuickstart-rg”。

2. 在“资源组”页上选择“删除资源组”，在文本框中输入“CDNQuickstart-rg”，然后选择“删除”。 

    此操作会删除在本快速入门中创建的资源组、配置文件和终结点。

3. 若要删除存储帐户，请从仪表板中选择它，然后从顶部的菜单中选择“删除”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建 Azure CDN 配置文件和终结点](cdn-create-new-endpoint.md)

> [!div class="nextstepaction"]
> [教程：从 Web 应用使用 CDN 提供静态内容](cdn-add-to-web-app.md)
