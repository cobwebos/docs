---
title: "在 Azure 中排查 OpenShift 部署问题 | Microsoft 文档"
description: "在 Azure 中排查 OpenShift 部署问题"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: ea3664870480f6ed170972a5f52940dc4a852219
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshooting-openshift-deployment-in-azure"></a>在 Azure 中排查 OpenShift 部署问题

如果 OpenShift 群集未成功部署，可通过完成一些故障排除任务来缩小问题范围。 查看部署状态，并与以下退出代码列表进行比较。

- 退出代码 3：Red Hat 订阅用户名/密码或组织 ID/激活密钥不正确
- 退出代码 4：Red Hat 池 ID 不正确或无可用授权
- 退出代码 5：未能预配 Docker 精简池卷
- 退出代码 6：OpenShift 群集安装失败
- 退出代码 7：OpenShift 群集安装成功，但 Azure 云提供程序配置失败 - 主节点上的主机配置出现问题
- 退出代码 8：OpenShift 群集安装成功，但 Azure 云提供程序配置失败 - 主节点上的节点配置出现问题
- 退出代码 9：OpenShift 群集安装成功，但 Azure 云提供程序配置失败 - 基础结构或应用节点上的节点配置出现问题
- 退出代码 10：OpenShift 群集安装成功，但 Azure 云提供程序配置失败 - 正在更正主节点或无法将主机设为“不可安排”
- 退出代码 11：未能部署指标
- 退出代码 12：未能部署日志记录

对于退出代码 7 - 10，OpenShift 群集已安装，但 Azure 云提供程序配置失败。 可通过 SSH 连接到主节点 (Origin) 或堡垒节点（容器平台），并通过 SSH 从此处连接到群集中每个节点，然后修复问题。

退出代码 7 - 9 的常见失败原因是，服务主体没有适当的权限访问订阅或资源组。 如果确实是此问题，则请分配适当的权限并手动重新运行导致所有后续脚本失败的脚本。
再次执行脚本前，请务必重启失败的服务（例如，systemctl restart atomic-openshift-node.service）。

对于进一步的故障排除，请通过 SSH 连接到端口 2200 上的主节点 (Origin) 或端口 22 上的堡垒节点（容器平台）中。 需到达根 (sudo su -)，然后导航到以下目录：/var/lib/waagent/custom-script/download

应看到名为“0”和“1”的文件夹。 在每个文件夹中，可看到 2 个文件：stderr 和 stdout。 可浏览这些文件，确定故障发生的位置。
