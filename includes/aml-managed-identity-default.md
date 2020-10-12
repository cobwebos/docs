---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147370"
---
默认托管标识是系统分配的托管标识或第一个用户分配的托管标识。

在运行期间，一个标识有两种应用：

1. 系统使用标识来设置用户的存储装载、容器注册表和数据存储。

    * 在这种情况下，系统将使用默认托管标识。

1. 用户应用标识以便从已提交运行的代码中访问资源

    * 在这种情况下，请提供与要用于检索凭据的托管标识对应的 client_id。
    * 或者，通过 DEFAULT_IDENTITY_CLIENT_ID 环境变量获取用户分配的标识的客户端 ID。

    例如，若要使用默认托管标识检索数据存储的令牌，请执行以下操作：

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```