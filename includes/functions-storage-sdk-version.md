---
title: include 文件
description: include 文件
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77198406"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Functions 1.x 中的 Azure 存储 SDK 版本

在 Functions 1.x 中，存储触发器和绑定使用 Azure 存储 SDK 版本 7.2.1（[WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet 包）。 如果引用不同版本的存储 SDK 并绑定到函数签名中的一种存储 SDK 类型，Functions 运行时可能报告无法绑定到该类型。 解决办法是确保项目引用 [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)。
