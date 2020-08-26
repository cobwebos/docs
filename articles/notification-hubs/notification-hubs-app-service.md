---
title: 通知中心与应用服务移动应用集成
description: 了解如何将 Azure 通知中心与 Azure 应用服务移动应用配合使用。
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88004050"
---
# <a name="integration-with-app-service-mobile-apps"></a>与应用服务移动应用集成

为了在 Azure 服务之间促成无缝且统一的体验，[应用服务移动应用](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)原生支持使用 Azure 通知中心的通知。 [应用服务移动应用](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)提供面向企业开发人员和系统集成商的高度可缩放、全局可用的移动应用程序开发平台，该平台向移动开发人员提供一组丰富的功能。

移动应用开发人员可以借助以下工作流来使用通知中心：

1. 检索设备 PNS 句柄。
2. 使用移动应用客户端 SDK 注册 API 将设备注册到通知中心。

    > [!NOTE]
    > 请注意，出于安全方面的考虑，移动应用会在注册中去除所有标记。 直接从后端使用通知中心将标记与设备相关联。

3. 从应用后端使用通知中心发送通知。

此集成提供的一些优势包括：

- **移动应用客户端 SDK**：这些多平台 SDK 提供用于注册的 API，并与链接到移动应用的通知中心通信。 不需要通知中心凭据，也不需要使用其他服务。
  - 推送到用户：SDK 将使用移动应用的已经过身份验证的用户 ID 来自动标记指定设备，以实现“推送到用户”的方案。
  - 推送到设备：SDK 自动使用移动应用安装 ID 作为 GUID 来注册通知中心，因此无需维护多个服务 GUID。
- **安装模型**：移动应用使用通知中心最新推送模型来呈现 JSON 安装中所有与设备关联的推送属性，该模型与推送通知服务密切合作且易于使用。
- **灵活性**：即使在集成环境中，开发人员也始终可以选择直接使用通知中心。
- **[Azure 门户](https://portal.azure.com)中的集成体验**：移动应用以可视化方式呈现推送功能，开发人员可以通过移动应用轻松使用关联的通知中心。
