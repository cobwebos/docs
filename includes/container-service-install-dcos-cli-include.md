---
title: "安装 DC/OS CLI |Microsoft 文档"
description: "安装 DC/OS CLI。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "容器，微服务 DC/OS Azure"
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: a8ea47f158c0d666340815d2e039995c7483257f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
> [!NOTE]
> 这是用于处理基于 DC/操作系统的 ACS 群集。 没有无需为 Swarm 基于 ACS 群集执行此操作。
> 
> 

首先，[连接到基于 DC/操作系统的 ACS 群集](../articles/container-service/container-service-connect.md)。 完成连接后，你可以在下面的命令与客户端计算机上安装 DC/OS CLI:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

如果你将对旧版本的 Python，你可能注意到某些"InsecurePlatformWarnings"。 你可以放心地忽略这些。

若要开始，无需重新启动 shell，运行：

```bash
source ~/.bashrc
```

当你启动新的 shell，则不会需要此步骤。

现在，你可以确认安装 CLI:

```bash
dcos --help
```