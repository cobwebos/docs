---
title: Azure 备份中的加密
description: 了解 Azure 备份中的加密功能如何帮助你保护备份数据并满足企业的安全需求。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 0a3f4db4d248d2534cfebd617be0f3ccc9647f15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807730"
---
# <a name="encryption-in-azure-backup"></a>Azure 备份中的加密

将所有备份数据存储在云中时，会使用 Azure 存储加密自动对其进行加密，这有助于履行安全性与合规性承诺。 此静态数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密，并符合 FIPS 140-2 规范。

除了静态加密以外，所有传输中的备份数据将通过 HTTPS 传输。 这些数据始终保留在 Azure 主干网络上。

有关详细信息，请参阅[静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。 请参阅 [Azure 备份常见问题解答](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption)，其中解答了有关加密的任何问题。

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平台托管的密钥加密备份数据

默认情况下，所有数据将使用平台托管的密钥进行加密。 你不需要执行任何显式操作即可启用这种加密，加密将应用于要备份到恢复服务保管库的所有工作负荷。

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客户托管密钥加密备份数据

现在，备份 Azure 虚拟机时，你可以使用自己拥有和管理的密钥来加密数据。 Azure 备份允许你使用存储在 Azure 密钥保管库中的 RSA 密钥对备份进行加密。 用于加密备份的加密密钥可能与用于源的加密密钥不同。 数据受到基于 AES 256 的数据加密密钥 (DEK) 的保护，而 DEK 又受到你的密钥的保护。 这使你可以完全控制数据和密钥。 若要允许加密，需要向恢复服务保管库授予对 Azure 密钥保管库中加密密钥的访问权限。 可以根据需要禁用密钥或撤销访问权限。 但是，在你尝试保护保管库中的任何项目之前，必须先使用你的密钥启用加密。

>[!NOTE]
>此功能目前处于可用性受限的状态。 如果你想要使用客户托管密钥加密备份数据，请填写[此调查表](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u)并发送电子邮件至 AskAzureBackupTeam@microsoft.com。 请注意，能否使用此功能取决于 Azure 备份服务的审批结果。

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>备份使用客户托管密钥加密的托管磁盘 VM

Azure 备份还允许备份使用密钥进行[存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)的 azure vm。 用于加密磁盘的密钥存储在 Azure 密钥保管库中，并由你进行管理。 使用客户托管密钥的存储服务加密（SSE）不同于 Azure 磁盘加密，因为 ADE 利用 BitLocker （适用于 Windows）和 DM Dm-crypt （适用于 Linux）来执行来宾内加密，所以 SSE 会对存储服务中的数据进行加密，使你能够为 Vm 使用任何 OS 或映像。 有关更多详细信息，请参阅[使用客户托管密钥加密托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)。

## <a name="backup-of-vms-encrypted-using-ade"></a>备份使用 ADE 加密的 VM

使用 Azure 备份，还可以备份已通过 Azure 磁盘加密功能将其 OS 磁盘或数据磁盘加密的 Azure 虚拟机。 ADE 使用适用于 Windows VM 的 BitLocker 以及适用于 Linux VM 的 DM-Crypt 来执行来宾内部加密。 有关详细信息，请参阅[使用 Azure 备份来备份和还原已加密的虚拟机](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="next-steps"></a>后续步骤

- [备份和还原已加密的 Azure VM](backup-azure-vms-encryption.md)
