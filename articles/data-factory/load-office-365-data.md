---
title: 使用 Azure 数据工厂从 Office 365 加载数据 | Microsoft Docs
description: 使用 Azure 数据工厂从 Office 365 复制数据
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: jingwang
ms.openlocfilehash: e2b441e0266ac397226b783c66df4ca54e0b3f71
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870156"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Office 365 加载数据

本文介绍如何使用数据工厂将 Office 365 中的数据载入 Azure Blob 存储。 可以遵循类似的步骤将数据复制到 Azure Data Lake Gen1 或 Gen2。 请参阅 [Office 365 连接器文章](connector-office-365.md)，了解有关从 Office 365 复制数据的一般信息。

请观看以下 9 分钟简介和演示视频，了解如何将数据工厂连接到 Office 365 数据：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-cloud-scale-analytics-of-Office-365-data-with-Azure-Data-Factory/player]

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“新建” > “数据 + 分析” > “数据工厂”： 
   
   ![新建数据工厂](./media/load-office-365-data/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，为下图中所示的字段提供值：
      
   ![“新建数据工厂”页](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 \"LoadFromOffice365Demo\" 不可用”，请输入不同的数据工厂名称。 例如，可以使用名称 _**yourname**_**LoadFromOffice365Demo**。 请重试创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
    * **版本**：选择“V2”.
    * **位置**：选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 这些数据存储包括 Azure Data Lake Store、Azure 存储、Azure SQL 数据库，等等。

3. 选择“创建”。
4. 创建操作完成后，请转到数据工厂。 此时会看到“数据工厂”主页，如下图所示：
   
   ![数据工厂主页](./media/load-office-365-data/data-factory-home-page.png)

5. 选择“创作和监视”磁贴，在单独的选项卡中启动数据集成应用程序。

## <a name="create-a-pipeline"></a>创建管道

1. 在“开始使用”页中，选择“创建管道”。
 
    ![创建管道](./media/load-office-365-data/create-pipeline-entry.png)

2. 在管道的“常规”选项卡中，输入“CopyPipeline”作为管道的**名称**。

3. 在“活动”工具箱 >“移动和转换”类别中，将“复制”活动从工具箱拖放到管道设计器图面。 指定“CopyFromOffice365ToBlob”作为活动名称。

### <a name="configure-source"></a>配置源

1. 转到管道 >“源”选项卡，单击“+ 新建”创建源数据集。 

2. 在“新建数据集”窗口中，选择“Office 365”，然后选择“完成”。

    ![新建 Office 365 数据集](./media/load-office-365-data/new-office-365-dataset.png)
 
3. 此时可以看到为 Office 365 数据集打开了一个新选项卡。 在“属性”窗口底部的“常规”选项卡中，输入“SourceOffice365Dataset”作为名称。

    ![配置 Office 365 数据集 - 常规](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. 转到“属性”窗口的“连接”选项卡。 单击“链接服务”文本框旁边的“+ 新建”。
 
    ![配置 Office 365 数据集 - 连接](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. 在“新建链接服务”窗口中，输入“Office365LinkedService”作为名称，输入服务主体 ID 和服务主体密钥，然后选择“保存”以部署链接服务。

    ![新建 Office 365 链接服务](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. 在创建链接服务后，会返回到数据集设置。 在“表”旁边，选择向下箭头展开可用的 Office 365 数据集列表，然后从下拉列表中选择“BasicDataSet_v0.Contact_v0”：

    ![配置 Office 365 数据集 - 表](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. 转到“属性”窗口的“架构”选项卡，选择“导入架构”。  可以看到，“联系人”数据集的架构和示例值已显示。

    ![配置 Office 365 数据集 - 架构](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. 现在，返回到管道 ->“源”选项卡，确认已选择了 SourceBlobDataset。
 
### <a name="configure-sink"></a>配置接收器

1. 转到管道 >“接收器”选项卡，选择“+ 新建”以创建一个接收器数据集。
 
2. 在“新建数据集”窗口中可以看到，从 Office 365 复制时仅选择了受支持的目标。 依次选择“Azure Blob 存储”、“完成”。  在本教程中，我们要将 Office 365 数据复制到 Azure Blob 存储。

    ![新建 Blob 数据集](./media/load-office-365-data/new-blob-dataset.png)

4. 在“属性”窗口的“常规”选项卡的“名称”中，输入“OutputBlobDataset”。

5. 转到“属性”窗口的“连接”选项卡。 在“链接服务”文本框旁边，选择“+ 新建”。

    ![配置 Blob 数据集 - 连接](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. 在“新建链接服务”窗口中，输入“AzureStorageLinkedService”作为名称，从身份验证方法下拉列表中选择“服务主体”，填写“服务终结点”、“租户服务主体 ID”和“服务主体密钥”，然后选择“保存”以部署链接服务。  请参阅[此文](connector-azure-blob-storage.md#service-principal-authentication)，了解如何为 Azure Blob 存储设置服务主体身份验证。

    ![新建 Blob 链接服务](./media/load-office-365-data/new-blob-linked-service.png)

7. 在创建链接服务后，会返回到数据集设置。 在“文件路径”旁边，选择“浏览”以选择要将 Office 365 数据提取到输出文件夹。  在“文件格式设置”下的“文件格式”旁边选择“JSON 格式”，然后在“文件模式”旁边选择“对象集”。

    ![配置 Blob 数据集 - 路径和格式](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. 返回到管道 ->“接收器”选项卡，确认已选择了 OutputBlobDataset。

## <a name="validate-the-pipeline"></a>验证管道

若要验证管道，请从工具栏中选择“验证”。

还可以通过单击右上角的“代码”来查看与管道关联的 JSON 代码。

## <a name="publish-the-pipeline"></a>发布管道

在顶部工具栏中，选择“全部发布”。 此操作将所创建的实体（数据集和管道）发布到数据工厂。

![发布更改](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>手动触发管道

选择工具栏中的“触发器”，然后选择“立即触发”。 在“管道运行”页上选择“完成”。 

## <a name="monitor-the-pipeline"></a>监视管道

转到左侧的“监视”选项卡。 此时会看到由手动触发器触发的管道运行。 可以使用“操作”列中的链接来查看活动详细信息以及重新运行该管道。

![监视管道](./media/load-office-365-data/pipeline-monitoring.png) 

若要查看与管道运行关联的活动运行，请选择“操作”列中的“查看活动运行”链接。 此示例中只有一个活动，因此列表中只看到一个条目。 有关复制操作的详细信息，请选择“操作”列中的“详细信息”链接（眼镜图标）。

![监视活动](./media/load-office-365-data/activity-monitoring.png) 

如果这是你首次请求此上下文（要访问的数据表、要将数据加载到的目标帐户和发出数据访问请求的用户标识的组合）的数据，则复制活动状态将显示为“正在进行”；仅当单击“操作”下的“详细信息”链接时，状态才显示为“正在请求许可”。  在继续执行数据提取之前，数据访问审批者组的成员需要在 Privileged Access Management 中审批该请求。

_正在请求许可状态：_
![活动执行详细信息 - 请求许可](./media/load-office-365-data/activity-details-request-consent.png) 

_正在提取数据状态：_

![活动执行详细信息 - 提取数据](./media/load-office-365-data/activity-details-extract-data.png) 

提供许可后，数据提取将会继续，一段时间后，管道运行将显示为已完成。

![监视管道 - 成功](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

现在，请转到 Azure Blob 存储目标，并验证是否已提取 JSON 格式的 Office 365 数据。

## <a name="next-steps"></a>后续步骤

请转至下列文章，了解有关 Azure SQL 数据仓库支持的相关信息： 

> [!div class="nextstepaction"]
>[Office 365 连接器](connector-office-365.md)