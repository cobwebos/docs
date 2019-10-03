---
title: 使用 Power BI 分析 Azure Data Lake Storage Gen2 中的数据 | Microsoft Docs
description: 使用 Power BI 分析 Azure Data Lake Storage Gen2 中存储的数据
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: d76ea317271ae0e8eb0d54fcfee5dc005d836fc1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984962"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>使用 Power BI 分析 Azure Data Lake Storage Gen2 中的数据

本文介绍如何使用 Power BI Desktop 分析和可视化某个存储帐户中存储的数据，该帐户有分层命名空间 (Azure Data Lake Storage Gen2)。

## <a name="prerequisites"></a>先决条件

在开始阅读本教程前，必须具有：

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 一个采用分层命名空间的存储帐户。 按[这些](data-lake-storage-quickstart-create-account.md)说明创建一个。
> 阅读本文的前提是已创建名为 `myadlsg2` 的帐户。
> * 名为 `Drivers.txt` 的示例数据文件位于存储帐户中。
> 可以从 [Azure Data Lake Git 存储库](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)下载此示例，然后将该文件上传到存储帐户。
> * **Power BI Desktop**。 可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=45331) 进行下载。 

## <a name="create-a-report-in-power-bi-desktop"></a>在 Power BI Desktop 中创建报表

1. 在计算机上启动 Power BI Desktop。
2. 在功能区的“主页”选项卡中单击“获取数据”，然后单击“详细信息”。
3. 在“获取数据”对话框中，依次单击“Azure”、“Azure Data Lake Store Gen2 (Beta)”、“连接”。

    ![“获取数据”页](media/data-lake-storage-use-power-bi/get-data-page.png)

4. 在 " **Azure Data Lake Storage Gen2** " 对话框中，可以使用容器终结点格式向 Azure Data Lake Storage Gen2 帐户、文件系统或子文件夹提供 URL。 确保 Data Lake Storage Gen2 的 URL 的模式为 `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>`，然后单击“确定”。

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. 在下一个对话框中单击“登录”，登录到存储帐户。 你会被重定向到组织的登录页面。 按照提示登录到该帐户。

    ![登录页](media/data-lake-storage-use-power-bi/sign-in.png)

6. 登录成功后，单击“连接”。

    ![“已登录”页](media/data-lake-storage-use-power-bi/signed-in.png)

7. 下一个对话框显示在上面的步骤 4 中提供的 URL 下的所有文件，其中包括已上传到存储帐户的文件。 验证信息，然后单击“加载”。

    ![文件系统](media/data-lake-storage-use-power-bi/file-systems.png)

8. 数据成功加载到 Power BI 后，“字段”选项卡会出现以下字段。

    ![“字段”选项卡](media/data-lake-storage-use-power-bi/fields.png)

    但是，若要可视化和分析数据，最好以下每个字段都可用该数据。

    ![字段](media/data-lake-storage-use-power-bi/preferred-fields.png)

    后续步骤会更新查询，以按所需格式转换已导入的数据。

9. 在功能区的“主页”选项卡中，单击“编辑查询”。

    ![查询](media/data-lake-storage-use-power-bi/queries.png)

10. 在“查询编辑器”中的“内容”列，单击“二进制”。 系统会自动将此文件检测为 CSV，你会出现如下所示的输出。 数据现在已是可用于创建可视化的格式。

    ![Output](media/data-lake-storage-use-power-bi/binary.png)

11. 在功能区的“主页”选项卡中，单击“关闭并应用”，然后再次单击“关闭并应用”。

    ![关闭并应用](media/data-lake-storage-use-power-bi/close-apply.png)

12. 查询更新后，“字段”选项卡会显示可用于可视化的新字段。

    ![新字段](media/data-lake-storage-use-power-bi/new-fields.png)

13. 创建一个表示特定国家/地区的每个城市中司机的饼图。 为此，请如下进行选择。

    在“可视化”选项卡中，单击饼图符号。

    ![可视化效果](media/data-lake-storage-use-power-bi/visualizations.png)

    将使用到的列是“列 4”（城市名称）和“列 7”（国家/地区名称）。 如下所示，从“字段”选项卡将这些列拖动到“可视化”选项卡。

    ![拖动字段](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    此时饼图应如下所示。

    ![饼图](media/data-lake-storage-use-power-bi/pie-chart.png)

14. 通过在页面级别筛选器中选择一个特定的国家/地区，此时会显示所选国家/地区的每个城市中的司机数量。 例如，在“可视化”选项卡中的“页面级别筛选器”中，选择“巴西”。

    ![页面筛选器](media/data-lake-storage-use-power-bi/page-filters.png)

15. 饼图会自动更新，显示巴西各个城市中的司机。

    ![巴西](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. 在“文件”菜单上，单击“保存”将可视化对象另存为 Power BI Desktop 文件。

## <a name="publish-report-to-power-bi-service"></a>将报表发布到 Power BI 服务

在 Power BI Desktop 中创建可视化后，可将其发布到 Power BI 服务中与他人共享。 有关如何发布的说明，请参阅[从 Power BI Desktop 发布](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)。
