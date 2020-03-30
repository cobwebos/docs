---
title: 部署存储同步服务
description: 部署 Azure 文件同步云资源。 存储同步服务。 在迁移文档之间共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124721"
---
在此步骤中，您需要 Azure 订阅凭据。

配置 Azure 文件同步的核心资源称为"存储同步服务"。
我们建议您只为公司中的所有服务器部署一个，这些服务器现在或将来同步同一组文件。 仅当具有必须交换数据的不同服务器集时，才创建多个存储同步服务。 （例如：同步相同的 Azure 文件共享）。 否则，最佳做法是单个存储同步服务。

为靠近办公室位置的存储同步服务选择 Azure 区域。 所有其他云资源必须部署在同一区域。
为了简化管理，请在订阅中创建一个包含同步和存储资源的新资源组。

以下文章介绍如何部署存储同步服务。 只遵循文档的这一部分。在后面的步骤中，将有指向此文档其他子部分的链接。

[了解如何部署存储同步服务。](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
