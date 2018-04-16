---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 51bdb7e3a84cbd176ac1a26747a627a6c2c4fa5b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>从 Azure 门户复制凭据

此示例应用程序需对存储帐户访问进行身份验证。 请为应用程序提供连接字符串形式的存储帐户凭据，以便进行身份验证。 若要查看存储帐户凭据，请执行以下操作：

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 找到自己的存储帐户。
3. 在存储帐户概述的“设置”部分，选择“访问密钥”。 此时会显示帐户访问密钥，以及每个密钥的完整连接字符串。
4. 请记下存储帐户的名称，在以后进行身份验证时，需提供该名称。   
5. 找到“key1”下面的“密钥”值，单击“复制”按钮复制该帐户密钥。

    ![显示如何从 Azure 门户复制帐户密钥的屏幕截图](media/storage-copy-account-key-portal/portal-account-key.png)
