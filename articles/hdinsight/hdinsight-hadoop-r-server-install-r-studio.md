---
title: "使用 R Server on HDInsight 安装 RStudio - Azure | Microsoft Docs"
description: "如何使用 R Server on HDInsight 安装 RStudio。"
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 416420d855505508735ebd8526e93efdb230ad53
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>使用 R Server on HDInsight 安装 RStudio

本文介绍如何使用自定义脚本，在群集的边缘节点上安装 [RStudio Server](https://www.rstudio.com/products/rstudio-server/) 的社区（免费）版本。 RStudio Server 为远程客户端提供一种基于浏览器的 IDE，并在 Linux 上广泛使用。 目前有多个集成开发环境 (IDE) 可使用 R，包括：

- Microsoft 的[针对 Visual Studio 的 R 工具](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) 
- [RStudio Server](https://www.rstudio.com/products/rstudio-server/) 
- Walware 的基于 Eclipse 的 [StatET](http://www.walware.de/goto/statet)。

在 HDInsight 群集的边缘节点上安装 RStudio Server 可以提供完整的 IDE 体验，以便通过群集上的 R Server 开发和运行 R 脚本。 与默认使用 R 控制台相比，此配置可以显着提高生产力。

> [!NOTE]
> 本文中所述的过程仅适用于在预配群集时没有选择安装 RStudio Server 社区版这种情况。 如果在预配期间已添加它，则通过以下方式访问它：单击群集的 Azure 门户条目中的“R Server 仪表板”磁贴，然后单击 “R Studio Server” 磁贴。 

如果偏好 RStudio Server 的商用授权 Pro 版本，则必须遵循 [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/) 的安装说明。

> [!NOTE]
> 如果你正使用 HDInsight 群集，并且按照[安装 R 脚本操作](hdinsight-hadoop-r-scripts-linux.md)来安装 R，则由于要求 R Server 要位于 HDInsight 群集之上，此文档中的步骤将失效。
>
> 

## <a name="prerequisites"></a>先决条件

* 装有 R Server 的 Azure HDInsight 群集。 有关说明，请参阅 [Get started with R Server on HDInsight clusters](hdinsight-hadoop-r-server-get-started.md)（HDInsight 群集上的 R Server 入门）。
* SSH 客户端。 对于 Linux 和 Unix 分发版或 Macintosh OS X，操作系统已随附 `ssh` 命令。 对于 Windows，建议使用带有 [OpenSSH 选项](https://www.youtube.com/watch?v=CwYSvvGaiWU)的 [Cygwin](http://www.redhat.com/services/custom/cygwin/)，或使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>使用自定义脚本在群集上安装 RStudio

下面是相关步骤：

1. 识别群集的边缘节点。 对于装有 R Server 的 HDInsight 群集，下面是头节点和边缘节点的命名约定。
   * 头节点 `CLUSTERNAME-ssh.azurehdinsight.net`
   * 边缘节点 `CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. 使用步骤 1 中提供的命名模式通过 SSH 连接到群集的边缘节点。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

3. 连接后，将成为该群集上的 root 用户。 在 SSH 会话中，使用下列命令：

        sudo su -

4. 下载用于安装 RStudio 的自定义脚本。 请使用以下命令：

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. 更改自定义脚本文件的权限，并运行该脚本。 使用以下命令：

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. 如果在创建包含 R Server 的 HDInsight 群集时使用了 SSH 密码，则可以跳过此步骤直接转到下一步。 如果创建群集时使用了 SSH 密钥，则必须为 SSH 用户设置密码。 连接到 RStudio 时需要此密码。 运行以下命令：

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. 当系统提示输入当前 Kerberos 密码时，请按 Enter。  请注意，必须将 `USERNAME` 替换为 HDInsight 群集的 SSH 用户。 如果已成功设置密码，你应会看到如下所示的消息：

        passwd: password updated successfully

    退出 SSH 会话。

8. 将 HDInsight 群集上的 `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` 映射到客户端计算机，创建通往群集的 SSH 隧道。 必须在打开新浏览器会话之前创建 SSH 隧道。

   * 在装有 [Cygwin](http://www.redhat.com/services/custom/cygwin/) 的 Linux 客户端或 Windows 客户端上，打开终端会话并使用以下命令：

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       将 USERNAME 替换为 HDInsight 群集的 SSH 用户，并将 CLUSTERNAME 替换为 HDInsight 群集的名称。
       还可通过添加 `-i id_rsa_key` 使用 SSH 密钥，而无需密码。        
   * 如果使用装有 PuTTY 的 Windows 客户端

     1. 打开 PuTTY 并输入连接信息。
     2. 在对话框左侧的“类别”部分中，依次展开“连接”和“SSH”，并选择“隧道”。
     3. 提供以下有关“用于控制 SSH 端口转发的选项”窗体的信息：

        * **源端口** - 客户端上要转发的端口。 例如 **8787**。
        * **目标** - 必须映射到本地客户端计算机的目标。 例如 **localhost:8787**。

            ![创建 SSH 隧道](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "创建 SSH 隧道")

     4. 单击“添加”以添加设置，并单击“打开”以打开 SSH 连接。
     5. 在系统提示时，登录到服务器以建立 SSH 会话并启用隧道。

9. 打开 Web 浏览器，并根据你为隧道输入的端口输入以下 URL：

        http://localhost:8787/ 

10. 系统会提示你输入的 SSH 用户名和密码以连接到群集。 如果在创建群集时使用了 SSH 密钥，则必须输入步骤 5 中所创建的密码。

    ![连接到 R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "创建 SSH 隧道")

11. 若要测试 RStudio 安装是否成功，你可以运行在群集执行基于 R 的 MapReduce 和 Spark 作业测试脚本。 要下载测试脚本并在 RStudio 中运行，请返回 SSH 控制台，然后输入下列命令：

    *    如果你创建了包含 R 的 Hadoop 群集，请使用此命令：

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    如果你创建了包含 R 的 Spark 群集，请使用此命令：

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. 在 RStudio，你将看到你下载的测试脚本。 双击该文件以打开它，选择该文件的内容，然后单击**运行**。 “控制台”窗格中应会显示输出：

   ![测试安装](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "测试安装")

另一种方法是键入 `source(testhdi.r)` 或 `source(testhdi_spark.r)` 来执行脚本。

## <a name="see-also"></a>另请参阅

* [适用于 HDInsight 群集上的 R Server 的计算上下文选项](hdinsight-hadoop-r-server-compute-contexts.md)
* [适用于 R Server on HDInsight 的 Azure 存储选项](hdinsight-hadoop-r-server-storage.md)

