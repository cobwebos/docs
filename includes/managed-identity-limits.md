---
title: include 文件
description: include 文件
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971036"
---
- 按 [Azure AD 服务限制和局限性](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md)中所述，每个托管标识都计入 Azure AD 租户中的对象配额限制。
-   托管标识的创建速率有以下限制：

    1. 每个 Azure 区域每个 Azure AD 租户：每 20 秒 200 次创建操作。
    2. 每个 Azure 区域每个 Azure 订阅：每 20 秒 40 次创建操作。

- 创建用户分配的托管标识时，只能使用字母数字字符（0-9、a-z、A-Z）和连字符 (-)。 要使虚拟机或虚拟机规模集的分配正常工作，该名称限制为 24 个字符。
