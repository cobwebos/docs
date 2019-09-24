---
title: 通知中心安全性
description: 本主题介绍 Azure 通知中心的安全性。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 753493100bbdb34255574656a47217560e2d321a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213058"
---
# <a name="notification-hubs-security"></a>通知中心安全性

## <a name="overview"></a>概述

本主题介绍 Azure 通知中心的安全模型。

## <a name="shared-access-signature-security-sas"></a>共享访问签名 (SAS) 安全性

通知中心实现称为 SAS（共享访问签名）的实体级安全方案。 每个规则包含一个名称、一个密钥值（共享机密）和一组权限，如[安全声明](#security-claims)中所述。 在创建通知中心时，将自动创建两个规则：一个具有**侦听**权限（由客户端应用使用），一个具有**所有**权限（由应用后端使用）。

通过客户端应用执行注册管理时，如果通过通知发送的信息不敏感（例如，天气更新），则访问通知中心的常用方法是，向客户端应用提供规则仅限侦听访问权限的密钥值，并向应用后端提供规则完全访问权限的密钥值。

应用不应将密钥值嵌入 Windows Store 客户端应用中，而应让客户端应用在启动时从应用后端检索该值。

具有**侦听**访问权限的密钥允许客户端应用注册任何标记。 如果应用必须将特定标记的注册限制到特定的客户端（例如，当标记表示用户 ID 时），则应用后端必须执行注册。 有关详细信息，请参阅[注册管理](notification-hubs-push-notification-registration-management.md)。 请注意，采用这种方式时，客户端应用将无权直接访问通知中心。

## <a name="security-claims"></a>安全声明

与其他实体类似，可以针对以下三种安全声明执行通知中心操作：**侦听**、**发送**和**管理**。

| 声明   | 描述                                          | 允许的操作 |
| ------- | ---------------------------------------------------- | ------------------ |
| 侦听  | 创建/更新、读取和删除单一注册。 | 创建/更新注册<br><br>读取注册<br><br>读取句柄的所有注册<br><br>删除注册 |
| 发送    | 向通知中心发送消息                | 发送消息 |
| 管理  | 通知中心的 CRUD（包括更新 PNS 凭据和安全密钥），以及基于标记读取注册。 |创建/更新/读取/删除通知中心<br><br>按标记读取注册 |

通知中心接受使用直接在通知中心上配置的共享密钥生成的签名令牌。

不能向多个命名空间发送通知。 命名空间是通知中心的逻辑容器，不参与发送通知。
命名空间级别的访问策略（凭据）可用于命名空间级别的操作，例如：列出通知中心、创建或删除通知中心等。只有中心级别访问策略才允许你发送通知。
