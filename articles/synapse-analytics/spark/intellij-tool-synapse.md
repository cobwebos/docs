---
title: 教程 - Azure Toolkit for IntelliJ（Spark 应用程序）
description: 教程 - 使用 Azure Toolkit for IntelliJ 开发以 Scala 编写的 Spark 应用程序，并将其提交到 Apache Spark 池（预览版）。
services: synapse-analytics
author: v-jiche
ms.author: v-jiche
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: fc7551c081d14a871c8ee96610ca7190f629901d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790960"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-spark-pools-preview"></a>教程：使用 Azure Toolkit for IntelliJ 为 Spark 池（预览版）创建 Apache Spark 应用程序

本教程演示如何使用 Azure Toolkit for IntelliJ 插件开发以 [Scala](https://www.scala-lang.org/) 编写的 Apache Spark 应用程序，并将其直接从 IntelliJ 集成开发环境 (IDE) 提交到 Spark 池（预览版）。 可按多种方式使用该插件：

- 在 Spark 池中开发并提交 Scala Spark 应用程序。
- 访问 Spark 池资源。
- 本地开发和运行 Scala Spark 应用程序。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
>
> - 使用 Azure Toolkit for IntelliJ 插件
> - 开发 Apache Spark 应用程序
> - 将应用程序提交到 Spark 池

## <a name="prerequisites"></a>先决条件

- [IntelliJ IDEA 社区版](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC)。
- Azure 工具包插件 3.27.0-2019.2 – 从 [IntelliJ 插件存储库](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)安装
- [JDK（版本 1.8）](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。
- Scala 插件 – 从 [IntelliJ 插件存储库](/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea)安装。
- 此先决条件仅适用于 Windows 用户。

  在 Windows 计算机上运行本地 Spark Scala 应用程序时，可能会发生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的异常。 发生此异常的原因是 Windows 中缺少 WinUtils.exe。
  若要解决此错误，请将 [WinUtils 可执行文件](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)下载到某个位置（例如 C:\WinUtils\bin）。  然后，必须添加环境变量 HADOOP_HOME  ，并将其值设置为 C:\WinUtils  。

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>为 Spark 池创建 Spark Scala 应用程序

1. 启动 IntelliJ IDEA，选择“创建新项目”打开“新建项目”窗口。  
2. 从左侧窗格中选择“Apache Spark/HDInsight”  。
3. 在主窗口中选择“Spark 项目和示例(Scala)”。 
4. 在“生成工具”下拉列表中选择以下类型之一： 

   - **Maven**：支持 Scala 项目创建向导。
   - **SBT**：用于管理依赖项和生成 Scala 项目。

    ![IntelliJ IDEA“新建项目”对话框](./media/intellij-tool-synapse/create-synapse-application01.png)

5. 选择“**下一页**”。
6. 在“新建项目”窗口中提供以下信息： 

    | properties | 说明 |
    | ----- | ----- |
    |项目名称| 输入名称。 本教程使用 `myApp`。|
    |项目位置&nbsp;| 输入所需的项目保存位置。|
    |项目 SDK| 首次使用 IDEA 时，此字段可能是空白的。 选择“新建...”并导航到 JDK。 |
    |Spark 版本|创建向导集成了适当版本的 Spark SDK 和 Scala SDK。 Synapse 仅支持 Spark 2.4.0。 |

    ![选择 Apache Spark SDK](./media/intellij-tool-synapse/create-synapse-application02.png)

7. 选择“完成”  。 可能需要在几分钟后才会显示该项目。
8. Spark 项目自动为你创建项目。 若要查看项目，请执行以下操作：

   a. 在菜单栏中，导航到“文件” > “项目结构...”。  

   b. 在“项目结构”窗口中选择“项目”。  

   c. 查看项目后选择“取消”。 

    ![对话框中的项目信息](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. 通过“myApp” > “src” > “main” > “scala”> “sample”> “LogQuery”找到“LogQuery”。        本教程使用 LogQuery 来运行。 

   ![用于在项目中创建 Scala 类的命令](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>连接到 Spark 池

登录到 Azure 订阅以连接到 Spark 池。

### <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅

1. 在菜单栏中，导航到“视图” > “工具窗口” > “Azure 资源管理器”。   

   ![IntelliJ IDEA 显示 Azure 资源管理器](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. 在 Azure 资源管理器中右键单击“Azure”节点，然后选择“登录”。  

   ![在 IntelliJ IDEA 资源管理器中右键单击“Azure”](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. 在“Azure 登录”  对话框中，依次选择“设备登录”、“登录”   。

    ![IntelliJ IDEA 的 Azure 登录](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. 在“Azure 设备登录”对话框中单击“复制并打开”。  

   ![IntelliJ IDEA Azure 设备登录](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. 在浏览器界面中粘贴代码，然后单击“下一步”  。

   ![Microsoft 的针对 HDI 的“输入代码”对话框](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. 输入 Azure 凭据，然后关闭浏览器。

   ![Microsoft 的针对 HDI 的“输入电子邮件”对话框](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. 登录之后，“选择订阅”  对话框会列出与凭据关联的所有 Azure 订阅。 选择你的订阅，然后单击“选择”。 

    ![“选择订阅”对话框](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. 在“Azure 资源管理器”中，展开“Synapse 上的 Apache Spark”以查看订阅中的工作区。  

    ![IntelliJ IDEA Azure 资源管理器主视图](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. 若要查看 Spark 池，可以进一步展开某个工作区。

    ![Azure 资源管理器存储帐户](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>在 Spark 池中远程运行 Spark Scala 应用程序

创建 Scala 应用程序后，可以远程运行该应用程序。

1. 单击相应的图标，打开“运行/调试配置”窗口。 

    ![“将 Spark 应用程序提交到 HDInsight”命令](./media/intellij-tool-synapse/open-configuration-window.png)

2. 在“运行/调试配置”对话框窗口中单击“+”，然后选择“Synapse 上的 Apache Spark”。   

    ![“将 Spark 应用程序提交到 HDInsight”命令](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. 在“运行/调试配置”窗口中提供以下值，然后选择“确定”：  

    |properties |值 |
    |----|----|
    |Spark 池|选择要在其上运行应用程序的 Spark 池。|
    |选择要提交的项目|保留默认设置。|
    |主类名|默认值是所选文件中的主类。 可以通过选择省略号图标 (...) 并选择另一个类来更改类。 |
    |作业配置|可以更改默认的键和值。 有关详细信息，请参阅 [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html)。|
    |命令行参数|如果需要，可为 main 类输入参数并以空格分隔。|
    |引用的 Jar 和引用的文件|可以输入引用的 Jar 和引用的文件的路径（如果有）。 还可以在 Azure 虚拟文件系统中浏览文件，但目前仅支持 ADLS 第 2 代群集。 更多相关信息：[Apache Spark 配置](https://spark.apache.org/docs/latest/configuration.html#runtime-environment)和[如何将资源上传到群集](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。|
    |作业上传存储|展开以显示其他选项。|
    |存储类型|从下拉列表中选择“使用 Azure Blob 上传”。 |
    |存储帐户|输入存储帐户。|
    |存储密钥|输入存储密钥。|
    |存储容器|输入“存储帐户”和“存储密钥”后，从下拉列表中选择你的存储容器。  |

    ![“Spark 提交”对话框](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. 单击“SparkJobRun”图标将项目提交到选定的 Spark 池。  底部的“群集中的远程 Spark 任务”选项卡显示作业执行进度  。 通过单击红色按钮，即可停止应用程序。

    ![Apache Spark 提交窗口](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![“Spark 提交”对话框](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>本地运行/调试 Apache Spark 应用程序

可以按照以下说明为 Apache Spark 作业设置本地运行和本地调试。

### <a name="scenario-1-do-local-run"></a>应用场景 1：执行本地运行

1. 打开“运行/调试配置”对话框，选择加号图标 (+)。   然后选择“Synapse 上的 Apache Spark”选项。  输入“名称”、“Main 类名”的信息并保存。  

    ![Intellij - 运行/调试配置 - 本地运行](./media/intellij-tool-synapse/local-run-synapse.png)

    - 环境变量和 WinUtils.exe 位置仅适用于 Windows 用户。
    - 环境变量：如果以前已设置系统环境变量，系统可以自动检测到该变量，无需手动添加。
    - [WinUtils.exe 位置](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)：可以通过单击右侧的文件夹图标来指定 WinUtils 位置。

2. 然后单击本地播放按钮。

    ![Intellij - 运行/调试配置 - 本地运行](./media/intellij-tool-synapse/local-run-synapse01.png)

3. 本地运行完成后，如果脚本包含输出，则可在“data” > “default”中检查输出文件。  

    ![Intellij 项目本地运行结果](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>应用场景 2：执行本地调试

1. 打开“LogQuery”脚本，设置断点。 
2. 单击“本地调试”图标，执行本地调试。 

    ![Intellij 项目本地运行结果](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>访问和管理 Synapse 工作区

可以在 Azure Toolkit for IntelliJ 内部的 Azure 资源管理器中执行不同的操作。 在菜单栏中，导航到“视图” > “工具窗口” > “Azure 资源管理器”。   

### <a name="launch-workspace"></a>启动工作区

1. 在 Azure 资源管理器中，导航到“Synapse 上的 Apache Spark”并将其展开。 

    ![IntelliJ IDEA Azure 资源管理器主视图](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. 右键单击某个工作区并选择“启动工作区”，随即会打开网站。 

    ![Spark 作业视图应用程序详细信息](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Spark 作业视图应用程序详细信息](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark 控制台

可以运行 Spark 本地控制台 (Scala) 或运行 Spark Livy 交互式会话控制台 (Scala)。

### <a name="spark-local-console-scala"></a>Spark 本地控制台 (Scala)

确保符合 WINUTILS.EXE 先决条件。

1. 从菜单栏中，导航到“运行” > “编辑配置...”   。
2. 在“运行/调试配置”窗口中的左侧窗格内，导航到“Synapse 上的 Apache Spark” > “[Synapse 上的 Spark] myApp”。   
3. 在主窗口中，选择“在本地运行”选项卡  。
4. 提供以下值，然后选择“确定”  ：

    |properties |值 |
    |----|----|
    |环境变量|请确认 HADOOP_HOME 的值是否正确。|
    |WINUTILS.exe 位置|请确保路径正确。|

    ![本地控制台设置配置](./media/intellij-tool-synapse/local-console-synapse01.png)

5. 从项目中，导航到“myApp” > “src” > “main” > “scala” > “myApp”      。
6. 在菜单栏中，导航到“工具” > “Spark 控制台” > “运行 Spark 本地控制台(Scala)”。   
7. 然后，系统可能会显示两个对话框，询问你是否要自动修复依赖项。 如果出现对话框，请选择“自动修复”  。

    ![IntelliJ IDEA Spark 自动修复对话框 1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark 自动修复对话框 2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. 控制台应如下图所示。 在“控制台”窗口中键入 `sc.appName`，然后按 Ctrl+Enter。 系统将显示结果。 可以单击红色按钮来停止本地控制台。

    ![IntelliJ IDEA 本地控制台结果](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark Livy 交互式会话控制台 (Scala)

它仅在 IntelliJ 2018.2 和 2018.3 上受支持。

1. 从菜单栏中，导航到“运行” > “编辑配置...”   。

2. 在“运行/调试配置”窗口中的左侧窗格内，导航到“Synapse 上的 Apache Spark” > “[Synapse 上的 Spark] myApp”。   

3. 在主窗口中，选择“在群集中远程运行”选项卡  。

4. 提供以下值，然后选择“确定”  ：

    |properties |值 |
    |----|----|
    |Spark 池|选择要在其上运行应用程序的 Spark 池。|
    ||

    ![交互式控制台设置配置](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. 从项目中，导航到“myApp” > “src” > “main” > “scala” > “myApp”      。

6. 在菜单栏中，导航到“工具” > “Spark 控制台” > “运行 Spark Livy 交互式会话控制台(Scala)”。   
7. 控制台应如下图所示。 在“控制台”窗口中键入 `sc.appName`，然后按 Ctrl+Enter。 系统将显示结果。 可以单击红色按钮来停止本地控制台。

    ![IntelliJ IDEA 交互式控制台结果](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>向 Spark 控制台发送所选内容

可以通过将一些代码发送到本地控制台或 Livy 交互式会话控制台 (Scala) 来方便地预测脚本结果。 可以在 Scala 文件中突出显示一些代码，然后右键单击“向 Spark 控制台发送所选内容”。  所选代码会发送到控制台并在其中执行。 结果将显示在控制台中的代码后面。 控制台将检查是否存在错误。

   ![向 Spark 控制台发送所选内容](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](../overview-what-is.md)
- [为 Azure Synapse Analytics 工作区创建新的 Apache Spark 池](../../synapse-analytics/quickstart-create-apache-spark-pool.md)
