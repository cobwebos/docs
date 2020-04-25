---
title: 部署存储同步服务
description: 部署 Azure 文件同步云资源，即存储同步服务。 跨迁移文档共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143628"
---
在此步骤中，需要你的 Azure 订阅凭据。

要为 Azure 文件同步配置的核心资源称为*存储同步服务*。 建议只为当前或将来同步同一组文件的所有服务器部署一个服务器。 仅在具有不同的服务器集（例如：同步同一个 Azure 文件共享）时，才创建多个存储同步服务。 否则，单个存储同步服务是最佳实践。

选择与你所在位置接近的存储同步服务的 Azure 区域。 所有其他云资源必须部署在同一区域中。
若要简化管理，请在订阅中创建一个包含同步和存储资源的新资源组。

有关详细信息，请参阅关于部署 Azure 文件同步一文中[有关部署存储同步服务的部分](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)。请仅关注本文的这一部分。 稍后的步骤中将提供文章的其他部分的链接。