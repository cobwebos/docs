---
title: Azure 备份中的加密
description: 了解 Azure 备份中的加密功能如何帮助你保护你的备份数据并满足你的业务的安全需求。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 1f7e0f26df0f8bd70a10b36f658dcfebe897b475
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82765794"
---
# <a name="encryption-in-azure-backup"></a>Azure 备份中的加密

使用 Azure 存储加密存储在云中时，会自动对所有已备份的数据进行加密，这有助于你满足安全性和符合性承诺。 使用256位 AES 加密对静态数据进行加密，这是一个最强的可用块密码，并且符合 FIPS 140-2。

除静态加密外，传输中的所有备份数据都通过 HTTPS 传输。 它始终保留在 Azure 主干网络中。

有关详细信息，请参阅[静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。 请参阅[Azure 备份常见问题解答](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption)，回答有关加密的任何问题。

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平台托管密钥加密备份数据

默认情况下，使用平台托管密钥对所有数据进行加密。 无需执行任何显式操作即可启用此加密，并将其应用于要备份到恢复服务保管库的所有工作负荷。

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客户管理的密钥加密备份数据

备份 Azure 虚拟机时，你现在可以使用自己拥有和管理的密钥来加密数据。 Azure 备份允许你使用存储在 Azure Key Vault 中的 RSA 密钥对备份进行加密。 用于加密备份的加密密钥可能不同于用于源的加密密钥。 使用基于 AES 256 的数据加密密钥（DEK）保护数据，进而使用密钥进行保护。 这使你可以完全控制数据和键。 若要允许加密，需要将恢复服务保管库授予对 Azure Key Vault 中的加密密钥的访问权限。 你可以根据需要禁用密钥或撤销访问权限。 但是，在尝试保护保管库中的任何项之前，必须使用密钥来启用加密。

>[!NOTE]
>此功能目前的可用性有限。 如果你想要使用客户托管密钥加密备份数据，请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u)并向我们发送电子邮件。 AskAzureBackupTeam@microsoft.com 请注意，若要使用此功能，可以从 Azure 备份服务进行审批。

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>使用客户管理的密钥备份已加密的托管磁盘 Vm

Azure 备份还允许备份使用密钥进行服务器端加密的 Azure Vm。 用于对磁盘进行加密的密钥存储在 Azure Key Vault 中，并由你进行管理。 使用客户托管密钥的服务器端加密不同于 Azure 磁盘加密，因为 ADE 利用 BitLocker （适用于 Windows）和 DM Dm-crypt （适用于 Linux）来执行来宾内加密，因此，SSE 对存储服务中的数据进行加密，使你能够为 Vm 使用任何 OS 或映像。 有关更多详细信息，请参阅[通过客户托管的密钥加密托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)。

## <a name="backup-of-vms-encrypted-using-ade"></a>备份使用 ADE 加密的 Vm

使用 Azure 备份，还可以备份使用 Azure 磁盘加密来加密操作系统或数据磁盘的 Azure 虚拟机。 ADE 使用适用于 Windows Vm 的 BitLocker 和 Dm-crypt for Linux Vm 的 DM 来执行来宾内加密。 有关详细信息，请参阅[备份和还原已加密的虚拟机和 Azure 备份](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="next-steps"></a>后续步骤

- [备份和还原已加密的 Azure VM](backup-azure-vms-encryption.md)
