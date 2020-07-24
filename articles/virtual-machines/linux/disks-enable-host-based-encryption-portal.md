---
title: 使用 Azure 门户托管磁盘上的加密来启用端到端加密
description: 使用主机上的加密在 Azure 托管磁盘上启用端到端加密。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 96506e0ca09e9491135faa69d0b690b91ccc8177
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136094"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>使用主机上的加密启用端到端加密-Azure 门户

当你在主机上启用加密时，存储在 VM 主机上的数据将静态加密，并加密为存储服务。 有关主机加密以及其他托管磁盘加密类型的概念信息，请参阅[VM 数据的主机端对端加密](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>后续步骤

[Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)