---
title: include 文件
description: include 文件
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132387"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Functions 1.x 中的 Azure 存储 SDK 版本

在 Functions 1.x 中，存储触发器和绑定使用 Azure 存储 SDK 版本 7.2.1（[WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet 包）。 如果引用不同版本的存储 SDK 并绑定到函数签名中的一种存储 SDK 类型，Functions 运行时可能报告无法绑定到该类型。 解决办法是确保项目引用 [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)。
