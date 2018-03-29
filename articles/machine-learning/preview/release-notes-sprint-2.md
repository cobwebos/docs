---
title: 2017 年 12 月冲刺 (sprint) 2 的 Azure ML Workbench 发行说明
description: 本文档详细介绍了 Azure ML 的冲刺 (sprint) 2 发行版的更新
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: cb620040f8c82fd2015f93963d99739d38ec6db3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2018
---
# <a name="sprint-2---december-2017"></a>冲刺 (sprint) 2 - 2017 年 12 月 

#### <a name="version-number-01171115263"></a>版本号：0.1.1711.15263

>此处介绍了如何[查找版本号](known-issues-and-troubleshooting-guide.md)。

欢迎使用 Azure Machine Learning Workbench 的第三个更新。 此更新包括 Workbench 应用、命令行接口 (CLI) 以及后端服务中的改进。 非常感谢你向我们发送微笑和哭脸。 下面的许多更新就是针对你的反馈的直接结果。 

## <a name="notable-new-features"></a>值得注意的新功能
- [支持 SQL Server 和 Azure SQL 数据库用作数据源](data-prep-appendix2-supported-data-sources.md#types) 
- [使用 MMLSpark 深入学习具备 GPU 支持的 Spark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [所有已部署的 AML 容器都与 Azure IoT Edge 设备兼容（无需额外步骤）](http://aka.ms/aml-iot-edge-blog)
- Azure 门户中提供了已注册的模型列表和详细信息视图
- 除了基于用户名/密码的身份验证，还使用基于 SSH 密钥的身份验证访问计算目标。 
- 引入新的模式频率检查器提供数据准备体验。 

## <a name="detailed-updates"></a>详细的更新
下面列出了此冲刺 (sprint) 中 Azure 机器学习的每个组件区域中的详细更新。

### <a name="installer"></a>安装程序
- 安装程序可以自动更新，用户无需重新安装就可以修复 Bug 并获取新功能。

### <a name="workbench-authentication"></a>Workbench 身份验证
- 修复了身份验证系统的多个问题。 如果仍然遇到登录问题，请通知我们。
- 更改了 UI，可更轻松地找到代理管理器设置。

### <a name="workbench"></a>工作台
- 只读文件视图现在具有浅蓝色背景
- 将“编辑”按钮移到了右侧，更易于发现。
- “dsource”、“dprep”和“ipynb”文件格式现在可以原始文本格式呈现
- Workbench 现在拥有新的编辑体验，可引导用户使用外部 IDE 来编辑脚本，并可直接使用 Workbench 来编辑具有丰富编辑体验的文件类型（如 Notebook、数据源、数据准备包）
- 现在可以更快地加载用户有权访问的工作区和项目列表

### <a name="data-preparation"></a>数据准备工作 
- 可使用模式频率检查器在列中查看字符串模式。 还可以使用这些模式来筛选数据。 这显示了一个类似值计数检查器的视图。 不同之处在于，模式频率显示的是数据唯一模式的计数，而不是唯一数据的计数。 还可以筛选出或筛选掉符合某种模式的所有行。

![“产品编号”上模式频率检查器的图像](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- 改进了性能，同时建议在“按示例派生列”转换中查看边缘事例

- [支持 SQL Server 和 Azure SQL 数据库用作数据源](data-prep-appendix2-supported-data-sources.md#types) 

![新建 SQL Server 数据源的图像](media/release-notes-sprint-2/sql-server-data-source.png)

- 启用了行和列计数的“概览”视图

![概览模式下行列计数的图像](media/release-notes-sprint-2/row-col-count.png)

- 在所有计算上下文中启用了数据准备
- 在所有计算上下文中启用了使用 SQL Server 数据库的数据源
- 可以按数据类型筛选数据准备网格列
- 修复了将多个列转换为日期时存在的问题
- 修复了在高级模式下更改输出列名称后用户可选择输出列作为“按示例派生列”中的源的问题。

### <a name="job-execution"></a>作业执行
现在可以按照以下步骤使用基于 SSH 密钥的身份验证创建和访问 remotedocker 或群集类型的计算目标：
- 在 CLI 中使用以下命令附加计算目标

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] 在命令中指定 -k（或 --use-azureml-ssh-key）选项以生成并使用 SSH 密钥。

- Azure ML Workbench 将生成一个公钥，并将其输出至控制台。 使用相同的用户名登录到计算目标，然后将此公钥追加至 ~/.ssh/authorized_keys 文件。

- 可以准备此计算目标并将其用于执行，Azure ML Workbench 会使用此密钥来进行身份验证。  

有关创建计算目标的详细信息，请参阅 [配置 Azure 机器学习试验服务](experimentation-service-configuration.md)

### <a name="visual-studio-tools-for-ai"></a>用于 AI 的 Visual Studio Tools
- 增加了对 [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017)的支持。 

### <a name="command-line-interface-cli"></a>命令行接口 (CLI)
- 增加了 `az ml datasource create` 命令，以允许从命令行创建数据源文件

### <a name="model-management-and-operationalization"></a>模型管理和操作化
- [所有已操作化的 AML 容器都与 Azure IoT Edge 设备兼容（无需额外步骤）](http://aka.ms/aml-iot-edge-blog) 
- 改进了 o16n CLI 中的错误消息
- 修复了模型管理门户 UX 中的 Bug  
- 详细信息页中模型管理属性的字母保持大小写一致
- 实时评分调用超时设置为 60 秒
- Azure 门户中提供了已注册的模型列表和详细信息视图

![门户中提供了模型详细信息](media/release-notes-sprint-2/model-list.jpg)

![门户中提供了模型概述](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- 深入学习具备 [GPU 支持](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)的 Spark
- 支持资源管理器模板，可轻松部署资源
- 支持 SparklyR 生态系统
- [AZTK 集成](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>示例项目
- 使用新的 Azure ML SDK 版本更新了 [Iris](https://github.com/Azure/MachineLearningSamples-Iris) 和 [MMLSpark](https://github.com/Azure/mmlspark) 示例

## <a name="breaking-changes"></a>重大更改
- 将 `az ml computetarget attach` 中的 `--type` 开关提升到了子命令。 

    - `az ml computetarget attach --type remotedocker` 现为 `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` 现为 `az ml computetarget attach cluster`
