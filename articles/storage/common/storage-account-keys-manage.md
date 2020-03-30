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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75975786"
---
# <a name="manage-storage-account-access-keys"></a>管理存储帐户访问密钥

创建存储帐户时，Azure 会生成两个 512 位的存储帐户访问密钥。 这些密钥可用于通过共享密钥授权来授予对你存储帐户中数据的访问权限。

Microsoft 建议使用 Azure 密钥保管库来管理访问密钥，并定期轮换和重新生成密钥。 使用 Azure 密钥保管库可以轻松轮换密钥，而无需中断应用程序。 还可以手动轮换密钥。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>查看访问密钥和连接字符串

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>使用 Azure 密钥保管库管理访问密钥

Microsoft 建议使用 Azure 密钥保管库来管理和旋转访问密钥。 应用程序可以安全地访问密钥保管库中的密钥，这样就可以避免使用应用程序代码来存储密钥。 有关使用密钥保管库进行密钥管理的详细信息，请参阅以下文章：

- [使用 Azure 密钥保管库和 PowerShell 管理存储帐户密钥](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [使用 Azure 密钥保管库和 Azure CLI 管理存储帐户密钥](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>手动轮换访问密钥

Microsoft 建议您定期轮换访问密钥，以帮助保护存储帐户的安全。 如果可能，请使用 Azure 密钥保管库管理访问密钥。 如果不使用密钥保管库，将需要手动轮换密钥。

分配了两个访问密钥，以便轮换使用。 拥有两个密钥可确保应用程序在整个轮换过程中能够持续访问 Azure 存储。

> [!WARNING]
> 重新生成访问密钥会影响依赖于存储帐户密钥的所有应用程序或 Azure 服务。 必须将使用帐户密钥访问存储帐户的所有客户端更新为使用新密钥，包括媒体服务、云、桌面和移动应用程序以及用于 Azure 存储的图形用户界面应用程序（如 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）。

按照此流程轮换使用存储帐户密钥：

1. 更新应用程序代码中的连接字符串以使用辅助密钥。
2. 为存储帐户重新生成主访问密钥。 在 Azure 门户中的“访问密钥”边栏选项卡上，单击“重新生成密钥 1”，并单击“是”以确认要生成新密钥************。
3. 更新代码中的连接字符串以引用新的主访问密钥。
4. 以相同方式重新生成辅助访问密钥。

> [!NOTE]
> Microsoft 建议同一时间在所有应用程序中只使用一个密钥。 如果在某些地方使用密钥 1 并在其他地方使用密钥 2，将无法在没有部分应用程序失去访问的情况下轮转密钥。

## <a name="next-steps"></a>后续步骤

- [Azure 存储帐户概述](storage-account-overview.md)
- [创建存储帐户](storage-account-create.md)
