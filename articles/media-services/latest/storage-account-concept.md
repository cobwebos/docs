---
title: 使用 Azure 媒体服务的云上传和存储 | Microsoft Docs
description: 本文介绍云上传和存储的概念。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/11/2019
ms.author: juliako
ms.openlocfilehash: 9cbb995eb3310a2263185d6fd6dba20efce37f38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550152"
---
# <a name="cloud-upload-and-storage"></a>云上传和存储

若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 创建媒体服务帐户时，需要提供 Azure 存储帐户资源的名称。 指定存储帐户会附加到媒体服务帐户。 

媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。 强烈建议在媒体服务帐户所在的位置使用存储帐户，避免额外的延迟和数据出口成本

必须具有一个主存储帐户，并且可以拥有任意数量的与媒体服务帐户关联的辅助存储帐户   。 媒体服务支持常规用途 v2 (GPv2) 或常规用途 v1 (GPv1) 帐户   。 

>[!NOTE]
> 不允许将仅限 Blob 的帐户作为主帐户  。 

建议使用 GPv2，以便在热存储层和冷存储层之间进行选择。 若要了解存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../../storage/common/storage-account-overview.md)。 

有不同的 Sku，可以选择存储帐户。 有关详细信息，请参阅[存储帐户](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)。 若要通过存储帐户进行试验，请使用 `--sku Standard_LRS`。 但是，在选取用于生产的 SKU 时，应考虑 `--sku Standard_RAGRS`，以便通过异地复制确保业务连续性。 

## <a name="assets-in-a-storage-account"></a>存储帐户中的资产

在媒体服务 v3 存储 Api 用于将文件上传到资产。 有关详细信息，请参阅[资产概念](assets-concept.md)。

> [!Note]
> 在不使用媒体服务 API 的情况下，不应该试更改媒体服务 SDK 生成的 BLOB 容器内容。
 
## <a name="storage-side-encryption"></a>存储端加密

若要保护静态资产，应通过存储端加密对资产进行加密。 下表显示了存储端加密在媒体服务 v3 中的工作方式：

|加密选项|描述|媒体服务 v3|
|---|---|---|
|媒体服务存储加密| AES-256 加密，媒体服务管理的密钥|不支持<sup>(1)</sup>|
|[静态数据的存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|由 Azure 存储提供的服务器端加密，由 Azure 或客户管理的密钥|支持|
|[存储客户端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|由 Azure 存储提供的客户端加密，由 Key Vault 中的客户管理的密钥|不支持|

<sup>1</sup> 在媒体服务 v3 中，仅当资产是使用媒体服务 v2 创建的时才支持存储加密（AES-256 加密）以实现向后兼容性。 这意味着 v3 会处理现有的存储加密资产，但不会允许创建新资产。

## <a name="storage-account-errors"></a>存储帐户错误

如果某个媒体服务帐户处于“已断开连接”状态，则表明该帐户不再能够访问一个或多个附加的存储帐户，因为存储访问密钥已更改。 媒体服务需要最新的存储访问密钥才能执行帐户中的许多任务。

下面这些主要场景会导致媒体服务帐户无法访问附加的存储帐户。 

|问题|解决方案|
|---|---|
|媒体服务帐户或附加的存储帐户已迁移到单独的订阅。 |迁移存储帐户或媒体服务帐户，使之全都位于同一订阅中。 |
|媒体服务帐户在使用另一订阅中的附加存储帐户，因为它是支持此功能的早期媒体服务帐户。 所有早期的媒体服务帐户都已转换成新式的基于 Azure 资源管理器 (ARM) 的帐户，其状态将为“已断开连接”。 |迁移存储帐户或媒体服务帐户，使之全都位于同一订阅中。|

## <a name="next-steps"></a>后续步骤

若要了解如何将存储帐户附加到媒体服务帐户，请参阅[创建帐户](create-account-cli-quickstart.md)。
