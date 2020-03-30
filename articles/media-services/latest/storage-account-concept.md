---
title: Azure 存储帐户
titleSuffix: Azure Media Services
description: 了解如何创建要与 Azure 媒体服务一起使用的 Azure 存储帐户。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499835"
---
# <a name="azure-storage-accounts"></a>Azure 存储帐户

若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 创建媒体服务帐户时，需要提供 Azure 存储帐户资源的名称。 指定存储帐户会附加到媒体服务帐户。

媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。 强烈建议使用与媒体服务帐户位于同一位置的存储帐户，以避免额外的延迟和数据出口成本。

必须具有一个主存储帐户，并且可以拥有任意数量的与媒体服务帐户关联的辅助存储帐户********。 媒体服务支持常规用途 v2 (GPv2) 或常规用途 v1 (GPv1) 帐户********。 仅 Blob 帐户不允许作为**主**帐户。

我们建议你使用 GPv2，以便可以利用最新的功能和性能。 要了解有关存储帐户的详细信息，请参阅[Azure 存储帐户概述](../../storage/common/storage-account-overview.md)。

> [!NOTE]
> 仅支持热访问层与 Azure 媒体服务一起使用，尽管其他访问层可用于降低未主动使用的内容的存储成本。

可以为存储帐户选择不同的 SKU。 有关详细信息，请参阅[存储帐户](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)。 若要通过存储帐户进行试验，请使用 `--sku Standard_LRS`。 但是，在选择用于生产的 SKU 时，应考虑`--sku Standard_RAGRS`，它为业务连续性提供地理复制。

## <a name="assets-in-a-storage-account"></a>存储帐户中的资产

在媒体服务 v3 中，存储 API 用于将文件上传到资产中。 有关详细信息，请参阅[Azure 媒体服务 v3 中的资产](assets-concept.md)。

> [!Note]
> 不要尝试更改媒体服务 SDK 生成的 Blob 容器的内容，而无需使用媒体服务 API。

## <a name="storage-side-encryption"></a>存储端加密

为了保护您的静态资产，资产应通过存储端加密进行加密。 下表显示了存储端加密在媒体服务 v3 中的工作方式：

|加密选项|描述|媒体服务 v3|
|---|---|---|
|媒体服务存储加密| AES-256 加密，媒体服务管理的密钥。 |不支持。<sup>（1）</sup>|
|[静态数据的存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 存储提供的服务器端加密，由 Azure 或客户管理密钥。|支持。|
|[存储客户端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 存储提供的客户端加密，密钥由密钥保管库中的客户管理。|不支持。|

<sup>1</sup>在媒体服务 v3 中，仅当使用 Media Services v2 创建资产时，存储加密 （AES-256 加密） 仅支持向后兼容性，这意味着 v3 与现有存储加密资产一起工作，但不允许创建新的存储加密资产。

## <a name="storage-account-errors"></a>存储帐户错误

如果某个媒体服务帐户处于“已断开连接”状态，则表明该帐户不再能够访问一个或多个附加的存储帐户，因为存储访问密钥已更改。 媒体服务需要最新的存储访问密钥才能执行帐户中的许多任务。

下面这些主要场景会导致媒体服务帐户无法访问附加的存储帐户。

|问题|解决方案|
|---|---|
|媒体服务帐户或附加的存储帐户已迁移到单独的订阅。 |迁移存储帐户或媒体服务帐户，以便它们都在同一订阅中。 |
|媒体服务帐户在使用另一订阅中的附加存储帐户，因为它是支持此功能的早期媒体服务帐户。 所有早期媒体服务帐户都转换为基于 Azure 资源管理器的现代帐户，并将具有断开连接状态。 |迁移存储帐户或媒体服务帐户，以便它们都在同一订阅中。|

## <a name="azure-storage-firewall"></a>Azure 存储防火墙

Azure 媒体服务不支持启用 Azure 存储防火墙或[专用终结点](https://docs.microsoft.com/azure/storage/common/storage-network-security)的存储帐户。

## <a name="next-steps"></a>后续步骤

若要了解如何将存储帐户附加到媒体服务帐户，请参阅[创建帐户](create-account-cli-quickstart.md)。
