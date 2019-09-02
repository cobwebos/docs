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
ms.openlocfilehash: 364da4bb5f3f76f0a8a189db5aa59cca305470ea
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70197103"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>从 Azure 门户复制凭据

此示例应用程序需对存储帐户访问进行身份验证。 若要进行身份验证，请将存储帐户凭据以连接字符串形式添加到应用程序中。 按照以下步骤查看存储帐户凭据：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 找到自己的存储帐户。
3. 在存储帐户概述的“设置”部分，选择“访问密钥”。   在这里，可以查看你的帐户访问密钥以及每个密钥的完整连接字符串。   
4. 找到“密钥 1”下面的“连接字符串”值，选择“复制”按钮复制该连接字符串。    下一步需将此连接字符串值添加到某个环境变量。

    ![显示如何从 Azure 门户复制连接字符串的屏幕截图](media/storage-copy-connection-string-portal/portal-connection-string.png)
