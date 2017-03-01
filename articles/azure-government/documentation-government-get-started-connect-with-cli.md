---
title: "连接到具有 Azure CLI 的 Azure 政府 | Microsoft Docs"
description: "有关通过与 Azure CLI 连接在 Azure 政府中管理订阅的信息"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c7cbe993-375e-4aed-9aa5-2f4b2111f71c
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 6e070870a93d2636f076b339dd401358735a9bd0
ms.lasthandoff: 02/21/2017


---


# <a name="connect-to-azure-government-with-azure-cli"></a>连接到具有 Azure CLI 的 Azure 政府

若要使用 Azure CLI，需要连接到 Azure 政府，而不是 Azure 公共版。 Azure CLI 可以用于通过脚本管理大型订阅或访问当前在 Azure 门户中不可用的功能。 如果已在 Azure 公共版中使用过 Azure CLI，它们之间的差别不大。  Azure 政府版的区别如下：

可通过多种方式来[安装 Azure CLI](https://docs.microsoft.com/en-us/azure/xplat-cli-install)。 如果已安装了节点，则最简单的方法是安装 npm 包：

若要通过 npm 包安装 CLI，请确保已下载并安装了[最新的 Node.js 和 npm](https://nodejs.org/en/download/package-manager/)。 然后，运行 **npm install** 来安装 azure-cli 包：

```bash
npm install -g azure-cli
```

在 Linux 分发版中，可能需要使用 **sudo** 才能成功运行 **npm** 命令，如下所示：

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> 如果需要在 Linux 分发版或 OS 中安装或更新 Node.js 和 npm，建议安装最新的 Node.js LTS 版本 (4.x)。 如果使用旧版本，可能会遇到安装错误。


安装了 Azure CLI 之后，需要登录到 Azure 政府：

```
azure login --username your-user-name@your-gov-tenant.onmicrosoft.com  --environment AzureUSGovernment
```

登录之后，便可以如往常一样运行 Azure CLI 命令：

```
azure webapp list my-resource-group
```
