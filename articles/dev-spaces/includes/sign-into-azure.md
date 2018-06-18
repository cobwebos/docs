---
title: include 文件
description: include 文件
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: d8ef59b157dc01c50d96561df31bbca4a8505018
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825488"
---
### <a name="sign-in-to-azure-cli"></a>登录 Azure CLI
登录 Azure。 在终端窗口中键入以下命令：

```cmd
az login
```

> [!Note]
> 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。

#### <a name="if-you-have-multiple-azure-subscriptions"></a>如果有多个 Azure 订阅...
通过运行以下命令可以查看订阅： 

```cmd
az account list
```
在 JSON 输出中找到具有 `isDefault: true` 的订阅。
如果这不是你想要使用的订阅，可以更改默认订阅：

```cmd
az account set --subscription <subscription ID>
```
