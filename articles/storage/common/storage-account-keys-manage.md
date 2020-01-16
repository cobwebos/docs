---
title: 管理帐户访问密钥
titleSuffix: Azure Storage
description: 了解如何查看、管理和轮换存储帐户访问密钥。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 13adf6de420b54299d04a226dab81e75cbb9fef2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975786"
---
# <a name="manage-storage-account-access-keys"></a>管理存储帐户访问密钥

创建存储帐户时，Azure 会生成两个 512 位的存储帐户访问密钥。 这些密钥可用于通过共享密钥授权授予对存储帐户中数据的访问权限。

Microsoft 建议使用 Azure Key Vault 来管理访问密钥，并且定期轮换和重新生成密钥。 使用 Azure Key Vault 可以轻松地在无需中断应用程序的情况下旋转密钥。 你还可以手动轮换密钥。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>查看访问密钥和连接字符串

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>使用 Azure Key Vault 管理访问密钥

Microsoft 建议使用 Azure Key Vault 来管理和旋转访问密钥。 你的应用程序可以安全地访问 Key Vault 中的密钥，这样你就可以避免将它们存储在应用程序代码中。 有关使用 Key Vault 进行密钥管理的详细信息，请参阅以下文章：

- [通过 Azure Key Vault 和 PowerShell 管理存储帐户密钥](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [用 Azure Key Vault 和 Azure CLI 管理存储帐户密钥](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>手动轮换访问密钥

Microsoft 建议定期轮换访问密钥，以确保存储帐户安全。 如果可能，请使用 Azure Key Vault 来管理访问密钥。 如果不使用 Key Vault，将需要手动轮换密钥。

分配了两个访问密钥，以便轮换使用。 使用两个密钥可确保应用程序能够在整个过程中保持对 Azure 存储的访问权限。

> [!WARNING]
> 重新生成访问密钥会影响依赖于存储帐户密钥的所有应用程序或 Azure 服务。 必须将使用帐户密钥访问存储帐户的所有客户端更新为使用新密钥，包括媒体服务、云、桌面和移动应用程序以及用于 Azure 存储的图形用户界面应用程序（如 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）。

按照此流程轮换使用存储帐户密钥：

1. 更新应用程序代码中的连接字符串以使用辅助密钥。
2. 为存储帐户重新生成主访问密钥。 在 Azure 门户中的“访问密钥”边栏选项卡上，单击“重新生成密钥 1”，并单击“是”以确认要生成新密钥。
3. 更新代码中的连接字符串以引用新的主访问密钥。
4. 以相同方式重新生成辅助访问密钥。

> [!NOTE]
> Microsoft 建议同一时间在所有应用程序中只使用一个密钥。 如果在某些地方使用密钥 1 并在其他地方使用密钥 2，将无法在没有部分应用程序失去访问的情况下轮转密钥。

## <a name="next-steps"></a>后续步骤

- [Azure 存储帐户概述](storage-account-overview.md)
- [创建存储帐户](storage-account-create.md)
