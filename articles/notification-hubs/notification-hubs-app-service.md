---
title: 与应用服务移动应用集成
description: 了解如何将 Azure 通知中心与 Azure 应用服务移动应用配合使用。
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 8c3bc90b282092ede0e924d32b50b67e5c4e22b8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244507"
---
# <a name="integration-with-app-service-mobile-apps"></a>与应用服务移动应用集成

为了在 Azure 服务之间促成完美且统一的体验， [应用服务移动应用](../app-service-mobile/app-service-mobile-value-prop.md) 原生支持使用通知中心推送通知。 [应用服务 Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) 提供面向企业开发人员和系统集成商的高度可缩放、全局可用的移动应用程序平台，该平台向移动开发人员提供一组丰富的功能。

Visual Studio App Center 为当今的开发人员提供了更新式的移动解决方案。 它支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用**生成**、**测试**和**分发**服务来设置持续集成和交付管道。 部署应用后，开发人员可以使用“分析”  和“诊断”  服务监视其应用的状态和使用情况，并使用“推送”  服务吸引用户。 开发人员还可以利用“身份验证”  对其用户进行身份验证，并使用“数据”  服务在云中保留和同步应用数据。

> [!NOTE]
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/signup?utm_source=NotificationHubs&utm_medium=Azure&utm_campaign=docs) 中。

移动应用开发人员可以借助以下工作流来利用通知中心：

1. 检索设备 PNS 句柄
2. 通过便利的移动应用客户端 SDK 注册 API 将设备注册到通知中心

    > [!NOTE]
    > 请注意，出于安全方面的考虑，移动应用会在注册中去除所有标记。 直接从后端使用通知中心将标记与设备相关联。

3. 从应用后端使用通知中心发送通知

以下是这种集成为开发人员带来的便利：

- **移动应用客户端 SDK**： 这些多平台 SDK 提供简单的 API 用于注册，并自动与链接到移动应用的通知中心联系。 开发人员不需要深入了解通知中心凭据和使用其他服务。
  -  推送到用户：SDK 将使用移动应用的已经过身份验证的用户 ID 来自动标记给定设备，以实现推送到用户的方案。
  -  推送到设备：SDK 自动使用移动应用安装 ID 作为 GUID 来注册到通知中心，省去了开发人员维护多个服务 GUID 的麻烦。
- **安装模型**：移动应用使用通知中心的最新推送模型来呈现 JSON 安装中所有与设备关联的推送属性，该模型与推送通知密切合作且易于使用。
- **灵活性**： 即使是就地集成的，开发人员也始终可以选择直接使用通知中心。
- **[Azure 门户](https://portal.azure.com)中的集成体验**：移动应用以可视化方式呈现推送功能，开发人员可以通过移动应用轻松使用关联的通知中心。
