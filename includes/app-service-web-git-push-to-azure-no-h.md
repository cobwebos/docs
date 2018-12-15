---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 026540290710d039dbc06c394ab538ebe2d7c12f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344663"
---
回到本地终端窗口，将 Azure 远程功能添加到本地 Git 存储库。 将 _&lt;deploymentLocalGitUrl-from-create-step>_ 替换为从[创建 Web 应用](#create-a-web-app)保存的 Git 远程 URL。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

使用以下命令推送到 Azure 远程功能以部署应用。 当 Git 凭据管理器提示输入凭据时，请确保输入在[配置部署用户](#configure-a-deployment-user)中创建的凭据，而不是用于登录到 Azure 门户的凭据。

```bash
git push azure master
```

此命令可能需要花费几分钟时间运行。 运行时，该命令会显示类似于以下示例的信息：
