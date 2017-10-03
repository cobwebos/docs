---
title: "团队数据科学流程小组管理员任务 - Azure | Microsoft Docs"
description: "数据科学团队项目组管理员的任务概述。"
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cd73aed14f672351b72e09d682909a47c63b9026
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---


# <a name="group-manager-tasks"></a>组管理员任务

本主题概述了组管理员应为他/她的数据科学组织完成的任务。 目的在于创建致力于标准化[团队数据科学流程](overview.md) (TDSP) 的团队协作环境。 有关致力于标准化此流程的数据科学团队要处理的人员角色及其相关任务的概述，请参阅[团队数据科学流程角色和任务](roles-tasks.md)。

**组管理员**是企业中整个数据科学部门的经理。 数据科学部门可能有多个团队，每个团队都在不同的业务垂直领域开展多个数据科学项目。 组管理员可以将任务委托给代理，但与角色相关的任务都相同。 如下图所示，有六项主要任务：

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] 我们在以下说明中概述了使用 VSTS 设置 TDSP 组环境所需的步骤。 我们指定了如何使用 VSTS 完成这些任务，因为这是我们在 Microsoft 中实现 TDSP 的方法。 如果你的组使用了其他代码承载平台，组管理员需要完成的任务通常不会发生变化。 但是完成这些任务的方法将会有所不同。

1. 为组设置 **Visual Studio Team Services (VSTS) 服务器**。
2. 在 Visual Studio Team Services 服务器上创建**组团队项目**（适用于 VSTS 用户）
3. 创建 **GroupProjectTemplate** 存储库
4. 创建 **GroupUtilities** 存储库
5. 使用 TDSP 存储库中的内容生成 VSTS 服务器的 **GroupProjectTemplate** 和 **GroupUtilities** 存储库。
6. 为团队成员设置**安全控件**使团队成员能够访问 GroupProjectTemplate 和 GroupUtilities 存储库。

上述每个步骤都进行了详细说明。 但首先，我们要熟悉缩写，并讨论使用存储库的先决条件。

### <a name="abbreviations-for-repositories-and-directories"></a>存储库和目录的缩写

本教程使用存储库和目录的缩写名称。 这些定义可便于在存储库和目录之间执行操作。 以下部分将使用此表示法：

- **G1**：Microsoft TDSP 团队开发并管理的项目模板存储库。
- **G2**：Microsoft TDSP 团队开发并管理的实用程序存储库。
- **R1**：在 VSTS 组服务器上设置的 Git 上的 GroupProjectTemplate 存储库。
- **R2**：在 VSTS 组服务器上设置的 Git 上的 GroupUtilities 存储库。
- **LG1** 和 **LG2**：分别克隆了 G1 和 G2 的计算机上的本地目录。
- **LR1** 和 **LR2**：分别克隆了 R1 和 R2 的计算机上的本地目录。

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>克隆存储库及签入和签出代码的先决条件
 
- 计算机上必须安装 Git。 如果使用的是数据科学虚拟机 (DSVM)，则已预安装 Git，可以继续操作。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。  
- 如果使用的是 **Windows DSVM**，则需要在计算机上安装 [Git 凭据管理器 (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)。 在 README.md 文件中，向下滚动到“下载并安装”部分，然后单击“最新安装程序”。 此步骤会将你转到最新安装程序页。 在此处下载 .exe 安装程序并运行它。 
- 如果使用的是 **Linux DSVM**，则在 DSVM 上创建一个 SSH 公钥，然后将它添加到组 VSTS 服务器。 有关 SSH 的详细信息，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的**创建 SSH 公钥**部分。 


## <a name="1-create-account-on-vsts-server"></a>1.在 VSTS 服务器上创建帐户

VSTS 服务器承载以下存储库：

- **组通用存储库**：可供多个数据科学项目组内的多个团队采用的通用存储库。 例如，*GroupProjectTemplate* 和 *GroupUtilities* 存储库。
- **团队存储库**：适用于组内特定团队的存储库。 这些存储库特定于团队需求，可由该团队执行的多个项目采用，但不足以使数据科学组中的多个团队受用。 
- **项目存储库**：适用于特定项目的存储库。 此类存储库通用程度比较低，可能不足以使一个团队执行的多个项目以及一个数据科学组的多个团队受用。


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>设置 VSTS 服务器并登录到 Microsoft 帐户
    
转到 [Visual Studio Online](https://www.visualstudio.com/)，单击右上角的“登录”，然后登录到 Microsoft 帐户。 
    
![1](./media/group-manager-tasks/login.PNG)

如果没有 Microsoft 帐户，请单击“立即注册”创建一个 Microsoft 帐户，然后使用此帐户登录。 

如果你的组织有 Visual Studio/MSDN 订阅，请单击绿色的“使用你的工作或学校帐户登录”框，并使用与此订阅关联的凭据登录。 
        
![#N/A](./media/group-manager-tasks/signin.PNG)


        
登录后，按下图所示单击右上角的“创建新帐户”：
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
在“创建帐户”向导中使用以下值填写想要创建的 VSTS 服务器的信息： 

- **服务器 URL**：用自己的*服务器名称*替换 *mysamplegroup*。 服务器 URL 应为：*https://\<servername\>.visualstudio.com*。 
- **管理代码使用：**选择 **_Git_**。
- **项目名称：**输入 *GroupCommon*。 
- **组织工作使用：**选择 *Agile*。
- **项目承载位置：**选择地区位置。 在此示例中，我们选择*美国中南部*。 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] 如果在单击“创建新帐户”后看到以下弹出窗口，则需要单击“更改详细信息”以显示逐项列出的所有字段。

![5](./media/group-manager-tasks/create-account-2.png)


单击“继续”。 

## <a name="2-groupcommon-team-project"></a>2.GroupCommon 团队项目

创建 VSTS 服务器后，将打开 **GroupCommon** 页 (*https://\<servername\>.visualstudio.com/GroupCommon*)。
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3.创建 GroupUtilities (R2) 存储库

要在 VSTS 服务器下创建 **GroupUtilities** (R2) 存储库：

- 若要打开“创建新的存储库”向导，请在团队项目的“版本控制”选项卡上单击“新建存储库”。 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- 选择“Git”作为“类型”，并输入“GroupUtilities”作为“名称”，然后单击“创建”。 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
现在，在“版本控制”页的左侧列中会看到两个 Git 存储库 **GroupProjectTemplate** 和 **GroupUtilities**： 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4.创建 GroupProjectTemplate (R1) 存储库

VSTS 组服务器的存储库安装包括两个任务：

- 将默认 **GroupCommon** 存储库重命名为 ***GroupProjectTemplate***。
- 在团队项目 **GroupCommon** 下的 VSTS 服务器上创建 **GroupUtilities** 存储库。 

本部分在命名约定或我们的存储库和目录注解后，对第一个任务进行了说明。 在下一部分的步骤 4 中对第二个任务进行了说明。

### <a name="rename-the-default-groupcommon-repository"></a>重命名默认 GroupCommon 存储库

要将默认 **GroupCommon** 存储库重命名为 *GroupProjectTemplate*（在本教程中称为 **R1**）：
    
- 单击 **GroupCommon** 团队项目页上的“协作代码”。 这会将你转到团队项目 **GroupCommon** 的默认 Git 存储库页。 目前，此 Git 存储库为空。 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- 单击 **GroupCommon** 的 Git 存储库页左上角的“GroupCommon”（在下图中用红色方框突出显示），然后选择“管理存储库”（在下图中用绿色方框突出显示）。 此过程将打开“控制面板”。 
- 选择团队项目的“版本控制”选项卡。 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- 单击左侧面板上 **GroupCommon** 存储库右侧的“...”，然后选择“重命名存储库”。 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- 在弹出的“重命名 GroupCommon 存储库”向导中，在“存储库名称”框中输入 *GroupProjectTemplate*，然后单击“重命名”。 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5.在 VSTS 服务器上设定 R1 和 R2 存储库的种子

在这一过程阶段中，设定在之前部分中设置的 *GroupProjectTemplate* (R1) 和 *GroupUtilities* (R2) 存储库的种子。 使用 Microsoft 为团队数据科学进程管理的 ***ProjectTemplate*** (**G1**) 和 ***Utilities*** (**G2**) 存储库设定这些存储库的种子。 种子设定完成后：

- R1 存储库将具有与 G1 相同的目录和文档模板集
- R2 存储库将包含由 Microsoft 开发的一套数据科学实用程序。

种子设定过程使用本地 DSVM 上的目录作为中间暂存站点。 以下是本部分中的后续步骤：

- G1 和 G2 - 已克隆到 -> LG1 & LG2
- R1 和 R2 - 已克隆到 -> LR1 & LR2
- LG1 和 LG2 - 文件已复制到 - > LR1 和 LR2
- （可选）LR1 和 LR2 的自定义
- LR1 和 LR2 - 内容添加到 -> R1 和 R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>将 G1 和 G2 存储库克隆到本地 DSVM

在此步骤中，将团队数据科学进程 (TDSP) ProjectTemplate 存储库 (G1) 和 Utilities (G2) 作为 LG1 和 LG2 从 TDSP github 存储库克隆到本地 DSVM 中的文件夹：

- 创建一个目录作为根目录来承载所有克隆的存储库。 
    -  在 Windows DSVM 中，创建目录 *C:\GitRepos\TDSPCommon*。 
    -  在 Linux DSVM 中，在主目录中创建目录 *GitRepos\TDSPCommon*。 

- 从 *GitRepos\TDSPCommon* 目录中运行以下命令集。

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- 使用缩写的存储库名称，这是这些脚本实现的功能： 
    - G1 - 已克隆到 -> LG1
    - G2 - 已克隆到 -> LG2
- 克隆完成后，在 **GitRepos\TDSPCommon** 目录下能够看到两个目录 _ProjectTemplate_ 和 _Utilities_。 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>将 R1 和 R2 存储库克隆到本地 DSVM

在此步骤中，将在 DSVM 上 **GitRepos\GroupCommon** 下的本地目录（分别称为 LR1 和 LR2）中克隆 GroupProjectTemplate 存储库 (R1) 和 GroupUtilities 存储库 (R2)。

- 要获取 R1 和 R2 存储库的 URL，请转至 VSTS 上的 **GroupCommon** 主页。 这通常具有 URL *https://\<Your VSTS Server Name\>.visualstudio.com/GroupCommon*。 
- 单击“代码”。 
- 选择“GroupProjectTemplate”和“GroupUtilities”存储库。 复制并保存“克隆URL”元素中的每个 URL（Windows 为 HTTPS；Linux 为 SSH），依次用于以下脚本：  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- 在 Windows 或 Linux DSVM 上更改为 **GitRepos\GroupCommon** 目录，然后运行以下命令集之一以将 R1 和 R2 克隆到该目录。
        
下面是 Windows 和 Linux 脚本：

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] 期望收到 LR1 和 LR2 都为空的警告消息。    

- 使用缩写的存储库名称，这是这些脚本实现的功能： 
    - R1 - 已克隆到 -> LR1
    - R2 - 已克隆到 -> LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>设定 GroupProjectTemplate (LR1) 和 GroupUtilities (LR2) 的种子

接下来，在本地计算机中，将 GitRepos\TDSPCommon 下的 ProjectTemplate 和 Utilities 目录的内容（.git 目录中的元数据除外）复制到 **GitRepos\GroupCommon** 下的 GroupProjectTemplate 和 GroupUtilities 目录。 下面是要在此步骤中完成的两个任务：

- 将 GitRepos\TDSPCommon\ProjectTemplate (**LG1**) 中的文件复制到 GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- 将 GitRepos\TDSPCommon\Utilities (**LG2**) 中的文件复制到 GitRepos\GroupCommon\Utilities (**LR2**)。 

要实现这两个任务，请在 PowerShell 控制台 (Windows) 或 Shell 脚本控制台 (Linux) 中运行以下脚本。 系统会提示输入 LG1、LR1、LG2 和 LR2 的完整路径。 已验证输入的路径。 如果输入的目录不存在，系统会要求重新输入。 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

现在，可以看到目录 LG1 和 LG2 中的文件（.git 目录中的文件除外）已分别复制到 LR1 和 LR2。

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
现在，可以看到两个文件夹中的文件（.git 目录中的文件除外）已分别复制到 GroupProjectTemplate 和 GroupUtilities。
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- 使用缩写的存储库名称，这是这些脚本实现的功能：
    - LG1 - 文件已复制到 -> LR1
    - LG2 - 文件已复制到 -> LR2

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>用于自定义 LR1 和 LR2 的内容的选项
    
如果想要自定义 LR1 和 LR2 的内容以满足组的特定需求，以下是适当的过程阶段。 可以修改模板文档，更改目录结构并添加你的组已开发或对整个组有用的现有实用程序。 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>将 LR1 和 LR2 中的内容添加到组服务器上的 R1 和 R2

现在，需要将 LR1 和 LR2 中的内容添加到存储库 R1 和 R2。 下面是可在 Windows PowerShell 或 Linux 中运行的 git bash 命令。 

从 GitRepos\GroupCommon\GroupProjectTemplate 目录中运行以下命令：

    git status
    git add .
    git commit -m"push from DSVM"
    git push

使用 -m 选项可为 git 提交设置消息。

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

可以看到，在组 VSTS 服务器的 GroupProjectTemplate 存储库中，文件会立即同步。

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

最后，更改为 **GitRepos\GroupCommon\GroupUtilities** 目录并运行同一 git bash 命令集：

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] 如果这是首次提交到 Git 存储库，则需要在运行 `git commit` 命令之前，配置全局参数 *user.name* 和 *user.email*。 运行以下两个命令：
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>如果要提交到多个 Git 存储库，则在提交到每个存储库时使用相同的姓名和电子邮件地址。 使用相同的姓名和电子邮件地址证明了以后构建 PowerBI 仪表板来跟踪多个存储库上的 Git 活动时会很方便。


- 使用缩写的存储库名称，这是这些脚本实现的功能：
    - LR1 - 内容添加到 -> R1
    - LR2 - 内容添加到 -> R2

## <a name="6-add-group-members-to-the-group-server"></a>6.将组成员添加到组服务器

在组 VSTS 服务器的主页上，单击右上角用户名旁边的“齿轮图标”，然后选择“安全性”选项卡。可以通过各种权限将成员添加到组。

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>后续步骤

以下是团队数据科学进程定义的角色和任务的详细说明链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [数据科学团队的项目单独参与者](project-ic-tasks.md)
