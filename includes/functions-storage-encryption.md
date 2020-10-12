---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648777"
---
Azure 存储可对存储帐户中的所有数据进行静态加密。 有关详细信息，请参阅[静态数据的 Azure 存储加密](../articles/storage/common/storage-service-encryption.md)。

默认情况下，数据使用 Microsoft 管理的密钥进行加密。 为了进一步控制加密密钥，可以提供客户管理的密钥，用于对 blob 和文件数据进行加密。 这些密钥必须存在于 Azure Key Vault 中，以便 Functions 能够访问存储帐户。 若要了解详细信息，请参阅[使用客户管理的密钥进行静态加密](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md)。  