---
title: "移动数据 - 数据管理网关 | Microsoft Docs"
description: "设置数据网关，在本地和云之间移动数据。 使用 Azure 数据工厂中的数据管理网关移动数据。"
keywords: "数据网关, 数据集成, 移动数据, 网关凭据"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: abnarain
ms.translationtype: Human Translation
ms.sourcegitcommit: e1d44f85b36d08944351a79d7a4b39cc8de61201
ms.openlocfilehash: 13044cc92a1577185b2aebc3a0ff8be0ec5eca60
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>使用数据管理网关在本地源与云之间移动数据
本文概述了如何使用数据工厂将本地数据存储与云数据存储之间的数据进行集成。 本文内容基于[数据移动活动](data-factory-data-movement-activities.md)一文和其他数据工厂核心概念文章：[数据集](data-factory-create-datasets.md)和[管道](data-factory-create-pipelines.md)。

## <a name="data-management-gateway"></a>数据管理网关
必须在本地计算机上安装数据管理网关，以便将数据移入/移出本地数据存储。 可在与数据存储相同的计算机上安装网关，也可在不同的计算机上安装网关，只要网关能连接数据存储即可。

> [!IMPORTANT]
> 有关数据管理网关的详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)一文。   
>
>

以下演练介绍如何创建一个包含管道的数据工厂，该管道可将数据从本地 **SQL Server** 数据库移动到 Azure blob 存储。 在演练过程中，需要在计算机上安装并配置数据管理网关。

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>演练：将本地数据复制到云
## <a name="create-data-factory"></a>创建数据工厂
本步骤使用 Azure 门户创建名为 **ADFTutorialOnPremDF** 的 Azure 数据工厂实例。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“+新建”，然后依次单击“智能 + 分析”、“数据工厂”。

   ![新建 -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. 在“新建数据工厂”边栏选项卡中，输入 **ADFTutorialOnPremDF** 作为名称。

    ![添加到开始板](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Azure 数据工厂的名称必须全局唯一。 如果收到错误：**数据工厂名称“ADFTutorialOnPremDF”不可用**，请更改该数据工厂名称（例如改为“yournameADFTutorialOnPremDF”），并尝试再次创建。 执行本教程中的剩余步骤时，请使用此名称取代 ADFTutorialOnPremDF。
   >
   > 数据工厂名称可能在将来被注册为 **DNS** 名称，因此将变成公开可见。
   >
   >
4. 选择要在其中创建数据工厂的 **Azure 订阅** 。
5. 选择现有的 **资源组** ，或创建资源组。 本教程创建名为 **ADFTutorialResourceGroup** 的资源组。
6. 在“新建数据工厂”边栏选项卡中单击“创建”。

   > [!IMPORTANT]
   > 只有订阅/资源组级别的 [数据工厂参与者](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) 角色成员才能创建数据工厂实例。
   >
   >
7. 完成创建后，将看到如下图所示的“数据工厂”边栏选项卡：

   ![数据工厂主页](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>创建网关
1. 在“数据工厂”边栏选项卡中，单击“编写和部署”磁贴启动数据工厂的“编辑器”。

    ![“编写和部署”磁贴](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. 在“数据工厂编辑器”中，单击工具栏上的“...更多”，然后单击“新建数据网关”。 此外，也可以在树视图中，右键单击“数据网关”，然后单击“新建数据网关”。

   ![工具栏上的“新建数据网关”](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. 在“创建”边栏选项卡中，输入 **adftutorialgateway** 作为**名称**，然后单击“确定”。     

    ![创建网关边栏选项卡](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)
4. 在“配置”边栏选项卡中，单击“直接在此计算机上安装”。 此操作将为网关下载安装包，并在计算机上安装、配置和注册该网关。  

   > [!NOTE]
   > 请使用 Internet Explorer 或与 Microsoft ClickOnce 兼容的 Web 浏览器。
   >
   > 如果使用 Chrome，请转到 [Chrome 网上应用店](https://chrome.google.com/webstore/)，使用“ClickOnce”关键字进行搜索，然后选择 ClickOnce 扩展之一并安装它。
   >
   > 对于 Firefox，请执行相同的操作（安装外接程序）。 在工具栏上单击“打开菜单”按钮（右上角的**三条横线**），单击“外接程序”，使用“ClickOnce”关键字进行搜索，然后选择一个 ClickOnce 扩展并安装它。    
   >
   >

    ![网关 - 配置边栏选项卡](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    此方法是执行以下操作最简单的方法（一键式操作）：下载、安装、配置和注册网关，只需一步操作即可完成。 可以看到计算机上已安装了 **Microsoft 数据管理网关配置管理器**应用程序。 还可以在文件夹 **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared** 中查找可执行文件 **ConfigManager.exe**。

    此外，还可以使用此边栏选项卡中的链接手动下载和安装网关，并使用**新密钥**文本框中所示的密钥对其进行注册。

    有关网关的所有详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)一文。

   > [!NOTE]
   > 必须是本地计算机的管理员，才能成功安装和配置数据管理网关。 可以将其他用户添加到“数据管理网关用户”本地 Windows 组。 此组的成员可以使用数据管理网关配置管理器工具来配置网关。
   >
   >
5. 等待几分钟，或一直等待直到看到以下通知消息：

    ![网关安装成功](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. 启动计算机上的“数据管理网关配置管理器”应用程序。 在“搜索”窗口中，键入“数据管理网关”，以访问此实用程序。 还可以在文件夹 **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared** 中查找可执行文件 **ConfigManager.exe**

    ![网关配置管理器](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. 确认可以看到 `adftutorialgateway is connected to the cloud service` 消息。 底部状态栏显示带有**绿色复选标记**的“已连接到云服务”。

    在“主页”选项卡上，还可以执行以下操作：

   * 单击“注册”按钮，使用 Azure 门户中的密钥**注册**一个网关。
   * **停止**在网关计算机上运行的数据管理网关主机服务。
   * 在一天中的特定时间安装**计划更新**。
   * 查看网关的**上次更新时间**。
   * 指定可以安装网关更新的时间点。
8. 切换到“设置”选项卡。 使用在**证书**部分中指定的证书，加密/解密在门户上指定的本地数据存储的凭据。 （可选）单击“更改”以改为使用你自己的证书。 默认情况下，网关使用由数据工厂服务自动生成的证书。

    ![网关证书配置](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    还可以在“设置”选项卡上执行以下操作：

   * 查看或导出网关正在使用的证书。
   * 更改网关使用的 HTTPS 终结点。    
   * 设置网关使用的 HTTP 代理。     
9. （可选）如果想要启用可用于排查有关网关的任何问题的详细日志记录，请切换到“诊断”选项卡，选中“启用详细日志记录”选项。 可在“应用程序和服务日志” -> “数据管理网关”节点下的“事件查看器”中找到该日志记录信息。

    ![“诊断”选项卡](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    还可以在“诊断”选项卡中执行以下操作：

   * 对使用网关的本地数据源使用**测试连接**部分。
   * 单击“查看日志”，以在“事件查看器”窗口中查看数据管理网关日志。
   * 单击“发送日志”将包含最后七天的日志的 zip 文件上传到 Microsoft，以便排查问题。
10. 在“诊断”选项卡上的“测试连接”部分中，选择 **SqlServer** 作为数据存储类型，输入数据库服务器和数据库的名称，指定身份验证类型，输入用户名和密码，然后单击“测试”以测试网关是否可以连接到数据库。
11. 切换到 Web 浏览器，在“Azure 门户”中，单击“配置”边栏选项卡上的“确定”，然后单击“新建数据网关”边栏选项卡上的“确定”。
12. 左侧树视图中的“数据网关”下应会显示 **adftutorialgateway**。  单击此项可看到相关联的 JSON。

## <a name="create-linked-services"></a>创建链接服务
本步骤创建两个链接服务：**AzureStorageLinkedService** 和 **SqlServerLinkedService**。 **SqlServerLinkedService** 用于链接本地 SQL Server 数据库，而 **AzureStorageLinkedService** 链接服务用于将 Azure blob 存储链接到数据工厂。 本演练稍后将介绍如何创建管道，将数据从本地 SQL Server 数据库复制到 Azure blob 存储。

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>将链接服务添加到本地 SQL Server 数据库
1. 在“数据工厂编辑器”中，单击工具栏上的“新建数据存储”，然后选择“SQL Server”。

   ![新建 SQL Server 链接服务](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. 在右侧的“JSON 编辑器”中，执行以下步骤：

   1. 对于 **gatewayName**指定 **adftutorialgateway**。    
   2. 在 **connectionString** 中，执行以下步骤：    

      1. 对于 **servername**，输入托管 SQL Server 数据库的服务器名称。
      2. 对于 **databasename**，输入数据库名称。
      3. 单击工具栏上的“加密”按钮。 此操作将下载并启动凭据管理器应用程序。

         ![凭据管理器应用程序](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. 在“设置凭据”对话框中，指定身份验证类型、用户名和密码，然后单击“确定”。 如果连接成功，则加密的凭据将存储在 JSON 中，并且对话框将关闭。
      5. 如果启动对话框的空浏览器选项卡未自动关闭，则关闭该选项卡，并通过 Azure 门户返回到该选项卡。

         在网关计算机上，这些凭据使用数据工厂服务拥有的证书进行“加密”。 如果想要改用与数据管理网关相关联的证书，请参阅[安全地设置凭据](#set-credentials-and-security)。    
   3. 单击命令栏上的“部署”，部署 SQL Server 链接服务。 可在树视图中看到链接服务。

      ![树视图中的 SQL Server 链接服务](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>为 Azure 存储帐户添加链接服务
1. 在“数据工厂编辑器”中，单击命令栏上的“新建数据存储”，然后单击“Azure 存储”。
2. 对于“帐户名称”，输入 Azure 存储帐户的名称。
3. 对于“帐户密钥”，输入 Azure 存储帐户的密钥。
4. 单击“部署”以部署 **AzureStorageLinkedService**。

## <a name="create-datasets"></a>创建数据集
本步骤创建表示输入和输出数据的输入和输出数据集以进行复制操作（从本地 SQL Server 数据库复制到 Azure blob 存储）。 在创建数据集之前，执行以下步骤（详细步骤见以下列表）：

* 在作为链接服务添加到数据工厂的 SQL Server 数据库中，创建名为 **emp** 的表，并在该表中插入几个示例条目。
* 在作为链接服务添加到数据工厂的 Azure blob 存储帐户中，创建一个名为 **adftutorial** 的 blob 容器。

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>为本教程准备本地 SQL Server
1. 在为本地 SQL Server 链接服务 (**SqlServerLinkedService**) 指定的数据库中，使用以下 SQL 脚本在数据库中创建 **emp** 表。

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. 在该表中插入几个示例：

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>创建输入数据集

1. 在“数据工厂编辑器”中，单击“...更多”，单击命令栏中的“新建数据集”，然后单击“SQL Server 表”。
2. 将右窗格中的 JSON 替换为以下文本：

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   请注意以下几点：

   * **type** 设置为 **SqlServerTable**。
   * **tableName** 设置为 **emp**。
   * **linkedServiceName** 设置为 **SqlServerLinkedService**（在本演练前面部分中已创建此链接服务。）。
   * 对于不是由 Azure 数据工厂中的另一管道生成的输入数据集，必须将 **external** 设置为 **true**。 表示该输入数据是在 Azure 数据工厂服务外部生成的。 可以使用**策略**部分中的 **externalData** 元素选择性地指定任何外部数据策略。    

   有关 JSON 属性的详细信息，请参阅[将数据移出/移入 SQL Server](data-factory-sqlserver-connector.md)。
3. 单击命令栏上的“部署”来部署数据集。  

### <a name="create-output-dataset"></a>创建输出数据集

1. 在“数据工厂编辑器”中，单击命令栏上的“新建数据集”，然后单击“Azure Blob 存储”。
2. 将右窗格中的 JSON 替换为以下文本：

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   请注意以下几点：

   * **type** 设置为 **AzureBlob**。
   * **linkedServiceName** 设置为 **AzureStorageLinkedService**（已在步骤 2 中创建此链接服务）。
   * **folderPath** 设置为 **adftutorial/outfromonpremdf**，其中 outfromonpremdf 为 adftutorial 容器中的文件夹。 如果不存在 **adftutorial** 容器，则创建该容器。
   * **availability** 设置为 **hourly**（**frequency** 设置为 **hour**，**interval** 设置为 **1**）。  数据工厂服务每小时在 Azure SQL 数据库的 **emp** 表中生成输出数据切片。

   如果未指定**输出表**的 **fileName**，**folderPath** 中生成的文件根据以下格式命名：Data.<Guid>.txt（例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt）。

   若要根据 **SliceStart** 时间动态设置 **folderPath** 和 **fileName**，请使用 partitionedBy 属性。 在以下示例中，folderPath 使用 SliceStart（所处理的切片的开始时间）中的年、月和日，fileName 使用 SliceStart 中的小时。 例如，如果切片生成于 2014-10-20T08:00:00，folderName 将设置为 wikidatagateway/wikisampledataout/2014/10/20，fileName 设置为 08.csv。

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    有关 JSON 属性的详细信息，请参阅[将数据移出/移入 Azure Blob 存储](data-factory-azure-blob-connector.md)。
3. 单击命令栏上的“部署”来部署数据集。 确认树视图中显示了这两个数据集。  

## <a name="create-pipeline"></a>创建管道
本步骤创建**管道**，其中包含使用 **EmpOnPremSQLTable** 作为输入、使用 **OutputBlobTable** 作为输出的**复制活动**。

1. 在“数据工厂编辑器”中，单击“...更多”，然后单击“新建管道”。
2. 将右窗格中的 JSON 替换为以下文本：    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on-prem SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > 将 **start** 属性的值替换为当前日期，将 **end** 值替换为下一个日期。
   >
   >

   请注意以下几点：

   * 在 activities 节中，只有一个活动的 **type** 设置为 **Copy**。
   * 活动的 **Input** 设置为 **EmpOnPremSQLTable**，**output** 设置为 **OutputBlobTable**。
   * 在 **typeProperties** 节中，**SqlSource** 指定为**源类型**，**BlobSink** 指定为**接收器类型**。
   * 为 **SqlSource** 的 **sqlReaderQuery** 属性指定 SQL 查询 `select * from emp`。

   开始和结束日期时间必须采用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。 **结束** 时间是可选的，但本教程使用该时间。

   如果未指定 **end** 属性的值，则以“**开始时间 + 48 小时**”计算。 若要无限期运行管道，请指定 **9/9/9999** 作为 **end** 属性的值。

   正在基于 **Availability** 属性定义处理数据切片的持续时间，该属性是针对每个 Azure 数据工厂数据集而定义的。

   在示例中，由于每小时生成一个数据切片，因此共有 24 个数据切片。        
3. 单击命令栏上的“部署”来部署数据集（表为矩形数据集）。 确认“管道”节点下的树视图中显示管道。  
4. 现在，单击 **X** 两次，关闭边栏选项卡，以返回到 **ADFTutorialOnPremDF** 的“数据工厂”边栏选项卡。

**祝贺你！** 现已成功创建 Azure 数据工厂、链接服务、数据集和管道，并已计划好管道。

#### <a name="view-the-data-factory-in-a-diagram-view"></a>在图示视图中查看数据工厂
1. 在“Azure 门户”中，单击主页上 **ADFTutorialOnPremDF** 数据工厂的“关系图”磁贴。 :

    ![关系图链接](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. 应会看到如下所示的图形：

    ![图示视图](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    可以将管道和数据集放大、缩小、还原、缩放到适当比例和自动定位，以及显示沿袭信息（突出显示所选项的上游和下游项）。  可以双击对象（输入/输出数据集或管道）查看其属性。

## <a name="monitor-pipeline"></a>监视管道
本步骤使用 Azure 门户监视 Azure 数据工厂的运行情形。 还可以使用 PowerShell cmdlet 来监视数据集和管道。 有关监视的详细信息，请参阅[监视和管理管道](data-factory-monitor-manage-pipelines.md)。

1. 在关系图中，双击 **EmpOnPremSQLTable**。  

    ![EmpOnPremSQLTable 切片](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. 请注意，所有数据切片都处于**就绪**状态，因为管道持续时间（开始时间到结束时间）发生在过去。 还因为已在 SQL Server 数据库中插入数据，并且数据一直处于该位置。 确认底部的“问题切片”部分中未显示任何切片。 若要查看所有切片，请单击切片列表底部的“查看更多”。
3. 现在，请在“数据集”边栏选项卡中单击“OutputBlobTable”。

    ![OputputBlobTable 切片](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. 在列表中单击任一数据切片可以看到“数据切片”边栏选项卡。 可看到为切片运行的活动。 通常只能看到一个活动运行。  

    ![“数据切片”边栏选项卡](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    如果切片状态不是“就绪”，可以在“未就绪的上游切片”列表中看到未就绪且阻碍当前切片运行的上游切片。
5. 单击底部列表中的“活动运行”可看到“活动运行详细信息”。

   ![“活动运行详细信息”边栏选项卡](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   可看到吞吐量、持续时间和用于传输数据的网关等信息。
6. 单击 **X** 关闭所有边栏选项卡，直到
7. 返回到 **ADFTutorialOnPremDF** 的“主页”边栏选项卡。
8. （可选）单击“管道”，再单击“ADFTutorialOnPremDF”，钻取输入表 (**Consumed**) 或输出数据集 (**Produced**)。
9. 使用工具（例如 [Microsoft 存储资源管理器](http://storageexplorer.com/)）验证是否每小时创建一个 blob/文件。

   ![Azure 存储空间资源管理器](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>后续步骤
* 有关数据管理网关的所有详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)一文。
* 若要深入了解如何使用“复制活动”将数据从源数据存储移动到接收器数据存储，请参阅[将数据从 Azure Blob 复制到 Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

