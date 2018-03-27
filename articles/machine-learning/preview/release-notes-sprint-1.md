---
title: "2017 年 11 月冲刺 (sprint) 1 的 Azure ML Workbench 发行说明"
description: "本文档详细介绍了 Azure ML 的冲刺 (sprint) 1 发行版的更新"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: a4945c77be5763ffeda328184149f712572937c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="sprint-1---november-2017"></a>冲刺 (sprint) 1 - 2017 年 11 月 

版本号：0.1.1710.04013

欢迎使用 Azure 机器学习 Workbench 的第二个更新。 我们将继续改进 Workbench 应用、 CLI 及后端服务层中的安全性、稳定性及可维护性。 非常感谢向我们发送微笑和哭脸。 下面的许多更新就是你反馈的直接结果。 请持续关注！

## <a name="notable-new-features"></a>值得注意的新功能
- 现在有两个新的 Azure 区域可提供 Azure ML：西欧和东南亚。 这两个区域加上以前的区域：“美国东部 2”、“美国中西部”和“澳大利亚东部”，使得部署区域的总数达到 5 个。
- 我们在 Workbench 应用中启用了 Python 代码语法突出显示功能，便于读取和编辑 Python 源代码。 
- 现在，你可以直接从文件中启动你喜欢的 IDE，而不是从整个项目中启动。  在 Workbench 中打开一个文件，然后单击“编辑”，即可启动当前文件和项目的 IDE（当前支持 VS Code 和 PyCharm）。  此外，也可以单击“编辑”按钮旁边的箭头，在 Workbench 文本编辑器中编辑文件。  文件在你单击“编辑”之前是只读的，用以防止意外更改。
- 常用的绘图库 `matplotlib` 版本 2.1.0 现在随 Workbench 应用一起提供。
- 我们已将适用于数据准备引擎的 .NET Core 版本升级到 2.0。 这不仅可以省去在 macOS 上安装应用期间使用 brew-install openssl 要求， 而且也为将在不久之后引入更多令人兴奋的数据准备功能铺平了道路。 
- 我们已启用特定于版本的应用主页，以便获取更具相关性的发行说明，以及基于当前应用版本的更新提示。
- 如果你的本地用户名包含空格，则应用程序现在可以成功安装。 

## <a name="detailed-updates"></a>详细的更新
下面列出了此冲刺 (sprint) 中 Azure 机器学习的每个组件区域中的详细更新。

### <a name="installer"></a>安装程序
- 应用安装程序现在将清理由旧版应用创建的安装目录。
- 修复了导致安装程序在 macOS High Sierra 上陷入 100% 停滞的 bug。
- 现在有了到安装程序目录的直接链接，用户可在安装失败的情况下查看安装程序日志。
- 安装现在适用于用户名中有空格的用户。

### <a name="workbench-authentication"></a>Workbench 身份验证
- 支持在代理管理器中进行身份验证。
- 如果用户位于防火墙后面，现在则可成功登录。 
- 如果用户在多个 Azure 区域具有试验帐户并且有一个区域碰巧不可用，则应用将不再挂起。
- 未完成身份验证且仍然可以看到身份验证对话框时，应用将不再尝试从本地缓存中加载工作区。

### <a name="workbench-app"></a>工作台应用
- 在文本编辑器中启用了 Python 代码语法突出显示。
- 通过文本编辑器中的“编辑”按钮，你可以在 IDE（支持 VS Code 和 PyCharm）或内置的文本编辑器中编辑该文件。
- 文本编辑器默认处于只读模式。 
- 在当前文件保存后，保存按钮可视状态现在更改到已禁用，因此不再包含脏数据。
- 启动运行时，Workbench 将保存“所有”未保存的文件。
- Workbench 会记住上次在本地计算机上使用的工作区以便它自动打开。
- 现在仅允许运行 Workbench 的单个实例。 以前可以启动多个实例，但在同一个项目中运行时这样做会导致问题。
- “重命名的文件”菜单“打开项目...”到“添加现有文件夹作为项目...” 
- 现在选项卡切换速度更快。
- 帮助链接已添加到“配置 IDE”对话框中。
- 反馈表单现在会记住你上次输入的电子邮件地址。
- 微笑和哭脸表单文本区域现在更大，因此，你可以向我们发送更多反馈！ 
- `az ml workspace create` 中的 `--owner` 切换可帮助文本得到更正。
- 我们添加了一个“关于”对话框，以帮助用户轻松查看和复制应用的版本号。
- “建议一项功能”菜单项被添加到“帮助”菜单。
- 实验帐户名称现在显示在应用标题栏中，前面是应用名称“Azure 机器学习 Workbench”。
- 特定于版本的应用主页现在基于检测到的应用版本显示。

### <a name="data-preparation"></a>数据准备工作 
- 无法再从地图检查器加载外部网站，以防止潜在的安全问题。
- 直方图和值计数检查器现在提供了用对数刻度显示图形的选项。
- 当计算处于进行中时，数据质量栏现在可显示不同的颜色，以表示“正在计算”的状态。
- 列度量值现在显示分类值列的统计信息。
- 数据源名称中的最后一个字符不再被截断。
- 切换选项卡时数据准备包现在保持打开状态，从而显著提升性能。
- 在数据源中，在数据视图和指标视图之间切换时，列的顺序不再更改。
- 打开无效的 `.dprep` 或 `.dsource` 文件不再导致 Workbench 崩溃。
- 数据准备包现在可以为“写入到 CSV”转换中的输出使用相对路径。
- “保留列”转换现在允许用户在编辑时添加其他列。
- “替换此内容”菜单中现在实际上启动了“替换值”对话框。
- “替换值”转换现在按预期方式运行，而不再引发错误。
- 现在，数据准备包在引用项目文件夹之外的数据文件时使用绝对路径，这样可以在本地上下文中以数据文件的绝对路径运行准备包。
- 将 Azure blob 用作数据源时，现在支持将“完整文件”作为采样策略。
- （从数据准备包）生成的 Python 代码现在会带来 CR 和 LF，使其在 Windows 中变得友好。
- 切换到“数据”视图时，“选择度量值”下拉列表现在可隐藏属性。
- Workbench 现在可以处理 parquet 文件，即使它使用 Python 运行时也是如此。 以前处理 parquet 文件时仅可以使用 Spark。 
- 筛选列中数据类型为 date 的值时不再导致数据准备引擎崩溃。
- 度量值视图现在会遵守采样策略更新。
- 远程采样作业现在工作正常。

### <a name="job-execution"></a>作业执行
- 运行历史记录中现在包括自变量。
- CLI 中启动的作业现在自动显示在“运行历史记录”作业面板中。
- 作业面板现在显示由来宾用户创建并添加到 AAD 租户的作业。
- 取消和删除作业面板操作会更为稳定。
- 单击“运行”按钮时，如果配置文件的格式错误，现在会触发错误消息。
- 终止应用程序不再干扰 CLI 中启动的作业。
- 即使在执行一小时之后，CLI 中启动的作业也将继续筛选标准。
- 数据准备包在 Python/PySpark 中运行失败时，则会显示更详细的错误消息。
- `az ml experiment clean` 现在也清理远程 VM 中的 Docker 映像。
- 现在，`az ml experiment clean` 在 macOS 上可正常用于本地目标。
- 定位本地或远程 Docker 运行时的错误消息已被清除，并且易于阅读。
- 在附加为一个执行目标时，如果 HDInsight 群集头节点名称格式不正确，则将显示更详细的错误消息。
- 在凭据服务中找不到密钥时，会显示更详细的错误消息。 
- MMLSpark 库已升级，现在可以支持 Apache Spark 2.2。
- MMLSpark 现在包含医疗文档的主题编码转换（网格编码）。
- `matplotlib` 版本 2.1.0 现已与 Workbench 一起提供。

### <a name="jupyter-notebook"></a>Jupyter 笔记本
- Notebook 名称搜索现在可在 Notebook 视图中正常使用。
- 现在可以在 Notebook 视图中删除 Notebook。
- 已添加新的 Magic `%upload_artifact`，用于将在 Notebook 执行环境中生成的文件上传到运行历史记录数据存储中。
- 内核错误现在显示在 Notebook 作业状态中，更加易于调试。
- 用户退出应用时，Jupyter 服务器现在可正确关闭。

### <a name="azure-portal"></a>Azure 门户
- 现在可以在两个新的 Azure 区域创建试验帐户和模型管理帐户：西欧和东南亚。
- 模型管理帐户 DevTest 计划现在只有在订阅中第一个创建时才可用。 
- Azure 门户中的帮助链接已更新以指向正确的文档页。
- 由于不适用，描述字段已从 Docker 映像详细信息页中删除。
- 包括 AppInsights 和自动缩放设置在内的详细信息已添加到 Web 服务详细信息页。
- 即使在浏览器中禁用了第三方 cookie，现在也可以呈现模型管理页面。 

### <a name="operationalization"></a>实施
- 在其名称中有“score”的 Web 服务不再失败。
- 用户现在只需对 Azure 资源组或订阅具有参与者访问权限，即可创建部署环境。 不再需要拥有整个订阅的所有者访问权限。
- 操作化 CLI 现已可在 Linux 中使用选项卡自动完成功能。
- 映像构造服务现在支持为 Azure IoT 服务/设备构建映像。

### <a name="sample-projects"></a>示例项目
- [_Classifying Iris_](./tutorial-classifying-iris-part-1.md) 示例项目：
    - `iris_pyspark.py` 已重命名为 `iris_spark.py`。
    - `iris_score.py` 已重命名为 `score_iris.py`。
    - `iris.dprep` 和 `iris.dsource` 已更新以反映最新的数据准备引擎更新。
    - `iris.ipynb` Notebook 已经过修改，可在 HDInsight 群集中工作。
    - 运行历史记录已在 `iris.ipynb` Notebook 单元格中打开。
- [](./tutorial-bikeshare-dataprep.md)“使用自行车共享数据的高级数据准备”示例项目的“处理错误值”步骤已修复。
- [](https://github.com/Azure/MachineLearningSamples-mmlspark)“针对成人人口普查数据的 MMLSpark”示例项目 `docker.runconfig` 的格式已从 JSON 更新到 YAML。
- [](./scenario-distributed-tuning-of-hyperparameters.md)“分布式超参数优化”示例项目 `docker.runconfig` 的格式已从 JSON 更新到 YAML。
- 新建示例项目“使用 CNTK 的图像分类”[](./scenario-image-classification-using-cntk.md)。
