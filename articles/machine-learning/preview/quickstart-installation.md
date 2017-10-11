---
title: "Azure 机器学习服务安装快速入门 | Microsoft Docs"
description: "本快速入门介绍如何创建 Azure 机器学习资源，以及如何安装 Azure Machine Learning Workbench。"
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: hero-article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: bb3ac21d2e8b8efd7b4f6943fcc3256f18cbdd68
ms.contentlocale: zh-cn
ms.lasthandoff: 09/28/2017

---

# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>创建 Azure 机器学习预览帐户并安装 Azure Machine Learning Workbench
Azure 机器学习是一个集成式的端到端数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本快速入门介绍如何在 Azure 机器学习服务预览版中创建试验和模型管理帐户。 此外，介绍如何安装 Azure Machine Learning Workbench 桌面应用程序和 CLI 工具。 接下来，快速讲解有关使用永久[鸢尾花卉数据集](https://en.wikipedia.org/wiki/iris_flower_data_set)生成模型用于基于某些物理特征预测鸢尾花类型的 Azure 机器学习预览版功能。  

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
目前，Azure Machine Learning Workbench 桌面应用只能安装在以下操作系统上： 
- Windows 10
- Windows Server 2016
- macOS Sierra

>[!NOTE]
>尚不支持 macOS High Sierra。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户
登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-azure-machine-learning-accounts"></a>创建 Azure 机器学习帐户
使用 Azure 门户预配 Azure 机器学习帐户。 
1. 选择门户左上角的“新建”按钮 (+)。

2. 在搜索栏中键入“机器学习”。 选择名为“机器学习试验(预览版)”的搜索结果。  单击星形图标，将此选择项目加入 Azure 门户中的收藏夹。

   ![Azure 机器学习搜索](media/quickstart-installation/portal-more-services.png)

3. 单击“+ 添加”配置新的机器学习试验帐户。 此时会打开详细表单。

   ![机器学习试验帐户](media/quickstart-installation/portal-create-experimentation.png)

4. 在机器学习试验表单中填写以下信息：

   设置|建议的值|说明
   ---|---|---
   试验帐户名称 | 唯一的名称 |选择用于标识帐户的唯一名称。 可以使用自己的名称，或者能够最好地标识试验的部门或项目名称。 该名称的长度应为 2 到 32 个字符，只能包含字母数字字符和短划线字符“-”。 
   订阅 | 用户的订阅 |要用于试验的 Azure 订阅。 如果有多个订阅，请选择要计费的资源所在的相应订阅。
   资源组 | 你的资源组 | 可以创建新的资源组名称，也可以使用订阅中现有的。
   位置 | _离用户最近的区域_ | 选择最靠近用户和数据资源的位置。
   座席数 | #N/A | 键入座席数。 此项选择会影响[定价](https://azure.microsoft.com/pricing/details/machine-learning/)。 前两个座席是免费的。 对于本快速入门，请使用两个座席。 以后可根据需要在 Azure 门户中更新座席数。
   存储帐户 | 唯一的名称 | 选择“新建”并提供一个名称来创建新的 Azure 存储帐户，或选择“使用现有”并从下拉列表中选择现有的存储帐户。 存储帐户是必需的，用于保存项目内容和运行历史记录数据。 
   试验帐户的工作区 | 唯一的名称 | 为新工作区提供一个名称。 该名称的长度应为 2 到 32 个字符，只能包含字母数字字符和短划线字符“-”。
   分配工作区的所有者 | 你的帐户 | 选择自己的帐户作为工作区所有者。
   创建模型管理帐户 | *勾选标记* | 在体验创建试验帐户的过程中，可以选择同时创建机器学习模型管理帐户。 准备好将模型作为实时 Web 服务进行部署和管理后，即会使用此资源。 我们建议在创建试验帐户的同时创建模型管理帐户。
   帐户名 | 唯一的名称 | 选择用于标识模型管理帐户的唯一名称。 可以使用自己的名称，或者能够最好地标识试验的部门或项目名称。 该名称的长度应为 2 到 32 个字符，只能包含字母数字字符和短划线字符“-”。 
   模型管理定价层 | **DEVTEST** | 单击“未选择定价层”指定新模型管理帐户的定价层。 为了节省成本，请选择“DEVTEST”（如果可在订阅中使用，可用性受限），否则请选择 S1 定价层来节省成本。 单击“选择”保存定价层选择。 
   固定到仪表板 | _勾选标记_ | 选中“固定到仪表板”选项，以便轻松地在 Azure 门户的前端仪表板页上跟踪机器学习试验帐户。

5. 单击“创建”开始创建过程。

6. 在 Azure 门户工具栏的右上角，单击“通知”（钟形图标）监视部署过程。 

   通知显示“部署正在进行...”。 完成部署后，状态会更改为“部署成功”。 成功后，会打开机器学习试验帐户页。
   
   ![Azure 门户中的通知](media/quickstart-installation/portal-notification.png)

现在，根据在本地计算机上使用的操作系统，遵循以下两个部分之一在计算机上安装 Azure Machine Learning Workbench。 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>在 Windows 上安装 Azure Machine Learning Workbench
在运行 Windows 10、Windows Server 2016 或更高版本的计算机上安装 Azure Machine Learning Workbench。

1. 下载最新的 Azure Machine Learning Workbench 安装程序 **[AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi)**。

2. 在文件资源管理器中双击下载的安装程序 _AmlWorkbenchSetup.msi_。

   >[!IMPORTANT]
   >将安装程序整个下载到磁盘上，然后在磁盘中启动安装程序。 不要直接从浏览器的下载小组件启动安装程序。

3. 遵照屏幕说明完成安装。

   安装程序会下载所有必要的从属组件（例如 Python、Miniconda）和其他相关库。 完成安装所有组件可能需要大约半个小时。 

4. Azure Machine Learning Workbench 现已安装到以下目录中：
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>在 macOS 上安装 Azure Machine Learning Workbench
在运行 macOS Sierra 的计算机上安装 Azure Machine Learning Workbench。

1. 使用 [Homebrew](http://brew.sh) 安装 openssl 库。 有关更多详细信息，请参阅 [Mac 上的 .NET Core 先决条件](https://docs.microsoft.com/dotnet/core/macos-prerequisites)。
   ```
   # install Homebrew first if you don't have it already
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

   # install latest openssl needed for .NET Core 1.x
   brew update
   brew install openssl
   mkdir -p /usr/local/lib
   ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
   ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
   ```

2. 下载最新的 Azure Machine Learning Workbench 安装程序 **[AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg)**。

   >[!IMPORTANT]
   >将安装程序整个下载到磁盘上，然后在磁盘中启动安装程序。 不要直接从浏览器的下载小组件启动安装程序。

3. 在 Finder 中双击下载的安装程序_AmlWorkbench.dmg_。

4. 遵照屏幕说明完成安装。

   安装程序会下载所有必要的从属组件（例如 Python、Miniconda）和其他相关库。 完成安装所有组件可能需要大约半个小时。 

5. Azure Machine Learning Workbench 现已安装到以下目录中： 

   `/Applications/AmlWorkbench.app`

## <a name="run-azure-machine-learning-workbench-to-log-in-the-first-time"></a>运行 Azure Machine Learning Workbench 以完成首次登录
1. 安装过程完成后，请在安装程序的最后一个屏幕上单击“启动 Workbench”按钮。 如果已关闭安装程序，请在桌面上找到 Machine Learning Workbench 的快捷方式，并通过名为“Azure Machine Learning Workbench”的菜单启动该应用。

2. 使用前面用来预配 Azure 资源的同一帐户登录到 Workbench。 

3. 登录过程成功后，Workbench 会尝试查找前面创建的机器学习试验帐户。 它会搜索凭据有权访问的所有 Azure 订阅。 如果至少找到了一个试验帐户，Workbench 会使用该帐户打开。 然后，列出在该帐户中找到的工作区和项目。 

   >[!TIP]
   > 如果有权访问多个试验帐户，可以通过单击 Workbench 应用左下角的虚拟形象图标，切换到不同的帐户。

有关如何创建用于部署 Web 服务的环境，请参阅[部署环境设置](deployment-setup-configuration.md)。

## <a name="create-a-new-project"></a>创建新项目
1. 启动 Azure ML Workbench 应用并登录。 

2. 单击“文件” --> “新建项目”（或者在“项目”窗格中单击 **+** 符号）。 

3. 填写“项目名称”和“项目目录”字段。 “项目说明”是可选的，但很有帮助。 暂时将“Visualstudio.com GIT 存储库 URL”字段留空。 选择一个工作区，并选择“鸢尾花分类”作为项目模板。

   >[!TIP]
   >（可选）可以在 Git 存储库文本字段中填写托管在 [VSTS（Visual Studio 团队服务）](https://www.visualstudio.com)项目中的 Git 存储库的 URL。 此 Git 存储库必须已存在，并且必须是不带任何主分支的空存储库。 必须对该存储库拥有写访问权限。 现在添加 Git 存储库可在稍后启用漫游和共享方案。 [了解详细信息](using-git-ml-project.md)。

4. 单击“创建”按钮创建项目。 随后会创建并打开一个新项目。 此时，可以浏览项目主页、数据源、Notebook 和源代码文件。 

    >[!TIP]
    >此外，只需配置一个 IDE（集成开发环境）链接即可在 VS Code 或其他编辑器中打开项目，然后打开其中的项目目录。 [了解详细信息](how-to-configure-your-IDE.md)。 

## <a name="run-a-python-script"></a>运行 Python 脚本
让我们在本地计算机上执行脚本。 

1. 每个项目会打开其自身的“项目仪表板”页。 从应用程序顶部附近的运行按钮左侧的命令栏中选择 `local` 作为执行目标，选择 `iris_sklearn.py` 作为要运行的脚本。  示例中包含其他一些文件，稍后可以检查。 

   ![img](media/quickstart-installation/run_control.png)

2. 在“参数”文本字段中输入 `0.01`。 此数字在代码中用于设置正则化率 - 一个用来配置线性回归模型训练方式的值。 

3. 单击“运行”按钮开始在计算机上执行 `iris_sklearn.py`。 

   此代码使用流行 Python [scikit-learn](http://scikit-learn.org/stable/index.html) 库中的[逻辑回归](https://en.wikipedia.org/wiki/logistic_regression)算法生成模型。

4. “作业”面板会从右侧滑出（如果尚未显示），面板中添加了一个  **作业。**`iris_sklearn` 作业开始运行时，其状态会从“正在提交”转换为“正在运行”，几秒钟后转换为“已完成”。 

5. 祝贺。 现已成功在 Azure ML Workbench 中执行了 Python 脚本。

6. 请重复步骤 2-4 多次。 每次使用从 `10` 到 `0.001` 的不同参数值。

## <a name="view-run-history"></a>查看运行历史记录
1. 导航到“运行”视图，单击运行列表中的“iris_sklearn.py”。 此时会打开 `iris_sklearn.py` 的运行历史记录仪表板， 其中显示了在 `iris_sklearn.py` 中执行的每个运行。 

   ![img](media/quickstart-installation/run_view.png)

2. 运行历史记录仪表板还显示最常见的指标、一组默认图形和每个运行的指标列表。 可以单击配置图标或筛选图标，通过排序、筛选和调整配置来自定义此视图。

   ![img](media/quickstart-installation/run_dashboard.png)

3. 单击某个已完成的运行可以看到该特定执行的详细视图，包括其他指标、运行生成的文件，以及其他可能有用的日志。

## <a name="next-steps"></a>后续步骤
现已成功创建 Azure 机器学习试验帐户和 Azure 机器学习模型管理帐户。 已安装 Azure Machine Learning Workbench 桌面应用和命令行接口。 已创建新项目、已通过执行脚本创建模型，并已浏览脚本的运行历史记录。

有关此工作流的更深入体验，包括如何将鸢尾花模型部署为 Web 服务，请遵循完整的“鸢尾花分类教程”，其中包含[数据准备](tutorial-classifying-iris-part-1.md)、[试验](tutorial-classifying-iris-part-2.md)和[模型管理](tutorial-classifying-iris-part-3.md)的详细步骤。 

> [!div class="nextstepaction"]
> [鸢尾花分类教程](tutorial-classifying-iris-part-1.md)

