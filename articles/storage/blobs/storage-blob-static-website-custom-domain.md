---
title: 教程：使用 Azure CDN 通过 SSL 在静态网站上启用自定义域 - Azure 存储
description: 了解如何为静态网站托管功能配置自定义域。
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 2b0bb94be2ba8ea983cda8fd015d05fcd532f2bc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226101"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>教程：使用 Azure CDN 通过 SSL 为静态网站启用自定义域

本教程是一个系列中的第二部分。 本教程介绍如何通过 SSL 为静态网站启用自定义域终结点。 

本教程介绍如何使用 [Azure CDN](../../cdn/cdn-overview.md) 为静态网站配置自定义域终结点。 使用 Azure CDN，可以一次性预配自定义 SSL 证书、使用自定义域以及配置自定义重写规则。 配置 Azure CDN 需额外付费，但用户从世界上的任何位置访问你的网站时，其延迟会始终很低。 Azure CDN 还提供使用你自己的证书进行 SSL 加密的功能。 有关 Azure CDN 定价的详细信息，请参阅 [Azure CDN 定价](https://azure.microsoft.com/pricing/details/cdn/)。

本系列教程的第二部分将介绍如何：

> [!div class="checklist"]
> * 在静态网站终结点上创建 CDN 终结点
> * 启用自定义域和 SSL

## <a name="prerequisites"></a>先决条件

开始学习本教程之前，请完成第一部分：[教程：在 Blob 存储上托管静态网站](storage-blob-static-website-host.md)。 

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)即可开始操作。

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>在静态网站终结点上创建 CDN 终结点

1. 在 Azure 门户中找到存储帐户并显示帐户概览。
1. 在“Blob 服务”菜单下选择“Azure CDN”，以便配置 Azure CDN。  
1. 在“CDN 配置文件”部分，指定新的或现有的 CDN 配置文件。  有关详细信息，请参阅[快速入门：创建 Azure CDN 配置文件和终结点](../../cdn/cdn-create-new-endpoint.md)。
1. 指定 CDN 终结点的定价层。 本教程使用“标准 Akamai”定价层，因为它传播速度快，通常在数分钟内传播完毕。  其他定价层可能需要更长的时间来传播，但也可能有其他优势。 有关详细信息，请参阅[比较 Azure CDN 产品功能](../../cdn/cdn-features.md)。
1. 在“CDN 终结点名称”字段中，指定 CDN 终结点的名称。  CDN 终结点必须在 Azure 中独一无二。
1. 在“源主机名”字段中指定静态网站终结点。  若要查找静态网站终结点，请导航到存储帐户的“静态网站”设置。  复制主终结点并将其粘贴到 CDN 配置中，删除协议标识符（  例如 HTTPS）。

    下图显示了一个终结点配置示例：

    ![显示 CDN 终结点配置示例的屏幕截图](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. 创建 CDN 终结点并等待其传播。
1. 若要验证 CDN 终结点是否已正确配置，请单击该终结点，导航到其设置。 在存储帐户的 CDN 概览中找到终结点主机名，导航到终结点，如下图所示。 CDN 终结点的格式将类似于 `https://staticwebsitesamples.azureedge.net`。

    ![显示 CDN 终结点概览的屏幕截图](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    CDN 终结点传播完成后，导航到 CDN 终结点就会显示以前上传到静态网站的 index.html 文件的内容。

1. 若要查看 CDN 终结点的源设置，请导航到 CDN 终结点的“设置”部分下的“源”。   此时会看到“源类型”字段设置为“自定义源”，“源主机名”字段显示静态网站终结点。   

    ![显示 CDN 终结点的源设置的屏幕截图](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>启用自定义域和 SSL

1. 在域名提供商处创建一个 CNAME 记录，以便将自定义域重定向到 CND 终结点。 *www* 子域的 CNAME 记录应该如下所示：

    ![指定子域 www 的 CNAME 记录](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. 在 Azure 门户中，显示 CDN 终结点的设置。 导航到“设置”下的“自定义域”，以便配置自定义域和 SSL 证书。  
1. 选择“添加自定义域”，输入域名，然后单击“添加”。  
1. 选择新的自定义域映射，以便预配 SSL 证书。
1. 将“自定义域 HTTPS”设置为“启用”，然后单击“保存”。    配置自定义域可能需要数小时。 门户会显示进度，如下图所示。

    ![显示自定义域配置进度的屏幕截图](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. 测试从静态网站到自定义域的映射，方法是访问自定义域的 URL。

若要详细了解如何为自定义域启用 HTTPS，请参阅[教程：在 Azure CDN 自定义域中配置 HTTPS](../../cdn/cdn-custom-ssl.md)。

## <a name="next-steps"></a>后续步骤

本教程的第二部分介绍了如何通过 SSL 在 Azure CDN 中为静态网站配置自定义域。

若要详细了解如何配置和使用 Azure CDN，请参阅[什么是 Azure CDN？](../../cdn/cdn-overview.md)。