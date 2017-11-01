---
title: "2017 年 10 月冲刺 (sprint) 0 的 Azure ML Workbench 发行说明"
description: "本文档详细介绍了 Azure ML 的冲刺 0 发行版的更新。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="sprint-0---october-2017"></a>冲刺 (sprint) 0 - 2017 年 10 月 

**版本号：0.1.1710.04013**

欢迎使用自 Microsoft Ignite 2017 大会发布 Azure Machine Learning Workbench 初始公共预览版以来的首个更新。 此发行版中的主要更新是可靠性和稳定性修复。  我们已解决的一些严重问题包括：

## <a name="new-features"></a>新增功能
- 现在已支持 macOS High Sierra

## <a name="bug-fixes"></a>Bug 修复
### <a name="workbench-experience"></a>Workbench 体验
- 将文件拖放到 Workbench 中导致 Workbench 崩溃。
- 作为 Workbench 的 IDE 配置的 VS Code 中的终端窗口不能识别 _az ml_ 命令。

### <a name="workbench-authentication"></a>Workbench 身份验证
我们已做了大量更新来改进所报告的各种登录和身份验证问题。
- 身份验证窗口一直保持弹出状态，尤其是当 Internet 连接不稳定时。
- 改进了与身份验证令牌过期相关的可靠性问题。
- 在某些情况下，身份验证窗口出现两次。
- 当身份验证过程实际上已完成并且弹出对话框已关闭时，Workbench 主窗口仍然显示“正在进行身份验证”消息。
- 如果没有 Internet 连接，则身份验证对话框会弹出一个空白屏幕。

### <a name="data-preparation"></a>数据编制 
- 筛选特定值时，错误和缺失值也将筛选出来。
- 更改采样策略会删除后续的现有联接操作。
- 替换缺失值转换不考虑 NaN。
- 遇到 null 值时，日期类型推理引发异常。

### <a name="job-execution"></a>作业执行
- 当作业执行因为项目文件夹超出了大小限制而无法将其上传时，没有显示明确的错误消息。
- 如果用户的 Python 脚本更改了工作目录，则不会跟踪写入到输出文件夹中的文件。 
- 如果处于活动状态的 Azure 订阅不同于当前项目所属的订阅，则作业提交会导致 403 错误。
- 不存在 Docker 时，如果用户尝试使用 Docker 作为执行目标，则不会返回明确的错误消息。
- 当用户单击“运行”按钮时，.runconfig 文件不会自动保存。

### <a name="jupyter-notebook"></a>Jupyter Notebook
- 当用户使用某些登录类型时，Notebook 服务器无法启动。
- Notebook 服务器错误消息未出现在对用户可见的日志中。

### <a name="azure-portal"></a>Azure 门户
- 选择 Azure 门户的深色主题会导致“模型管理”边栏选项卡显示为一个黑色框。

### <a name="operationalization"></a>实施
- 重复使用某个清单更新 Web 服务会导致使用随机名称构建新的 Docker 映像。
- 无法从 Kubernetes 群集检索 Web 服务日志。
- 当用户尝试创建模型管理帐户或 ML 计算帐户并遇到权限问题时，会输出误导性错误消息。
