---
title: 管理 Azure Data Lake Analytics 帐户策略
description: 了解如何使用帐户策略来控制 Data Lake Analytics 帐户的使用情况，例如最大的澳大利亚和最大作业数。
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966432"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>使用帐户策略管理 Azure Data Lake Analytics

帐户策略有助于控制使用 Azure Data Lake Analytics 帐户的资源。 这些策略允许你控制使用 Azure Data Lake Analytics 的成本。 例如，通过这些策略，你可以通过限制帐户同时使用多少个 AU 来防止成本超出预期。

## <a name="account-level-policies"></a>帐户级策略

这些策略适用于 Data Lake Analytics 帐户中的所有作业。

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Data Lake Analytics 帐户中的最大 AU 数
由策略控制 Data Lake Analytics 帐户可使用的分析单位 (AU) 总数。 默认情况下，该值设置为 250。 例如，如果此值设置为 250 个 AU，可以运行一个作业，向它分配 250 个 AU；也可以运行 10 个作业，每个作业分配 25 个 AU。 提交的其他作业将进行排队，直到完成正在运行的作业。 正在运行的作业完成后，释放 AU，用于要运行的已排队作业。

更改 Data Lake Analytics 帐户的 AU 数：

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 单击 "**限制和策略**"。
3. 在“最大 AU 数”下，移动滑块选择一个值，或在文本框中输入值。 
4. 单击“保存”。

> [!NOTE]
> 如果需要的数量大于默认 AU 数 (250)，在门户中单击“帮助 + 支持”提交支持请求。 可增加 Data Lake Analytics 帐户中可用的 AU 数。
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>可同时运行的最大作业数
此策略限制可以同时运行的作业数。 默认情况下，此值设置为 20。 如果 Data Lake Analytics 有 AU 可用，将立即计划运行新的作业，直到运行作业的总数到达此策略的值。 当你达到可同时运行的作业的最大数量时，后续作业将按优先级顺序排队，直到一个或多个正在运行的作业完成（取决于可用的澳大利亚）。

更改可同时运行的作业数：

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 单击 "**限制和策略**"。
3. 在“运行作业的最大数”下，移动滑块选择一个值，或在文本框中输入值。 
4. 单击“保存”。

> [!NOTE]
> 如果需运行的作业数大于默认作业数 (20)，请在门户中单击“帮助 + 支持”提交支持请求。 可增加 Data Lake Analytics 帐户中可同时运行的作业数。
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>保留作业元数据和资源的时长 
如果用户运行的是 U-SQL 作业，则 Data Lake Analytics 服务将保留所有相关文件。 这些文件包括： U SQL 脚本、在 U SQL 脚本中引用的 DLL 文件、编译的资源和统计信息。 文件位于默认 Azure Data Lake 存储帐户的 /system/ 文件夹中。 此策略控制要将这些资源存储多长时间，再自动删除它们（默认值为 30 天）。 可使用这些文件对将来要重新运行的作业进行调试和性能优化。

更改保留作业元数据和资源的时长：

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 单击 "**限制和策略**"。
3. 在“保留作业查询的天数”下，移动滑块选择一个值，或在文本框中输入值。  
4. 单击“保存”。

## <a name="job-level-policies"></a>作业级策略

作业级策略允许您控制每个用户（或特定安全组的成员）可以在其提交的作业上设置的最大值和最大优先级。 此策略可以控制用户所产生的成本。 它还允许控制计划作业可能对在同一 Data Lake Analytics 帐户中的运行且优先级较高的生产作业产生的影响。

Data Lake Analytics 有两个可在作业级别设置的策略：

* 每个作业的 AU 限制：用户只可以提交所含 AU 数不超过此数字的作业。 默认情况下，此限制与帐户的最大 AU 限制相同。
* 优先级：用户只可提交优先级低于或等于此值的作业。 数字越大优先级越低。 默认情况下，此限制值设置为 1，这是可能的最高优先级。

对每个帐户都设有默认策略。 默认策略适用于帐户的所有用户。 您可以为特定用户和组创建其他策略。 

> [!NOTE]
> 帐户级策略和作业级策略同时应用。
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>添加用于特定用户或组的策略

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 单击 "**限制和策略**"。
3. 在“作业提交限制”下，单击“添加策略”按钮。 然后选择或输入以下设置：
    1. 计算策略名称：输入策略名称，提醒策略的用途。
    2. 选择用户或组：选择此策略适用的用户或组。
    3. 设置作业 AU 限制：设置适用于所选用户或组的 AU 限制。
    4. 设置优先级限制：设置适用于所选用户或组的优先级限制。

4. 单击“确定”。

5. 新策略在“默认”策略表的“作业提交限制”下列出。 

### <a name="delete-or-edit-an-existing-policy"></a>删除或编辑现有策略

1. 在 Azure 门户中转到 Data Lake Analytics 帐户。
2. 单击 "**限制和策略**"。
3. 在“作业提交限制”下找到要编辑的策略。
4.  若要查看“删除”和“编辑”选项，请在表的最右侧的列中，单击“`...`”。

## <a name="additional-resources-for-job-policies"></a>有关作业策略的其他资源
* [策略概述博客文章](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [帐户级策略博客文章](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [作业级策略博客文章](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [使用 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)