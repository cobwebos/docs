---
title: 使用导入到 Microsoft Excel 中的 Azure 数据资源管理器 Kusto 查询来可视化数据
description: 本文介绍如何将 Azure 数据资源管理器 Kusto 查询导入到 Microsoft Excel。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 55198e0c38c2922d69b68d9ce62e16ea25e9cc44
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173724"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>使用导入到 Microsoft Excel 中的 Azure 数据资源管理器 Kusto 查询来可视化数据

Azure 数据资源管理器提供两个选项用于连接到 Excel 中的数据：使用本机连接器或从 Azure 数据资源管理器导入查询。 本文介绍如何将查询从 Azure 数据资源管理器导入到 Excel 中，以可视化数据。 将 Kusto 查询添加为 Excel 数据源，以便对数据执行其他计算或可视化。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 作为 Azure Active directory 成员的组织电子邮件帐户，因此你可以连接到[azure 数据资源管理器帮助群集](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>或</br>
* 创建[测试群集和数据库](create-cluster-database-portal.md)，并登录到[AZURE 数据资源管理器 Web UI 应用程序](https://dataexplorer.azure.com/)。

## <a name="define-kusto-query-as-an-excel-data-source"></a>将 Kusto 查询定义为 Excel 数据源

1. 在[Azure 数据资源管理器 WEB UI](https://dataexplorer.azure.com/clusters/help/databases/Samples)中，运行查询并检查结果。

1. 选择 "**共享**" 选项卡，然后选择 "**查询" 以 Power BI**。

    ![要 Power BI 的 Web UI 查询](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. 此时会出现一个窗口，其中包含以下通知：

    ![将查询导出到剪贴板](media/excel-blank-query/query-exported-to-clipboard.png)

1. 打开 **Microsoft Excel**。

1. 在 "**数据**" 选项卡中，选择 "**从其他源** **获取数据** > " > "**空白查询**"。

    ![获取数据并选择空白查询](media/excel-blank-query/get-data-blank-query.png)

1. 此时将打开 " **Power Query 编辑器**" 窗口。 在窗口中，选择 "**高级编辑器**"。

    ![Power query 编辑器窗口](media/excel-blank-query/power-query-editor.png)

1. 在 "**高级编辑器**" 窗口中，将导出的查询粘贴到剪贴板，然后选择 "**完成**"。

    ![高级编辑器查询](media/excel-blank-query/advanced-editor-query.png)    

1. 若要进行身份验证，请选择 "**编辑凭据**"。

    ![编辑凭据](media/excel-blank-query/edit-credentials.png)

1. 选择 "**组织帐户** **" 并登录**。 完成登录过程，然后选择 "**连接**"。

    ![完成登录](media/excel-blank-query/complete-sign-in.png)

    重复上述步骤以添加更多查询。 可以将查询重命名为更有意义的名称。

1. 选择 "**关闭" & "加载**" 按钮，将数据导入 Excel。

    ![选择 "关闭并加载"](media/excel-blank-query/close-and-load.png)

1. 现在，你的数据在 Excel 中。 选择 "**刷新**" 按钮以刷新查询。

    ![在 excel 中查看数据](media/excel-blank-query/data-in-excel.png)