---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629474"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果数据存储是以下方式之一配置的，则需要设置[自承载集成运行时](../articles/data-factory/create-self-hosted-integration-runtime.md)才能连接到此数据存储：

- 数据存储位于本地网络内部、Azure 虚拟网络内部或 Amazon 虚拟私有云内。
- 数据存储是一种托管的云数据服务，只有在防火墙规则中列入允许列表的 IP 才能访问该服务。
