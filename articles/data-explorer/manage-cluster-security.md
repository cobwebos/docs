---
title: 在 Azure 数据资源管理器中保护群集
description: 本文介绍如何在 Azure 门户中通过 Azure 数据资源管理器保护群集。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720338"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>在 Azure 数据资源管理器中保护群集 - Azure 门户

[Azure 磁盘加密](/azure/security/azure-security-disk-encryption-overview)有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 它为群集虚拟机的 OS 和数据磁盘提供卷加密。 它还与 Azure[密钥保管库](/azure/key-vault/)集成，这使我们能够控制和管理磁盘加密密钥和机密，并确保 VM 磁盘上的所有数据都加密。 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>在 Azure 门户中启用静态加密
  
通过群集安全设置可以在群集上启用磁盘加密。 在群集上启用[静态加密](/azure/security/fundamentals/encryption-atrest)为存储的数据（静态）提供数据保护。 

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集资源。 在“设置”标题下，选择“安全性”********。 

    ![启用静态加密](media/manage-cluster-security/security-encryption-at-rest.png)

1. 在“安全性”**** 窗口中，为“磁盘加密”**** 安全设置选择“打开”****。 

1. 选择“保存”。****
 
> [!NOTE]
> 启用加密后，选择“关闭”**** 可禁用加密。

## <a name="next-steps"></a>后续步骤

[检查群集运行状况](/azure/data-explorer/check-cluster-health)
