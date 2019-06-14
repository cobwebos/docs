---
title: include 文件
description: include 文件
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 7e4760990229433b2ea40fadd0d17de0b52fcb36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66162161"
---
下表介绍了 Azure 数字孪生中可用的角色：

| **角色** | **说明** | **标识符** |
| --- | --- | --- |
| 空间管理员 | 指定空间和其下所有节点的 CREATE、READ、UPDATE 和 DELETE 权限     。 全局权限。 | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| 用户管理员| 用户及用户相关对象的 CREATE、READ、UPDATE 和 DELETE 权限     。 空间的 READ 权限  。 | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| 设备管理员 | 设备及设备相关对象的 CREATE、READ、UPDATE 和 DELETE 权限     。 空间的 READ 权限  。 | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| 密钥管理员 | 访问密钥的 CREATE、READ、UPDATE 和 DELETE 权限     。 空间的 READ 权限  。 | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| 令牌管理员 |  访问密钥的 READ 和 UPDATE 权限   。 空间的 READ 权限  。 | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| 用户 |  空间、传感器和用户（包括其相应的相关对象）的 READ 权限  。 | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| 支持专家 |  除访问密钥之外的所有内容的 READ 权限  。 | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| 设备安装员 | 设备和传感器（包括其相应的相关对象）的 READ 和 UPDATE 权限   。 空间的 READ 权限  。 | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| 网关设备 | 传感器的 CREATE 权限  。 设备和传感器（包括其相应的相关对象）的 READ 权限  。 | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |