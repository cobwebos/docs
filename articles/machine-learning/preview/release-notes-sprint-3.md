---
title: "2018 年 1 月冲刺 (sprint) 3 的 Azure ML Workbench 发行说明"
description: "本文档详细介绍了 Azure ML 的冲刺 (sprint) 3 发行版的更新"
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/22/2018
ms.openlocfilehash: 266a56d5e247fd4926031e563c8be302599838eb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="sprint-3---january-2018"></a>冲刺 (sprint) 3 - 2018 年 1 月 

#### <a name="version-number-01171218263"></a>版本号：0.1.1712.18263

>此处介绍了如何[查找版本号](known-issues-and-troubleshooting-guide.md)。

欢迎使用 Azure Machine Learning Workbench 的第四个更新。 下面是此冲刺 (sprint) 中的更新和改进。 其中许多更新是用户反馈的直接结果。 

## <a name="notable-new-features-and-changes"></a>值得注意的新功能和更改
- 对身份验证堆栈的更新强制在启动时进行登录和选择帐户

## <a name="detailed-updates"></a>详细的更新
下面列出了此冲刺 (sprint) 中 Azure 机器学习的每个组件区域中的详细更新。

### <a name="workbench"></a>工作台
- 能够通过“添加/删除程序”安装/卸载应用
- 对身份验证堆栈的更新强制在启动时进行登录和选择帐户
- Windows 上改进的单一登录 (SSO) 体验
- 使用不同凭据属于多个租户的用户现在将能够登录到 Workbench

#### <a name="ui"></a>UI
- 常规改进和 bug 修复

### <a name="notebooks"></a>笔记本
- 常规改进和 bug 修复

### <a name="data-preparation"></a>数据准备工作 
- 改进了执行按示例转换时的自动建议
- 改进了模式频率检查器的算法
- 能够在执行按示例转换时发送示例数据和反馈 ![执行派生列转换时发送反馈链接的图像](media/release-notes-sprint-3/SendFeedbackFromDeriveColumn.png)
- Spark 运行时改进
- Scala 已取代 Pyspark
- 修复了无法关闭不适用于时序检查器的数据 
- 修复了 HDI 数据准备执行的挂起时间

### <a name="model-management-cli-updates"></a>模型管理 CLI 更新 
  - 预配资源不再需要订阅所有权。 对资源组的参与者访问权限将足以设置部署环境。
  - 为免费订阅启用了本地环境设置 
