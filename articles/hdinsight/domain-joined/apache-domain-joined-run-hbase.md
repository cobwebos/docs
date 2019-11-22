---
title: Apache HBase 和企业安全性套餐 - Azure HDInsight
description: 教程 - 了解如何使用企业安全性套餐为 Azure HDInsight 中的 HBase 配置 Apache Ranger 策略。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 89e9faeb3c67d0fd0c57adea3a3f69ec5438e3a0
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044649"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>教程：使用企业安全性套餐在 HDInsight 中配置 Apache HBase 策略

了解如何为企业安全性套餐 (ESP) Apache HBase 群集配置 Apache Ranger 策略。 将 ESP 群集连接到域，可允许用户使用域凭据进行身份验证。 本教程将创建两个 Ranger 策略来限制对 HBase 表中不同列系列的访问。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建域用户
> * 创建 Ranger 策略
> * 在 HBase 群集中创建表
> * 测试 Ranger 策略

## <a name="before-you-begin"></a>开始之前

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。

* 登录到 [Azure 门户](https://portal.azure.com/)。

* [使用企业安全性套餐创建 HDInsight HBase 群集](apache-domain-joined-configure-using-azure-adds.md)。

## <a name="connect-to-apache-ranger-admin-ui"></a>连接到 Apache Ranger 管理 UI

1. 在浏览器中，使用 URL `https://<ClusterName>.azurehdinsight.net/Ranger/` 连接到 Ranger 管理用户界面。 请记住将 `<ClusterName>` 更改为 HBase 群集的名称。

    > [!NOTE]  
    > Ranger 凭据与 Hadoop 集群凭据不同。 若要防止浏览器使用缓存的 Hadoop 凭据，请使用新的 InPrivate 浏览器窗口连接到 Ranger 管理 UI。

2. 使用 Azure Active Directory (AD) 管理员凭据登录。 Azure AD 管理员凭据与 HDInsight 群集凭据或 Linux HDInsight 节点 SSH 凭据不同。

## <a name="create-domain-users"></a>创建域用户

访问[使用企业安全性套餐创建 HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)，以了解如何创建 **sales_user1** 和 **marketing_user1** 域用户。 在生产方案中，域用户来自你的 Active Directory·租户。

## <a name="create-hbase-tables-and-import-sample-data"></a>创建 HBase 表并导入示例数据

可以使用 SSH 连接到 HBase 群集，并使用 [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) 来创建 HBase 表以及插入和查询数据。 有关详细信息，请参阅 [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

### <a name="to-use-the-hbase-shell"></a>使用 HBase shell

1. 从 SSH 运行以下 HBase 命令：
   
    ```bash
    hbase shell
    ```

2. 创建包含列系列 `Name` 和 `Contact` 的 HBase 表 `Customers`。

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. 插入一些数据：
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. 查看表的内容：
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![HDInsight Hadoop HBase shell 输出](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>创建 Ranger 策略

为 **sales_user1** 和 **marketing_user1** 创建 Ranger 策略。

1. 打开“Ranger 管理 UI”  。 单击“HBase”下的“\<群集名称 >_hbase”。  

   ![HDInsight Apache Ranger 管理 UI](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. “策略列表”屏幕将显示针对此群集创建的所有 Ranger 策略。  可以列出一个预先配置的策略。 单击“添加新策略”。 

    ![Apache Ranger HBase 策略列表](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. 在“创建策略”屏幕上输入以下值  ：

   |**设置**  |**建议的值**  |
   |---------|---------|
   |策略名称  |  sales_customers_name_contact   |
   |HBase 表   |  客户 |
   |HBase 列系列   |  名称、联系人 |
   |HBase 列   |  * |
   |选择组  | |
   |选择用户  | sales_user1 |
   |权限  | 读取 |

   主题名称中可以包含以下通配符：

   * `*` 表示字符出现零次或多次。
   * `?` 表示单个字符。

   ![Apache Ranger 策略创建 sales](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >如果“选择用户”  中未自动填充域用户，请等待片刻时间让 Ranger 与 Azure AD 同步。

4. 单击“添加”保存策略。 

5. 单击“添加新策略”  ，然后输入以下值：

   |**设置**  |**建议的值**  |
   |---------|---------|
   |策略名称  |  marketing_customers_contact   |
   |HBase 表   |  客户 |
   |HBase 列系列   |  联系人 |
   |HBase 列   |  * |
   |选择组  | |
   |选择用户  | marketing_user1 |
   |权限  | 读取 |

   ![Apache Ranger 策略创建 marketing](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. 单击“添加”保存策略。 

## <a name="test-the-ranger-policies"></a>测试 Ranger 策略

根据配置的 Ranger 策略，**sales_user1** 可以查看 `Name` 和 `Contact` 列系列中的列的所有数据。 **marketing_user1** 只能查看 `Contact` 列系列中的数据。

### <a name="access-data-as-sales_user1"></a>以 sales_user1 的身份访问数据

1. 打开与群集的新 SSH 连接。 使用以下命令登录到群集：

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. 使用 kinit 命令更改为所需用户的上下文。

   ```bash
   kinit sales_user1
   ```

2. 打开 HBase shell，并扫描表 `Customers`。

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. 请注意，sales 用户可以查看 `Customers` 表的所有列，包括 `Name` 列系列中的两个列，以及 `Contact` 列系列中的五个列。

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketing_user1"></a>以 marketing_user1 的身份访问数据

1. 打开与群集的新 SSH 连接。 使用以下命令以 **marketing_user1** 身份登录：

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. 使用 kinit 命令更改为所需用户的上下文

   ```bash
   kinit marketing_user1
   ```

1. 打开 HBase shell，并扫描表 `Customers`：

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. 请注意，marketing 用户只能查看 `Contact` 列系列的五个列。

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

1. 从 Ranger UI 查看审核访问事件。

   ![HDInsight Ranger UI 策略审核](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请使用以下步骤删除创建的 HBase 群集：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在顶部的“搜索”框中，键入 **HDInsight**。  
1. 选择“服务”下的“HDInsight 群集”   。
1. 在显示的 HDInsight 群集列表中，单击为本教程创建的群集旁边的“...”。  
1. 单击“删除”  。 单击 **“是”** 。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Apache HBase 入门](../hbase/apache-hbase-tutorial-get-started-linux.md)
