---
title: 防止意外删除 - Azure 文件共享
description: 了解 Azure 文件共享的软删除，以及如何将其用于数据恢复和防止意外删除。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 96e3d5001d11455337ae092776a1a4c5c3738012
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83882930"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>防止意外删除 Azure 文件共享

Azure 存储为文件共享提供软删除。 使用软删除，可在应用程序或其他存储帐户用户错误地删除了数据后恢复数据。

## <a name="how-soft-delete-works"></a>软删除的工作方式

启用软删除后，可通过其保存和恢复被删除的文件共享。 被删除的数据会过渡到软删除状态，而非被永久擦除。 可配置软删除数据被永久删除前的可恢复时间。

可以在新的或现有的文件共享上启用软删除。 此外，软删除还向后兼容，因此无需对应用程序进行任何更改即可利用软删除提供的保护。 

对于软删除的高级文件共享，将在存储帐户总配额计算中纳入文件共享配额（预配的文件共享大小），直至达到软删除共享的到期日期，共享被完全删除为止。

### <a name="availability"></a>可用性

Azure 文件共享的软删除在所有存储层、所有存储帐户类型以及提供 Azure 文件存储的每个区域中均可用。

## <a name="configuration-settings"></a>配置设置

文件共享的软删除在存储帐户级别启用，软删除设置适用于存储帐户中的所有文件共享。 创建新存储帐户时，默认关闭软删除。 对于现有存储帐户，软删除也默认处于关闭状态。 可以随时打开和关闭软删除。

如果为文件共享启用软删除、删除一些文件共享，然后又禁用软删除，则在该时间段内保存的文件共享仍可供访问和恢复。 启用软删除时，还需配置保持期。

保持期指示已软删除文件共享的存储和可恢复时间期限。 对于显式删除的文件共享，保持期从删除数据时开始计时。 当前，软删除共享可保留 1-365 天。

可随时更改软删除保持期。 更新后的保持期仅适用于在更新保持期后删除的共享。 在更新保持期之前删除的共享的过期时间取决于删除该数据时配置的保持期。

要在到期日期之前永久删除处于软删除状态的文件共享，必须撤消删除共享、禁用软删除，然后再次删除该共享。 然后，应重新启用软删除，因为当软删除处于禁用状态时，该存储帐户中的任何其他文件共享可能被意外删除。

## <a name="pricing-and-billing"></a>定价和计费

软删除时，标准文件共享和高级文件共享均按使用的容量计费，而不是按预配容量计费。 此外，处于软删除状态的高级文件共享还按快照费率计费。 而处于软删除状态的标准文件共享按常规费率计费。 不会对在配置的保持期后永久删除的数据计费。

有关 Azure 文件存储常规价格的详细信息，请参阅 [Azure 文件存储定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

初次启用软删除时，建议使用较短的保持期，以便更好地了解因该功能而产生的费用变动。

## <a name="next-steps"></a>后续步骤

要了解如何启用和使用软删除，请继续查看[启用软删除](storage-files-enable-soft-delete.md)