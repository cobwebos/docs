---
title: "include 文件"
description: "include 文件"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
资源锁可以防止组织中的用户意外删除或修改重要资源。 与基于角色的访问控制不同，资源锁对所有用户和角色应用限制。 

可以将锁定级别设置为 **CanNotDelete** 或 **ReadOnly**。 在门户中，锁定级别分别显示为“删除”和“只读”。

* **CanNotDelete** 表示经授权的用户仍可读取和修改资源，但不能删除资源。 
* **ReadOnly** 表示经授权的用户可以读取资源，但不能删除或更新资源。 应用此锁类似于将所有经授权的用户限制于使用“读者”角色授予的权限。 

> [!TIP]
> 应用 **ReadOnly** 锁时请小心。 某些看起来像读取操作的操作实际需要其他操作。 例如，存储帐户上的 **ReadOnly** 锁将阻止所有用户列出密钥。 列出密钥操作通过 POST 请求进行处理，因为返回的密钥可用于写入操作。 应用服务资源上的 **ReadOnly** 锁将阻止 Visual Studio 服务器资源管理器显示资源文件，因为该交互需要写访问权限。

在父范围应用锁时，该范围内所有资源都将继承相同的锁。 即使是之后添加的资源也会从父作用域继承该锁。 继承中限制性最强的锁优先执行。

Resource Manager 锁仅适用于管理平面内发生的操作，包括发送到 `https://management.azure.com` 的操作。 锁不会限制资源处理其自己的功能的方式。 资源更改将受到限制，但资源操作不受限制。 例如，SQL 数据库上的 ReadOnly 锁会阻止你删除或修改数据库。 它不会阻止你在数据库中创建、更新或删除数据。 允许数据事务，因为这些操作不会发送到 `https://management.azure.com`。

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>谁可以在组织中创建或删除锁
若要创建或删除管理锁，必须有权执行 `Microsoft.Authorization/locks/*` 操作。 在内置角色中，只有“所有者”和“用户访问管理员”有权执行这些操作。
