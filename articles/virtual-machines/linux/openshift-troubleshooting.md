---
title: 在 Azure 中排查 OpenShift 部署问题 | Microsoft Docs
description: 在 Azure 中排查 OpenShift 部署问题。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2017
ms.locfileid: "24139444"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>在 Azure 中排查 OpenShift 部署问题

如果 OpenShift 群集未成功部署，可通过完成这些故障排除任务来缩小问题范围。 查看部署状态，并与以下退出代码列表进行比较：

- 退出代码 3：Red Hat 订阅用户名/密码或组织 ID/激活密钥不正确
- 退出代码 4：Red Hat 池 ID 不正确或无可用授权
- 退出代码 5：未能预配 Docker 精简池卷
- 退出代码 6：OpenShift 群集安装失败
- 退出代码 7：OpenShift 群集安装成功，但 Azure 云解决方案提供程序配置失败 - 主节点上的主机配置出现问题
- 退出代码 8：OpenShift 群集安装成功，但 Azure 云解决方案提供程序配置失败 - 主节点上的节点配置出现问题
- 退出代码 9：OpenShift 群集安装成功，但 Azure 云解决方案提供程序配置失败 - 基础结构或应用节点上的节点配置出现问题
- 退出代码 10：OpenShift 群集安装成功，但 Azure 云解决方案提供程序配置失败 - 正在更正主节点或无法将主机设为“不可安排”
- 退出代码 11：未能部署指标
- 退出代码 12：未能部署日志记录

对于退出代码 7 - 10，OpenShift 群集已安装，但 Azure 云解决方案提供程序配置失败。 可通过 SSH 连接到主节点 (OpenShift Origin) 或守护节点（OpenShift 容器平台），并通过 SSH 从此处连接到每个群集节点来解决问题。

退出代码 7 - 9 的常见失败原因是，服务主体没有适当的权限访问订阅或资源组。 如果是此问题，则请分配适当的权限并手动重新运行导致所有后续脚本失败的脚本。

再次执行脚本前，请务必重启失败的服务（例如，systemctl restart atomic-openshift-node.service）。

对于进一步的故障排除，请通过 SSH 连接到端口 2200 上的主节点 (Origin) 或端口 22 上的守护节点（容器平台）中。 以 root 身份操作 (sudo su -)，浏览到以下目录：/var/lib/waagent/custom-script/download。

在此处可以看到名为“0”和“1”的文件夹。 在每个文件夹中，可看到两个文件：“stderr”和“stdout”。 浏览这些文件，确定错误发生的位置。
