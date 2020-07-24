---
title: 启用静态加密 Azure 门户托管磁盘
description: 使用 Azure 门户为托管磁盘数据启用静态加密。
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7fefaa3b13b73db119da696738af66e84f8bdd63
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136057"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure 门户-在托管磁盘上启用静态加密

Azure 磁盘存储支持将静态加密用于托管磁盘。 有关静态加密以及其他托管磁盘加密类型的概念信息，请参阅磁盘加密一文中的 "[静态加密](disk-encryption.md#double-encryption-at-rest)" 部分。

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>后续步骤

- [Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [使用服务器端加密启用客户管理的密钥-示例](disks-enable-customer-managed-keys-cli.md#examples)