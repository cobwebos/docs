---
title: 在 Azure API 管理中管理协议和加密 | Microsoft Docs
description: 了解如何在 Azure API 管理中管理协议（TLS、SSL）和加密 (DES)。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: a55d16a35b5eec1af2b24d02e158905493615999
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441106"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>在 Azure API 管理中管理协议和加密

Azure API 管理支持客户端和后端的多个 TLS 协议版本，同时也支持 3DES 加密。

此指南将演示如何管理 Azure API 管理实例的协议和加密配置。

![在 APIM 中管理协议和加密](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>先决条件

若要执行本文中的步骤，必须提供：

* API 管理实例

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>如何管理 TLS 协议和 3DES 加密

1. 在 Azure 门户中，导航到“ Azure 管理实例”。
2. 从菜单选择“SSL”。  
    ![在 APIM 中管理协议和加密 - 菜单](./media/api-management-howto-manage-protocols-ciphers/api-management-menu.png)
3. 启用或禁用所需协议或加密。
4. 单击“ **保存**”。 更改将在一个小时内生效。  
    ![在 APIM 中管理协议和加密 - 保存](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers-save.png)

## <a name="next-steps"></a>后续步骤

* 详细了解 [TLS（传输层安全性）](https://docs.microsoft.com/dotnet/framework/network-programming/tls)。
* 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。