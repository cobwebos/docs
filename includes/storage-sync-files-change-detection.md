---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 59adee2f1d6a99a0a984b9b63c7201266b6381d4
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69984522"
---
与对服务器终结点所做的更改不同，使用 Azure 门户或 SMB 对 Azure 文件共享所做的更改不会立即检测到并复制。 Azure 文件尚没有更改通知或日记，因此无法在文件更改时自动启动同步会话。 在 Windows Server 上，Azure 文件同步使用 [Windows USN 日记](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx)可在文件更改时自动启动同步会话。

为了检测对 Azure 文件共享所做的更改，Azure 文件同步有一个称为“更改检测作业”的计划作业。 更改检测作业枚举文件共享中的每个文件，然后将它与该文件的同步版本进行比较。 当更改检测作业确定文件已更改时，Azure 文件同步会启动同步会话。 更改检测作业每 24 小时启动一次。 由于更改检测作业的工作原理是枚举 Azure 文件共享中的每个文件，因此大命名空间用时会长于较小的命名空间。 对于大命名空间，用时可能超过每 24 小时一次，才能确定哪些文件已更改。

若要立即同步 Azure 文件共享中已更改的文件, 可使用**AzStorageSyncChangeDetection** PowerShell cmdlet 手动启动对 azure 文件共享中的更改的检测。 此 cmdlet 适用于某些类型的自动化过程在 Azure 文件共享中进行更改或由管理员完成更改的情况 (如将文件和目录移动到共享中)。 对于最终用户的更改, 建议在 IaaS VM 中安装 Azure 文件同步代理, 并让最终用户通过 IaaS VM 访问文件共享。 这样一来, 所有更改都将快速同步到其他代理, 而无需使用 AzStorageSyncChangeDetection cmdlet。 若要了解详细信息, 请参阅[AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)文档。

>[!NOTE]
>使用 REST 对 Azure 文件共享所做的更改不会更新 SMB 上次修改时间, 也不会被视为同步更改。

我们正在探讨针对 Azure 文件共享添加类似于针对 Windows Server 上的卷使用的 USN 的更改检测功能。 请在 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 上为此功能投票，帮助我们确定将来开发此功能的优先级。
