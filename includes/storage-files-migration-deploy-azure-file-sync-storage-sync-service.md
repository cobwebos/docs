---
title: 部署存储同步服务
description: 部署 Azure 文件同步云资源。 存储同步服务。 在迁移文档之间共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209449"
---
在此步骤中，需要你的 Azure 订阅凭据。 你使用的 Azure 订阅可以与用于 StorSimple 的 Azure 订阅不同。

要配置 Azure 文件同步核心资源称为 "存储同步服务"。
建议你只为公司中的所有服务器部署一个，以立即同步相同的一组文件。 如果有多个 StorSimple 设备，可以考虑为每个设备创建一个存储同步服务资源。 但是，如果你有不同的服务器集，而这些服务器必须不能交换数据，则应仅创建多个存储同步服务。 否则，单个存储同步服务是最佳实践。

选择与你的办公地点接近的存储同步服务的 Azure 区域。 所有其他云资源必须部署在同一区域中。
最佳做法是在订阅中创建新的资源组，以容纳同步和存储资源，以便更轻松地进行管理。

以下文章介绍了如何部署存储同步服务。 仅遵循文档的这一部分。稍后的步骤中将提供此文档的其他子节的链接。

[了解如何部署存储同步服务。](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
