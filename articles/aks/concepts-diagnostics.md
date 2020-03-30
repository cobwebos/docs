---
title: Azure 库伯奈斯服务 （AKS） 诊断概述
description: 了解 Azure 库伯奈斯服务中的自诊断群集。
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126605"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure 库伯奈斯服务诊断（预览）概述

解决 Azure 库伯奈斯服务 （AKS） 群集问题是维护群集的重要组成部分，尤其是在群集运行任务关键型工作负载时。 AKS 诊断是一种智能的自诊断体验，可帮助您识别和解决群集中的问题。 AKS 诊断是云原生诊断，您可以使用它，无需额外的配置或计费成本。

此功能现已处于公共预览版中。

## <a name="open-aks-diagnostics"></a>打开 AKS 诊断

要访问 AKS 诊断：

- 导航到[Azure 门户](https://portal.azure.com)中的库伯内斯群集。
- 单击"**诊断并解决**左侧导航中的问题"，该导航将打开 AKS 诊断。
- 使用主页磁贴中的关键字选择最能描述群集问题的类别，或在搜索栏中键入最能描述问题关键字，例如_群集节点问题_。

![主页](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>查看诊断报告

单击类别后，您可以查看特定于群集的诊断报告。 如果群集中存在状态图标的问题，诊断报告会智能调用。 您可以通过单击 **"更多信息**"来深入了解每个主题，以查看问题的详细描述、建议的操作、指向有用文档的链接、相关指标和日志记录数据。 在运行各种检查后，根据群集的当前状态智能生成诊断报告。 诊断报告是确定群集问题和查找解决问题的后续步骤的有用工具。

![诊断报告](./media/concepts-diagnostics/diagnostic-report.png)

![扩展诊断报告](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>群集洞察

以下诊断检查可在**群集见解**中提供。

### <a name="cluster-node-issues"></a>群集节点问题

群集节点问题检查可能导致群集意外行为的节点相关问题。

- 节点就绪问题
- 节点故障
- 资源不足
- 节点缺少 IP 配置
- 节点 CNI 故障
- 找不到节点
- 节点电源关闭
- 节点身份验证失败
- 节点库贝代理过时

### <a name="create-read-update--delete-operations"></a>创建、读取、更新&删除操作

CRUD 操作检查可能导致群集中问题的任何 CRUD 操作。

- 使用中的子网删除操作错误
- 网络安全组删除操作错误
- 使用中路由表删除操作错误
- 引用的资源预配错误
- 公共 IP 地址删除操作错误
- 部署失败，由于部署配额
- 由于组织策略而导致的操作错误
- 缺少订阅注册
- VM 扩展预配错误
- 子网容量
- 超出配额错误

### <a name="identity-and-security-management"></a>标识和安全管理

身份和安全管理检测可能阻止与群集通信的身份验证和授权错误。

- 节点授权失败
- 401 错误
- 403 错误

## <a name="next-steps"></a>后续步骤

收集日志，以帮助您使用[AKS Periscope](https://aka.ms/aksperiscope)进一步解决群集问题。

通过在标题中添加"[Diag]"，在[UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks)上发布您的问题或反馈。
