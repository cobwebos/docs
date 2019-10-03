---
title: 使用 Azure 数据工厂从 SAP Business Warehouse 中加载数据 |Microsoft Docs
description: 使用 Azure 数据工厂从 SAP Business Warehouse (BW) 复制数据
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: jingwang
ms.openlocfilehash: 3c846ab3e81e7ab8a4948aa4ed96cfa75e8eb3f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449688"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP Business Warehouse 复制数据

本文介绍如何使用 Azure 数据工厂将数据从 SAP Business Warehouse (BW) 通过打开中心复制到 Azure 数据湖存储第 2 代。 可以使用类似的过程将数据复制到其他[支持的接收器数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

> [!TIP]
> 有关如何将数据从 SAP BW，包括 SAP BW 开放中心集成和增量提取流复制的常规信息，请参阅[通过使用 Azure 数据工厂将数据复制从 SAP Business Warehouse 通过 Open Hub](connector-sap-business-warehouse-open-hub.md)。

## <a name="prerequisites"></a>必备组件

- **Azure 数据工厂**:如果你没有帐户，遵循相应的步骤[创建数据工厂](quickstart-create-data-factory-portal.md#create-a-data-factory)。

- **SAP BW 开放中心目标 (OHD) 与目标类型"数据库表"** :若要创建 OHD 或检查你 OHD 是否正确配置为数据工厂集成，请参阅[SAP BW Open Hub 目标配置](#sap-bw-open-hub-destination-configurations)本文的部分。

- **SAP BW 用户需要以下权限**:

  - 远程函数调用 (RFC) 的授权和 SAP BW。
  - 对"执行"活动的权限**S_SDSAUTH**授权对象。

- **一个[自我托管集成运行时 (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime)与 SAP.NET connector 3.0**。 请按照这些设置步骤操作：

  1. 安装并注册自承载的集成运行时，3.13 或更高版本。 （描述此过程是在本文后面部分。）

  2. 下载[64 位 SAP 连接器用于 Microsoft.NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)从 SAP 的网站，并将其安装在同一台计算机作为自承载 ir。 在安装期间，请确保您选择**程序集安装到 GAC**中**可选设置步骤**对话框中的，如下图所示：

     ![设置 SAP.NET 连接器对话框](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>从 SAP BW 开放中心执行操作的完整副本

在 Azure 门户中，转到数据工厂。 选择**创作和监视**在单独的选项卡中打开数据工厂 UI。

1. 上**让我们开始吧**页上，选择**复制数据**打开复制数据工具。

2. 上**属性**页上，指定**任务名称**，然后选择**下一步**。

3. 上**源数据存储区**页上，选择 **+ 创建新的连接**。 选择**SAP BW Open Hub**从连接器库，并选择**继续**。 若要筛选的连接器，可以键入**SAP**在搜索框中。

4. 上**指定 SAP BW Open Hub 连接**页上，执行以下步骤以创建新的连接。

   ![创建 SAP BW 开放中心链接服务页](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. 从**通过集成运行时连接**列表中，选择现有的自承载 ir。 或者，如果你没有订阅创建一个选择。

      若要创建新的自承载的 IR，请选择 **+ 新建**，然后选择**自承载**。 输入**名称**，然后选择**下一步**。 选择**Express 安装程序**以安装在当前计算机，或按照**手动设置**提供的步骤。

      如中所述[先决条件](#prerequisites)，请确保用于 Microsoft.NET 3.0 安装自承载的 IR 正在其中运行在同一计算机上拥有 SAP 连接器。

   2. 填写 SAP BW**服务器名称**，**系统编号**，**客户端 ID** **语言**(如果不同于**EN**)**用户名**，并**密码**。

   3. 选择**测试连接**以验证设置，然后选择**完成**。

   4. 创建新的连接。 选择“**下一步**”。

5. 上**选择打开的中心目标**页上，浏览打开 SAP BW 中可用的中心目标。 选择以复制数据，然后选择 OHD**下一步**。

   ![选择 SAP BW Open Hub 目标表](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 如果你需要一个，指定筛选器。 如果你 OHD 仅包含与单个请求 ID，在单个数据传输进程 (DTP) 执行中的数据，或确保在 DTP 完成，并且你想要复制的数据，清除**排除上一次请求**复选框。

   了解有关这些设置的详细信息[SAP BW Open Hub 目标配置](#sap-bw-open-hub-destination-configurations)本文的部分。 选择**验证**要仔细检查不会返回哪些数据。 然后，选择“下一步”  。

   ![配置 SAP BW Open Hub 筛选器](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. 上**目标数据存储区**页上，选择 **+ 创建新的连接** > **Azure 数据湖存储第 2 代** >  **继续**。

8. 上**指定 Azure Data Lake 存储连接**页上，执行以下步骤以创建连接。

   ![创建 ADLS 第 2 代链接的服务页](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. 选择从帐户的 Data Lake 存储支持第 2 代**名称**下拉列表。
   2. 选择“完成”  以创建连接。 然后，选择“下一步”  。

9. 上**选择输出文件或文件夹**页上，输入**copyfromopenhub**作为输出文件夹名称。 然后，选择“下一步”  。

   ![选择文件夹输出页](media/load-sap-bw-data/choose-output-folder.png)

10. 上**文件格式设置**页上，选择**下一步**以使用默认设置。

    ![指定接收器格式页](media/load-sap-bw-data/specify-sink-format.png)

11. 上**设置**页上，展开**性能设置**。 输入一个值**复制度**例如 5 并行加载来自 SAP BW。 然后，选择“下一步”  。

    ![配置复制设置](media/load-sap-bw-data/configure-copy-settings.png)

12. 上**摘要**页上，查看设置。 然后，选择“下一步”  。

13. 上**部署**页上，选择**监视器**来监视管道。

    ![“部署”页](media/load-sap-bw-data/deployment.png)

14. 请注意，**监视器**自动选择页面左侧的选项卡。 **操作**列中包含链接以查看活动运行详细信息，以及重新运行管道。

    ![监视视图的管道](media/load-sap-bw-data/pipeline-monitoring.png)

15. 若要查看与管道运行相关联的活动运行，请选择**查看活动运行**中**操作**列。 该管道只包含一个活动（复制活动），因此只显示了一个条目。 若要切换回到管道运行视图，请选择**管道**顶部的链接。 选择“刷新”可刷新列表。 

    ![活动监视屏幕](media/load-sap-bw-data/activity-monitoring.png)

16. 若要监视每个复制活动的执行详细信息，请选择**详细信息**链接，这是下一个眼镜图标**操作**活动监视视图中。 可用的详细信息包括从源复制到接收器、 数据吞吐量、 执行步骤和持续时间，数据量和使用的配置。

    ![活动监视的详细信息](media/load-sap-bw-data/activity-monitoring-details.png)

17. 若要查看**最大请求 ID**，返回到该活动监视视图，然后选择**输出**下**操作**。

    ![活动输出屏幕](media/load-sap-bw-data/activity-output.png)

    ![活动输出的详细信息视图](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>从 SAP BW 打开中心的增量复制

> [!TIP]
> 请参阅[SAP BW Open Hub 连接器增量提取流](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)若要了解如何在数据工厂中的 SAP BW Open Hub 连接器复制增量数据从 SAP BW。 本文还有助于你了解基本的连接器配置。

现在，让我们继续配置从 SAP BW 打开中心的增量复制。

使用"高水位"机制为基础，增量复制**请求 ID**。 该 ID 由自动生成 SAP BW Open Hub 目标在 DTP。 下图显示了此工作流：

![增量复制工作流流程图](media/load-sap-bw-data/incremental-copy-workflow.png)

在数据工厂**让我们开始吧**页上，选择**从模板创建管道**若要使用内置模板。

1. 搜索**SAP BW**以查找并选择**增量从 SAP BW 复制到 Azure 数据湖存储第 2 代**模板。 此模板将数据复制到 Azure 数据湖存储第 2 代。 你可以使用类似的工作流将复制到其他接收器类型。

2. 在模板的主页上，选择或创建以下三个连接，并选择**使用此模板**在窗口的右下角。

   - **Azure Blob 存储**:在本演练中，我们使用 Azure Blob 存储来存储高水印，即*最大值复制请求 ID*。
   - **SAP BW 开放中心**:这是要复制的数据的源。 请参阅前面的完整拷贝演练的详细配置。
   - **Azure 数据湖存储第 2 代**:这是将数据复制到接收器。 请参阅前面的完整拷贝演练的详细配置。

   ![从 SAP BW 模板进行增量复制](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 此模板生成包含以下三个活动的管道，并使它们链接在一起上成功：*查找*，*将数据复制*，和*Web*。

   转到管道**参数**选项卡。请参阅你需要提供的所有配置。

   ![从 SAP BW 配置增量复制](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**:指定要复制的数据的 Open Hub 表名称。

   - **ADLSGen2SinkPath**:指定要将数据复制到的目标 Azure 数据湖存储第 2 代路径。 如果该路径不存在，数据工厂复制活动在执行期间创建一个路径。

   - **HighWatermarkBlobPath**:指定的路径来存储高水印值，例如`container/path`。

   - **HighWatermarkBlobName**:指定 blob 名称来存储高水印值，例如`requestIdCache.txt`。 在 Blob 存储中，转到相应的路径的 HighWatermarkBlobPath + HighWatermarkBlobName，如*container/path/requestIdCache.txt*。 使用内容 0 创建一个 blob。

      ![Blob 内容](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**:在此模板中，我们使用 WebActivity 调用 Azure 逻辑应用在 Blob 存储中设置高水印值。 或者，可以使用 Azure SQL 数据库来存储它。 使用存储的过程活动来更新的值。

      如下图所示，必须先创建逻辑应用中。 然后，在粘贴**HTTP POST URL**。

      ![逻辑应用配置](media/load-sap-bw-data/logic-app-config.png)

      1. 转到 Azure 门户。 选择一个新**逻辑应用**服务。 选择 **+ 空白逻辑应用**若要转到**逻辑应用设计器**。

      2. 创建的触发器**收到 HTTP 请求时**。 指定的 HTTP 请求正文，如下所示：

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. 添加**创建的 blob**操作。 有关**文件夹路径**并**Blob 名称**，使用以前配置中的相同值**HighWatermarkBlobPath**和**HighWatermarkBlobName**.

      4. 选择“保存”。  然后，将复制的值**HTTP POST URL**若要在数据工厂管道中使用。

4. 提供数据工厂管道参数后，选择**调试** > **完成**调用运行验证配置。 或者，选择**全部发布**以发布所做的更改，然后选择**触发器**执行运行。

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW Open Hub 目标配置

本部分介绍配置要在数据工厂中使用 SAP BW 开放中心连接器将数据复制的 SAP BW 面。

### <a name="configure-delta-extraction-in-sap-bw"></a>在 SAP BW 中配置增量提取

如果您需要历史记录副本和增量复制或仅增量复制，请在 SAP BW 中配置增量提取。

1. 创建 Open Hub 目标。 可以在 SAP 事务 RSA1，会自动创建所需的转换和数据传输过程中创建 OHD。 使用以下设置：

   - **ObjectType**:可以使用任何对象类型。 在这里，我们使用**InfoCube**作为示例。
   - **目标类型**:选择**数据库表**。
   - **表的键**:选择**技术密钥**。
   - **提取**：选择**到表中保留数据和插入记录**。

   ![创建 SAP BW OHD 增量提取对话框](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![创建 SAP BW OHD delta2 提取对话框](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   可能会增加并行 DTP 运行 SAP 工作进程数：

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 计划中进程链 DTP。

   只有在所需的行未压缩，仅适用于多维数据集的增量 DTP。 请确保 BW 多维数据集压缩不运行之前 DTP 到打开的中心表。 若要执行此操作的最简单方法是将 DTP 集成到现有的进程链。 在以下示例中，（到 OHD) DTP 插入到之间的进程链*调整*（聚合汇总） 和*折叠*（多维数据集压缩） 的步骤。

   ![创建 SAP BW 进程链流程图](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>在 SAP BW 中配置完整提取

除了增量提取，您可能希望相同的 SAP BW InfoProvider 完整提取。 这通常适用于你想要执行完整复制但不是增量，或您希望[重新同步增量提取](#resync-delta-extraction)。

您不能具有多个 DTP 的相同 OHD。 因此，必须创建其他 OHD 增量提取前。

![创建完整的 SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

对于完全加载 OHD，选择不同选项比增量提取：

- 在 OHD:设置**提取**选项设为**删除数据和插入记录**。 否则，将很多时候时重复 DTP BW 进程链中的提取数据。

- 在 DTP:设置**提取模式**到**完整**。 必须更改从自动创建的 DTP**增量**到**完整**立即创建，因为此图显示了 OHD 后：

   ![创建 SAP BW OHD 为"完整"提取配置对话框](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- 在数据工厂的 BW Open Hub 连接器：关闭**排除上一次请求**。 否则，将提取执行任何操作。

您通常手动运行完整 DTP。 或者，你可以为完整 DTP 创建进程链。 它通常是独立于现有的进程链的单独链。 在任一情况下，*确保通过使用数据工厂复制开始提取之前完成 DTP*。 否则，将复制仅部分数据。

### <a name="run-delta-extraction-the-first-time"></a>运行增量提取第一次

从技术上讲是第一个增量提取*完整的提取*。 默认情况下，SAP BW Open Hub 连接器时，不包括最后一个请求将复制数据。 对于第一个增量提取、 任何数据不提取由数据工厂复制活动之前后续 DTP 生成一个单独的请求 id。 在表中的增量数据 有两种方法来避免这种情况：

- 关闭**排除上一次请求**为第一个增量提取选项。 请确保在开始增量提取第一次之前，已完成的第一个增量 DTP。
-  用于过程正在重新同步增量提取下一节中所述。

### <a name="resync-delta-extraction"></a>重新同步增量提取

以下情况下更改在 SAP BW 多维数据集中的数据，但并不认为增量 DTP:

- SAP BW 选择性删除 （的通过使用任何筛选器条件的行）
- SAP BW 请求删除 （的错误请求）

SAP Open Hub 目标不是 （中所有 SAP BW 的支持包，自 2015年） 的数据市场控制数据目标。 因此，可以删除从多维数据集数据而无需更改 OHD 中的数据。 然后必须重新同步使用数据工厂的多维数据集数据：

1. （通过使用在 SAP 中完整 DTP） 在数据工厂中运行完整的提取。
2. 删除增量 DTP 的 Open Hub 表中的所有行。
3. 将增量 DTP 状态设置为**已提取**。

在此之后，所有后续的增量 DTPs 和数据工厂增量提取按预期方式工作。

可将增量 DTP 状态设置为**已提取**，可以使用以下选项来手动运行增量 DTP:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>后续步骤

了解有关 SAP BW Open Hub 连接器支持：

> [!div class="nextstepaction"]
>[SAP 业务仓库 Open Hub 连接器](connector-sap-business-warehouse-open-hub.md)
