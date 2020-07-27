---
title: 使用 Azure 门户托管磁盘上的加密来启用端到端加密
description: 使用主机上的加密在 Azure 托管磁盘上启用端到端加密-Azure 门户。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e969fe34e7e7723218586f24807ad70944ba5716
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171514"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>使用主机上的加密启用端到端加密-Azure 门户

当你在主机上启用加密时，存储在 VM 主机上的数据将静态加密，并加密为存储服务。 有关主机加密以及其他托管磁盘加密类型的概念信息，请参阅[VM 数据的主机端对端加密](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>后续步骤

[Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)