---
title: 通知中心安全性
description: 本主题介绍 Azure 通知中心的安全性。
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 3f5b23028094b545262e9c01640890f2c0b989ca
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431251"
---
# <a name="notification-hubs-security"></a>通知中心安全性

## <a name="overview"></a>概述

本主题介绍 Azure 通知中心的安全模型。

## <a name="shared-access-signature-security-sas"></a>共享访问签名 (SAS) 安全性

通知中心实现称为 SAS（共享访问签名）的实体级安全方案。 此方案允许消息实体在其描述中最多声明 12 个用于向该实体授权的授权规则。

每个规则包含名称、 键值 （共享机密） 和一组权限，如中所述[安全声明](#security-claims)。 创建通知中心时，会自动创建两个规则： 一个具有**侦听**权限 （即客户端应用程序使用），另一个使用**所有**权限 （即应用后端使用）。

通过客户端应用执行注册管理时，如果通过通知发送的信息不敏感（例如，天气更新），则访问通知中心的常用方法是，向客户端应用提供规则仅限侦听访问权限的密钥值，并向应用后端提供规则完全访问权限的密钥值。

应用不应在 Windows 应用商店客户端应用程序中嵌入密钥值，而具有客户端应用程序在启动应用后端从其进行检索。

使用键**侦听**访问可让客户端应用以注册任何标记。 如果您的应用程序必须注册限制到特定的客户端 （例如，当标记表示用户 Id） 的特定标记，应用后端必须执行注册。 有关详细信息，请参阅[注册管理](notification-hubs-push-notification-registration-management.md)。 请注意，采用这种方式时，客户端应用将无权直接访问通知中心。

## <a name="security-claims"></a>安全声明

与其他实体类似，可以针对以下三种安全声明执行通知中心操作：**侦听**，**发送**，和**管理**。

| 声明   | 描述                                          | 允许的操作 |
| ------- | ---------------------------------------------------- | ------------------ |
| 侦听  | 创建/更新、读取和删除单一注册。 | 创建/更新注册<br><br>读取注册<br><br>读取句柄的所有注册<br><br>删除注册 |
| 发送    | 向通知中心发送消息                | 发送消息 |
| 管理  | 通知中心的 CRUD（包括更新 PNS 凭据和安全密钥），以及基于标记读取注册。 |创建/更新/读取/删除通知中心<br><br>按标记读取注册 |

通知中心接受的签名令牌生成具有共享直接在通知中心上配置的密钥。

不能向多个命名空间发送通知。 命名空间是通知中心的逻辑容器，不参与发送通知。
命名空间级别的访问策略（凭据）可用于命名空间级别的操作，例如：列出通知中心、创建或删除通知中心等。只有中心级别访问策略才允许你发送通知。
