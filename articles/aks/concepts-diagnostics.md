---
title: Azure Kubernetes 服务（AKS）诊断概述
description: 了解 Azure Kubernetes 服务中的自我诊断群集。
services: container-service
author: yunjchoi
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 2148c6c8c9d78355847b3240b54349f1e7789921
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291373"
---
# <a name="azure-kubernetes-service-aks-diagnostics-overview"></a>Azure Kubernetes 服务（AKS）诊断概述

排查 Azure Kubernetes 服务（AKS）群集问题是维护群集的重要部分，尤其是在群集运行任务关键型工作负荷的情况下。 AKS 诊断是一种智能的自我诊断体验，可帮助你识别和解决群集中的问题。 AKS 诊断是云本机的，你可以使用它，而无需额外配置或计费成本。

## <a name="open-aks-diagnostics"></a>打开 AKS 诊断

访问 AKS 诊断：

- 在[Azure 门户](https://portal.azure.com)中导航到 Kubernetes 群集。
- 在左侧导航栏中单击 "**诊断和解决问题**"，这将打开 AKS 诊断。
- 通过使用 "主页" 磁贴中的关键字，选择最能描述群集问题的类别，或在搜索栏中键入最能描述你的问题的关键字，例如_群集节点问题_。

![主页](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>查看诊断报告

单击某个类别后，可以查看特定于群集的诊断报告。 如果群集中存在状态图标，则诊断报告将智能地调用。 单击 "**详细信息**" 可查看问题的详细描述、建议操作、指向有用文档的链接、相关度量和日志记录数据，从而向下钻取每个主题。 诊断报告在运行各种检查后基于群集的当前状态进行智能生成。 诊断报告可能是一个有用的工具，可用于查明群集的问题并找到解决此问题的后续步骤。

![诊断报告](./media/concepts-diagnostics/diagnostic-report.png)

![展开的诊断报告](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>群集见解

以下诊断检查在**群集见解**中可用。

### <a name="cluster-node-issues"></a>群集节点问题

群集节点问题会检查与节点相关的问题，这些问题可能会导致群集意外运行。

- 节点就绪问题
- 节点故障
- 资源不足
- 节点缺少 IP 配置
- 节点 CNI 故障
- 找不到节点
- 节点关机
- 节点身份验证失败
- Node kube-proxy 过时

### <a name="create-read-update--delete-operations"></a>创建、读取、更新 & 删除操作

CRUD 操作检查可能导致群集中出现问题的任何 CRUD 操作。

- 正在使用的子网删除操作错误
- 网络安全组删除操作错误
- 正在使用的路由表删除操作错误
- 引用的资源设置错误
- 公共 IP 地址删除操作错误
- 部署失败，因为部署配额
- 由于组织策略，操作错误
- 缺少订阅注册
- VM 扩展设置错误
- 子网容量
- 超出了配额错误

### <a name="identity-and-security-management"></a>标识和安全管理

标识和安全管理将检测可阻止与群集通信的身份验证和授权错误。

- 节点授权失败
- 401错误
- 403 错误

## <a name="next-steps"></a>后续步骤

收集日志，以帮助你使用[AKS Periscope](https://aka.ms/aksperiscope)进一步排查群集问题。

通过在标题中添加 "[诊断]"，在[UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks)上发布你的问题或反馈。
