---
title: '教程：使用复制向导创建管道 '
description: 在本教程中，通过使用数据工厂所支持的复制向导，创建带有复制活动的 Azure 数据工厂管道。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8bbe32a202af3b8684c16cc2e56d5a111511bef5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75438902"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>教程：使用数据工厂复制向导创建带有复制活动的管道
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [复制向导](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 模板](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用的是数据工厂服务的当前版本，请参阅[复制活动教程](../quickstart-create-data-factory-dot-net.md)。 


本教程介绍如何使用**复制向导**将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 

使用 Azure 数据工厂**复制向导**可以快速创建数据管道，以便将数据从支持的源数据存储复制到支持的目标数据存储。 因此，建议首先使用向导为数据移动方案创建一个示例管道。 如需可以用作源和目标的数据存储的完整列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。  

本教程说明如何创建 Azure 数据工厂、启动复制向导，以及执行一系列步骤来提供有关数据引入/移动方案的详细信息。 完成向导中的步骤后，向导会自动创建包含复制活动的管道，将数据从 Azure Blob 存储复制到 Azure SQL 数据库。 有关复制活动的详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)。

## <a name="prerequisites"></a>必备条件
执行本教程之前，请完成[教程概述](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)中的先决条件步骤。

## <a name="create-data-factory"></a>创建数据工厂
本步中，使用 Azure 门户创建名为 **ADFTutorialDataFactory**的 Azure 数据工厂。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击左上角的“创建资源”  ，单击“数据 + 分析”  ，然后单击“数据工厂”  。 
   
   ![新建 -> DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. 在“新建数据工厂”  边栏选项卡中：
   
   1. 输入 **ADFTutorialDataFactory** 作为**名称**。
       Azure 数据工厂的名称必须全局唯一。 如果收到错误`Data factory name “ADFTutorialDataFactory” is not available`，请更改数据工厂的名称（例如改为 yournameADFTutorialDataFactoryYYYYMMDD），并重新尝试创建。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。  
      
       ![数据工厂名称不可用](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. 选择 **Azure 订阅**。
   3. 对于资源组，请执行以下步骤之一： 
      
      - 选择“使用现有资源组”并选择一个现有的资源组。 
      - 选择“新建”并输入资源组的名称。 
          
        本教程中的一些步骤假定使用 **ADFTutorialResourceGroup** 作为资源组名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../../azure-resource-manager/management/overview.md)。
   4. 选择数据工厂的**位置**。
   5. 选中位于边栏选项卡底部的“固定到仪表板”复选框。   
   6. 单击“创建”。 
      
       ![“新建数据工厂”边栏选项卡](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. 完成创建后，将看到如下图所示的“数据工厂”边栏选项卡： 
   
   ![数据工厂主页](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>启动复制向导
1. 在“数据工厂”边栏选项卡上，单击“复制数据”以启动“复制向导”。   
   
   > [!NOTE]
   > 如果 Web 浏览器卡在“正在授权...”处，请在浏览器设置中禁用或取消选中“阻止第三方 Cookie 和站点数据”设置，或在保持启用的状态下为 **login.microsoftonline.com** 创建一个例外，并尝试再次启动该向导。
2. 在“属性”  页中：
   
   1. 输入 **CopyFromBlobToAzureSql** 作为**任务名称**
   2. 输入 **说明** （可选）。
   3. 更改“开始日期时间”和“结束日期时间”，使结束日期设置为当天，开始日期设置为五天前。    
   4. 单击“下一步”。   
      
      ![复制工具 - 属性页](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. 在“源数据存储”页上，单击“Azure Blob 存储”磁贴。   此页用于指定复制任务的源数据存储。 
   
    ![复制工具 - 源数据存储页](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. 在“指定 Azure Blob 存储帐户”  页上：
   
   1. 输入 **AzureStorageLinkedService** 作为**链接服务名称**。
   2. 确认为“帐户选择方法”选择了“来自 Azure 订阅”。  
   3. 选择 **Azure 订阅**。  
   4. 从所选订阅的可用 Azure 存储帐户列表中，选择一个 **Azure 存储帐户**。 还可选择手动输入存储帐户设置，方法是在“帐户选择方法”中选择“手动输入”选项，并单击“下一步”。    
      
      ![复制工具 - 指定 Azure Blob 存储帐户](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. 在“选择输入文件或文件夹”  页上：
   
   1. 双击“adftutorial”  （文件夹）。
   2. 选择“emp.txt”，并单击“选择”  
      
      ![复制工具 - 选择输入文件或文件夹](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. 在“选择输入文件或文件夹”页中，单击“下一步”。   请不要选择“二进制副本”  。 
   
    ![复制工具 - 选择输入文件或文件夹](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. 在“文件格式设置”页上，可以看到分隔符以及向导通过分析文件自动检测到的架构。  也可以手动输入分隔符，使复制向导停止自动检测或覆盖设置。 检查分隔符并预览数据之后，请单击“下一步”。  
   
    ![复制工具 - 文件格式设置](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. 在“目标数据存储”页上，选择“Azure SQL 数据库”，并单击“下一步”。  
   
    ![复制工具 - 选择目标存储](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. 在“指定 Azure SQL 数据库”  页中：
   
   1. 在“连接名称”字段中输入 **AzureSqlLinkedService**。 
   2. 确认为“服务器/数据库选择方法”选择了“来自 Azure 订阅”。  
   3. 选择 **Azure 订阅**。  
   4. 选择**服务器名称**和**数据库**。
   5. 输入**用户名**和**密码**。
   6. 单击“下一步”。   
      
      ![复制工具 - 指定 Azure SQL 数据库](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. 在“表映射”页上，在“目标”字段中通过下拉列表中选择“emp”，单击**向下箭头**查看架构并预览数据（可选）。
    
     ![复制工具 - 表映射](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. 在“架构映射”页上，单击“下一步”。  
    
    ![复制工具 - 架构映射](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. 在“性能设置”页上，单击“下一步”。   
    
    ![复制工具 - 性能设置](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. 在“摘要”页中检查信息，并单击“完成”。   复制向导在数据工厂（即启动该向导的位置）中创建两个链接服务、两个数据集（输入和输出）和一个管道。 
    
    ![复制工具 - 性能设置](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>启动监视器和管理应用程序
1. 在“部署”页上单击以下链接：`Click here to monitor copy pipeline`。
   
   ![复制工具 - 部署成功](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. 监视应用程序会在 Web 浏览器的单独选项卡中启动。   
   
   ![监视应用](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. 若要查看小时切片的最新状态，请单击“活动时段”列表底部的“刷新”按钮。   可以看到五个活动窗口，对应于管道开始时间和结束时间之间的五天。 列表不会自动刷新，因此可能需要单击“刷新”多次，才能看到所有活动窗口都处于“就绪”状态。 
4. 在列表中选择一个活动窗口。 可以在右侧的**活动窗口资源管理器**中查看其详细信息。

    ![活动窗口详细信息](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    请注意，11、12、13、14、15 这几个日期的颜色为绿色，表明已生成这些日期的每日输出切片。 也可在图示视图的管道和输出数据集上看到该颜色编码。 请注意，在上一步中，两个切片已生成，一个切片当前正在处理，还有两个切片正在等待处理（根据颜色编码判断）。 

    有关如何使用此应用程序的详细信息，请参阅[使用监视应用监视和管理管道](data-factory-monitor-manage-app.md)一文。

## <a name="next-steps"></a>后续步骤
在本教程中，在复制操作中使用了 Azure Blob 存储作为源数据存储，使用了 Azure SQL 数据库作为目标数据存储。 下表列出了复制活动支持的充当源和目标的数据存储： 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

若要详细了解在复制向导中看到的数据存储的字段/属性，请单击表中数据存储的链接。 