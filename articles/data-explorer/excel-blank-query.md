---
title: 使用导入到 Microsoft Excel 中的 Azure 数据资源管理器 Kusto 查询进行数据可视化
description: 本文介绍如何将 Azure 数据资源管理器 Kusto 查询导入到 Microsoft Excel 中。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849082"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>使用导入到 Microsoft Excel 中的 Azure 数据资源管理器 Kusto 查询进行数据可视化

Azure 数据资源管理器提供两个可以在 Excel 中连接到数据的选项：使用本机连接器或从 Azure 数据资源管理器导入查询。 本文介绍如何将查询从 Azure 数据资源管理器导入 Excel 以实现数据可视化。 将 Kusto 查询添加为 Excel 数据源，以便对数据进行其他计算或可视化操作。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 属于 Azure 活动目录的组织电子邮件帐户，因此您可以连接到[Azure 数据资源管理器帮助群集](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>或</br>
* 创建[测试群集和数据库](create-cluster-database-portal.md)并登录到 [Azure 数据资源管理器 Web UI 应用程序](https://dataexplorer.azure.com/)。

## <a name="define-kusto-query-as-an-excel-data-source"></a>将 Kusto 查询定义为 Excel 数据源

1. 在 [Azure 数据资源管理器 Web UI](https://dataexplorer.azure.com/clusters/help/databases/Samples) 中，运行此查询并查看结果。

1. 选择“共享”选项卡，然后选择“向 Power BI 查询”。********

    ![向 Power BI 进行 Web UI 查询](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. 此时会显示一个包含以下通知的窗口：

    ![将查询导出到剪贴板](media/excel-blank-query/query-exported-to-clipboard.png)

1. 打开 **Microsoft Excel**。

1. 在“数据”**** 选项卡中，选择“获取数据”**** > “从其他源”**** > ****“空白查询”。

    ![获取数据并选择空白查询](media/excel-blank-query/get-data-blank-query.png)

1. 此时会打开“Power Query 编辑器”窗口。**** 在窗口中，选择“高级编辑器”。****

    ![“Power Query 编辑器”窗口](media/excel-blank-query/power-query-editor.png)

1. 在“高级编辑器”窗口中，将导出的查询粘贴到剪贴板，然后选择“完成”。********

    ![高级编辑器查询](media/excel-blank-query/advanced-editor-query.png)    

1. 若要进行身份验证，请选择“编辑凭据”。****

    ![编辑凭据](media/excel-blank-query/edit-credentials.png)

1. 选择“组织帐户”，然后选择“登录”。******** 完成登录过程，然后选择“连接”****。

    ![完成登录](media/excel-blank-query/complete-sign-in.png)

    重复上述步骤，添加更多查询。 可以将查询重命名为更有意义的名称。

1. 选择“关闭并加载”按钮，将数据导入 Excel。****

    ![选择“关闭并加载”](media/excel-blank-query/close-and-load.png)

1. 现在，数据位于 Excel 中。 使用“刷新”按钮，刷新查询。****

    ![查看 Excel 中的数据](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>后续步骤

[使用 Excel 的 Azure 数据资源管理器连接器进行数据可视化](excel-connector.md)