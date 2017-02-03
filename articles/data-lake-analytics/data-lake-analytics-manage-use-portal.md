---
title: "使用 Azure 门户管理 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何管理 Data Lake Analytics 帐户、数据源、用户和作业。"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 86711ba89442c3569b570bbf3ea2d1661a469011
ms.openlocfilehash: 34be29749075b9953950ffd64d2526430307d33e


---
# <a name="manage-azure-data-lake-analytics-using-azure-portal"></a>使用 Azure 门户管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure 门户管理 Azure Data Lake Analytics 帐户、帐户数据源、用户和作业。 若要查看使用其他工具的管理主题，请单击页面顶部的选项卡选择器。

**先决条件**

开始学习本教程之前，必须做好以下准备：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>管理帐户
运行任何 Data Lake Analytics 作业之前，必须具有 Data Lake Analytics 帐户。 与 Azure HDInsight 不同，只需在运行作业时为 Data Lake Analytics 帐户付费。  只需在其运行作业时付费。  有关详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。  

**创建 Data Lake Analytics 帐户**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 依次单击“新建”、“智能 + 分析”、“Data Lake Analytics”。
3. 键入或选择以下值：
   
    ![Azure Data Lake Analytics 门户边栏选项卡](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)
   
   * **名称**：Data Lake Analytics 帐户的名称。
   * **订阅**：选择用于 Analytics 帐户的 Azure 订阅。
   * **资源组**。 选择现有的 Azure 资源组或创建新的资源组。 那么，你可以使用 Azure 资源管理器以组的方式处理应用程序中的资源。 有关详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。 
   * **位置**。 为 Data Lake Analytics 帐户选择 Azure 数据中心。 
   * **Data Lake Store**：每个 Data Lake Analytics 帐户都有一个从属 Data Lake Store 帐户。 Data Lake Analytics 帐户和从属 Data Lake Store 帐户必须位于同一个 Azure 数据中心。 按照说明创建新的 Data Lake Store 帐户，或选择现有帐户。
4. 单击“创建” 。 随后将转到门户主页屏幕。 此时，一个新磁贴已添加到启动板，其中的标签显示“正在部署 Azure Data Lake Analytics”。 需要花一些时间来创建一个 Data Lake Analytics 帐户。 帐户创建后，门户将在新的边栏选项卡上打开该帐户。

创建 Data Lake Analytics 帐户后，可以添加其他 Data Lake Store 帐户和 Azure 存储帐户。 有关说明，请参阅 [Manage Data lake Analytics account data sources](data-lake-analytics-manage-use-portal.md#manage-account-data-sources)（管理 Data Lake Analytics 帐户数据源）。

<a name="access-adla-account"></a>**访问/打开 Data Lake Analytics 帐户**

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击左侧菜单上的“Data Lake Analytics”。  如果看不到，请单击“更多服务”，然后单击“智能 + 分析”下的“Data Lake Analytics”。
3. 单击想要访问的 Data Lake Analytics 帐户。 帐户将在新的边栏选项卡中打开。

**删除 Data Lake Analytics 帐户**

1. 打开想要删除的 Data Lake Analytics 帐户。 有关说明，请参阅[访问 Data Lake Analytics 帐户](#access-adla-account)。
2. 单击边栏选项卡顶部按钮菜单中的“删除”。
3. 键入帐户名称，然后单击“删除” 。

删除 Data Lake Analytics 帐户不会删除从属 Data Lake Store 帐户。 有关删除 Data Lake Storage 帐户的说明，请参阅[删除 Data Lake Store 帐户](../data-lake-store/data-lake-store-get-started-portal.md#delete-azure-data-lake-store-account)。

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>管理帐户数据源
Data Lake Analytics 当前支持以下数据源：

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure 存储](../storage/storage-introduction.md)

创建 Data Lake Analytics 帐户时，必须指定一个 Azure Data Lake Store 帐户作为默认存储帐户。 默认 Data Lake Store 帐户用于存储作业元数据和作业审核日志。 创建 Data Lake Analytics 帐户后，可添加其他 Data Lake Store 帐户和/或 Azure 存储帐户。 

<a name="default-adl-account"></a>**查找默认 Data Lake Storage 帐户**

* 打开想要管理的 Data Lake Analytics 帐户。 有关说明，请参阅[访问 Data Lake Analytics 帐户](#access-adla-account)。 默认 Data Lake Store 在“基本”中显示：
  
    ![Azure Data Lake Analytics 添加数据源](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-default-adl-storage-account.png)

**添加更多数据源**

1. 打开想要管理的 Data Lake Analytics 帐户。 有关说明，请参阅[访问 Data Lake Analytics 帐户](#access-adla-account)。
2. 单击“设置”，然后单击“数据源”。 此时应看到列出的默认 Data Lake Store 帐户。 
3. 单击“添加数据源”。
   
    ![Azure Data Lake Analytics 添加数据源](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-add-data-source.png)
   
    若要添加 Azure Data Lake Store 帐户，需要帐户名称和此帐户的访问权限才能对其进行查询。
    若要添加 Azure Blob 存储，需要存储帐户和帐户密钥，可通过导航到门户中的存储帐户找到。

<a name="explore-data-sources"></a>**浏览数据源**    

1. 打开想要管理的 Analytics 帐户。 有关说明，请参阅[访问 Data Lake Analytics 帐户](#access-adla-account)。
2. 单击“设置”，然后单击“数据资源管理器”。 
   
    ![Azure Data Lake Analytics 数据资源管理器](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
3. 单击 Data Lake Store 帐户以打开帐户。
   
    ![Azure Data Lake Analytics 数据资源管理器存储帐户](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
   
    对于每个 Data Lake Store 帐户，均可以
   
   * **新文件夹**：添加新文件夹。
   * **上传**：将文件从工作站上传到存储帐户。
   * **访问**：配置访问权限。
   * **重命名文件夹**：重命名文件夹。
   * **文件夹属性**：显示文件或文件夹属性，如 WASB 路径、WEBHDFS 路径以及上次修改时间等。
   * **删除文件夹**：删除文件夹。

<a name="upload-data-to-adls"></a> **将文件上传到 Data Lake Store 帐户**

1. 在门户中，单击左侧菜单中的“浏览” ，然后单击“Data Lake Store”。
2. 单击想要向其中上传数据的 Data Lake Store 帐户。 若要查找默认 Data Lake Storage 帐户，请参阅[此处](#default-adl-account)。
3. 单击顶部菜单中的“数据资源管理器”。
4. 单击“新目录”创建一个新的文件夹，或单击文件夹名称更改文件夹。
5. 单击顶部菜单中的“上传”以上传文件。

<a name="upload-data-to-wasb"></a>**将文件上传到 Azure Blob 存储帐户**

请参阅[在 HDInsight 中上传 Hadoop 作业的数据](../hdinsight/hdinsight-upload-data.md)。  此信息适用于 Data Lake Analytics。

## <a name="manage-users"></a>管理用户
Data Lake Analytics 通过 Azure Active Directory 使用基于角色的访问控制。 创建 Data Lake Analytics 帐户时，该帐户中将添加“订阅管理员”角色。 可添加具有以下角色的其他用户和安全组：

| 角色 | 说明 |
| --- | --- |
| 所有者 |允许管理一切，包括对资源的访问权限。 |
| 参与者 |访问门户；提交和监视作业。 若要提交作业，参与者需要对 Data Lake Store 帐户的读取或写入权限。 |
| DataLakeAnalyticsDeveloper |提交、监视和取消作业。  这些用户只能取消自己的作业。 他们不能管理自己的帐户，例如，添加用户、更改权限或删除帐户。 若要运行作业，他们需要对 Data Lake Store 帐户的读取或写入权限 |
| 读取器 |允许查看所有内容，但不能进行任何更改。 |
| DevTest 实验室用户 |允许查看一切内容，并可连接、启动、重启和关闭虚拟机。 |
| 用户访问管理员 |允许你管理用户对 Azure 资源的访问权限。 |

有关创建 Azure Active Directory 用户和安全组的信息，请参阅[什么是 Azure Active Directory](../active-directory/active-directory-whatis.md)。

**将用户或安全组添加到 Data Lake Analytics 帐户**

1. 打开想要管理的 Analytics 帐户。 有关说明，请参阅[访问 Data Lake Analytics 帐户](#access-adla-account)。
2. 单击“设置”，然后单击“用户”。 还可以单击“概要”标题栏上的“访问”，如以下屏幕截图所示：
   
    ![Azure Data Lake Analytics 帐户添加用户](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-access-button.png)
3. 在“用户”边栏选项卡中，单击“添加”。
4. 选择一个角色并添加用户，然后单击“确定”。

>[!NOTE]
>如果此用户或安全组需要提交作业，也需要为其授予 Data Lake Store 上的权限。 有关详细信息，请参阅[保护 Data Lake Store 中的数据](../data-lake-store/data-lake-store-secure-data.md)
>

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>管理作业
必须拥有 Data Lake Analytics 帐户，才能运行任何 U-SQL 作业。  有关详细信息，请参阅[管理 Data Lake Analytics 帐户](#manage-data-lake-analytics-accounts)。

<a name="create-job"></a>**创建作业**

1. 打开想要管理的 Analytics 帐户。 有关说明，请参阅[访问 Data Lake Analytics 帐户](#access-adla-account)。
2. 单击“新建作业”。
   
    ![创建 Azure Data Lake Analytics U-SQL 作业](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-create-job-button.png)
   
    应看到新的边栏选项卡，类似于：
   
    ![创建 Azure Data Lake Analytics U-SQL 作业](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-new-job.png)
   
    可为每个作业配置

    |Name|说明|
    |----|-----------|
    |作业名称|输入作业的名称。|
    |Priority|数字越小优先级越高。 如果队列有两个作业，将首先运行优先级低的作业|
    |并行度 |可同时进行的计算进程的最大数量。 增加此数量可以提高性能，但也会增加成本。|
    |脚本|输入作业的 U-SQL 脚本。|

    通过使用相同的接口，还可以浏览链接数据源，并将其他文件添加到链接的数据源。 
1. 若要提交作业，请单击“提交作业”。

**提交作业**

请参阅[创建 Data Lake Analytics 作业](#create-job)。

<a name="monitor-jobs"></a>**监视作业**

1. 打开想要管理的 Analytics 帐户。 有关说明，请参阅[访问 Data Lake Analytics 帐户](#access-adla-account)。 作业管理面板显示基本作业信息：
   
    ![管理 Azure Data Lake Analytics U-SQL 作业](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs.png)
2. 单击“作业管理”，如上一张屏幕截图中所示。
   
    ![管理 Azure Data Lake Analytics U-SQL 作业](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs-details.png)
3. 单击列表中的作业。 或单击“筛选器”查找作业：
   
    ![筛选 Azure Data Lake Analytics U-SQL 作业](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-filter-jobs.png)
   
    可按“时间范围”、“作业名称”和“作者”筛选作业。
4. 如果想要重新提交作业，请单击“重新提交”。

**重新提交作业**

请参阅[监视 Data Lake Analytics 作业](#monitor-jobs)。

## <a name="monitor-account-usage"></a>监视帐户使用情况
**监视帐户使用情况**

1. 打开想要管理的 Analytics 帐户。 有关说明，请参阅[访问 Data Lake Analytics 帐户](#access-adla-account)。 “使用情况”面板显示使用情况：
   
    ![监视 Azure Data Lake Analytics 使用情况](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-monitor-usage.png)
2. 双击此窗格可查看更多详细信息。

## <a name="view-u-sql-catalog"></a>查看 U-SQL 目录
可使用 [U-SQL 目录](data-lake-analytics-use-u-sql-catalog.md)组织数据和代码，以便通过 U-SQL 脚本共享数据和代码。 目录启用 Azure Data Lake 中数据的最高可能性能。 可从 Azure 门户查看 U-SQL 目录。

**浏览 U-SQL 目录**

1. 打开想要管理的 Analytics 帐户。 有关说明，请参阅[访问 Data Lake Analytics 帐户](#access-adla-account)。
2. 单击顶部菜单中的“数据资源管理器”。
3. 依次展开“目录”、“主要”、“表格”或“表值函数”**或“程序集”****。 以下屏幕截图显示一个表值函数。
   
    ![Azure Data Lake Analytics 数据资源管理器存储帐户](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-catalog.png)

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-azure-resource-manager-groups"></a>使用 Azure Resource Manager 组
应用程序通常由许多组件构成，例如 Web 应用、数据库、数据库服务器、存储和第三方服务。 可使用 Azure Resource Manager 以组（称为 Azure 资源组）的方式处理应用程序中的资源。 可以通过单个协调的操作为应用程序部署、更新、监视或删除所有资源。 可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 你可以通过查看整个组的累积费用，明确了解组织的帐单开支。 有关详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。 

Data Lake Analytics 服务可包括以下组件：

* Azure Data Lake Analytics 帐户
* 所需的默认 Azure Data Lake Store 帐户
* 其他 Azure Data Lake Store 帐户
* 其他 Azure 存储帐户

可在一个资源管理组下创建所有这些组件，使其更易于管理。

![Azure Data Lake Analytics 帐户和存储](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics 帐户和从属存储帐户必须位于同一个 Azure 数据中心。
但资源管理组可以位于不同的数据中心。  

## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [Get started with Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)




<!--HONumber=Dec16_HO2-->


