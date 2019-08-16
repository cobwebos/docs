---
title: 设置和配置 AWS 成本和使用情况报告与 Azure 成本管理的集成
description: 本文逐步讲解如何设置和配置 AWS 成本和使用情况报告与 Azure 成本管理的集成。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 9664beca514abcbad4eca7c8f9dc1b494018802e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535182"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>设置和配置 AWS 成本和使用情况报告集成

使用 Amazon Web Services (AWS) 成本和使用情况报告 (当前) 集成, 你可以在 Azure 成本管理中监视和控制 AWS 支出。 集成允许 Azure 门户中的单个位置监视和控制 Azure 和 AWS 的支出。 本文介绍如何设置集成并对其进行配置, 以便可以使用 Azure 成本管理功能来分析成本和查看预算。

成本管理通过使用 AWS 访问凭据获取报表定义并下载报告 GZIP CSV 文件, 处理存储在 S3 存储桶中的 AWS 成本和使用情况报告。

## <a name="create-a-cost-and-usage-report-in-aws"></a>在 AWS 中创建成本和使用情况报告

使用成本和使用情况报表是 AWS 建议的收集和处理 AWS 成本的方式。 有关详细信息, 请参阅[AWS Cost 和使用情况报告](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)文档。

使用 AWS 中 "计费和成本管理" 控制台的 "**成本 & 使用情况报告**" 页, 可以创建成本和使用情况报告, 步骤如下:

1. 登录到 AWS 管理控制台并打开[计费和成本管理控制台](https://console.aws.amazon.com/billing)。
2. 在导航窗格中, 选择 "**成本 & 使用情况报告**"。
3. 选择 "**创建报表**"。
4. 对于 "**报表名称**", 请输入报表的名称。
5. 在 "**其他报表详细信息**" 下, 选择 "**包含资源 id**"。
6. 对于 "**数据刷新设置**", 请选择是否希望在最终完成帐单后, AWS 向帐户应用退款、信用或支持费用时, 是否需要刷新 AWS 成本和使用情况报告。 当报表刷新时, 将向 Amazon S3 上传新报表。 建议将设置保留为选中状态。
7. 选择“**下一步**”。
8. 对于**S3 存储桶**, 请选择 "**配置**"。
9. 在 "配置 S3 Bucket" 对话框中, 执行以下任务之一:
    1. 从下拉列表中选择现有存储桶, 然后选择 "**下一步**"。
    2. 输入 bucket 名称以及要在其中创建新存储桶的区域, 然后选择 "**下一步**"。
10. 选择 "**我已确认此策略正确**", 然后单击 "**保存**"。
11. 可有可无对于 "报表路径前缀", 请输入要在报表名称前附加的报表路径前缀。
如果未指定前缀, 则默认前缀是为报表指定的名称。 日期范围的格式为`/report-name/date-range/` 。
12. 对于**时间单位**, 选择 "**每小时**"。
13. 对于**报表版本控制**, 选择是希望报表的每个版本覆盖以前的版本, 还是要选择其他新报表。
14. 对于**启用的数据集成**, 无需选择。
15. 对于**压缩**, 请选择**GZIP**。
16. 选择“**下一步**”。
17. 查看报表设置后, 请选择 "**查看并完成**"。

    记下报表名称。 稍后步骤中将用到它。

AWS 可能需要长达24小时才能开始将报表传递到 Amazon S3 存储桶中。 送达开始后, AWS 将每日至少更新一次 AWS 成本和使用情况报告文件。 你可以继续配置你的 AWS 环境, 而无需等待传递开始。

## <a name="create-a-role-and-policy-in-aws"></a>在 AWS 中创建角色和策略

Azure 成本管理访问 S3 存储桶, 其中的成本和使用情况报告一天会多次进行。 服务需要访问凭据以检查是否有新数据。 在 AWS 中创建角色和策略, 以允许成本管理访问它。

若要对成本管理中的 AWS 帐户启用基于角色的访问, 请在 AWS 控制台中创建该角色。 需要从 AWS 控制台获得_ROLE ARN_和_external ID_ 。 稍后, 你可以在成本管理中的 "**创建 AWS 连接器**" 页上使用这些凭据。

使用 "创建新角色向导":

1. 登录到 AWS 控制台, 并选择 "**服务**"。
2. 在服务列表中, 选择 " **IAM**"。
3. 选择 "**角色**", 然后选择 "**创建角色**"。
4. 在下一页上, 选择**其他 AWS 帐户**。
5. 在 "**帐户 ID**" 中, 输入**432263259397**。
6. 在 "**选项**" 中, 选择 "**需要外部 ID" (第三方将担任此角色时的最佳做法)** 。
7. 在 "**外部 id**" 中, 输入外部 id。 外部 ID 是 AWS 角色与 Azure 成本管理之间共享的密码。 在成本管理中的**新连接器**页上也使用同一外部 ID。 例如, 外部 ID 类似于_Companyname1234567890123_。

    > [!NOTE]
    > 请勿更改 "**需要 MFA**" 的选择。 应保持清除状态。
8. 在完成时选择“下一步:权限”。
9. 选择 "**创建策略**"。 此时会打开新浏览器标签。 这就是创建策略的位置。
10. 选择 "**选择服务**"。

配置成本和使用情况报表的权限:

1. 输入**成本和使用情况报告**。
2. 选择**访问级别** > **Read** > **DescribeReportDefinitions**。 此步骤允许成本管理读取当前定义的报表, 并确定它们是否与报表定义必备项匹配。
3. 选择 "**添加其他权限**"。

为 S3 存储桶和对象配置权限:

1. 选择 "**选择服务**"。
2. 输入 " **S3**"。
3. 选择 "**访问级别** > "**列表** > **ListBucket**。 此操作获取 S3 存储桶中的对象的列表。
4. 选择 "**访问级别** > " "**读取** > **GetObject**"。 此操作允许下载计费文件。
5. 选择 "**资源**"。
6. 选择**bucket –添加 ARN**。
7. 在 " **bucket 名称**" 中, 输入用于存储当前文件的存储桶。
8. 选择**对象-添加 ARN**。
9. 在 " **bucket 名称**" 中, 输入用于存储当前文件的存储桶。
10. 在 "**对象名称**" 中, 选择**任何**。
11. 选择 "**添加其他权限**"。

为成本资源管理器配置权限:

1. 选择 "**选择服务**"。
2. 输入**成本资源管理器服务**。
3. 选择**所有成本资源管理器服务操作 (\*ce:)** 。 此操作验证集合是否正确。
4. 选择 "**添加其他权限**"。

为 AWS 组织添加权限:

1. 输入**组织**。
2. 选择 "**访问级别** > "**列表** > **ListAccounts**。 此操作获取帐户的名称。
3. 在 "**查看策略**" 中, 输入新策略的名称。 检查输入的信息是否正确, 然后选择 "**创建策略**"。
4. 返回到上一个选项卡并刷新浏览器的网页。 在搜索栏上, 搜索新策略。
5. 在完成时选择“下一步:查看”。
6. 输入新角色的名称。 检查输入的信息是否正确, 然后选择 "**创建角色**"。

    请注意在创建角色时上述步骤中使用的角色 ARN 和外部 ID。 稍后在设置 Azure 成本管理连接器时, 将使用这些设置。

策略 JSON 应类似于以下示例。 将_bucketname_替换为 S3 存储桶的名称。

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>在 Azure 中设置新的 AWS 连接器

使用以下信息创建 AWS 连接器并开始监视 AWS 成本:

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请参阅**成本管理 + 计费** > **成本管理**。
3. 在 "**设置**" 下, 选择 "**云连接器 (预览)** "。  
    ![显示云连接器 (预览) 设置](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)的示例。
4. 选择页面顶部的 " **+ 添加**" 以创建连接器。
5. 在 "**创建 AWS 连接器**" 页上的 "**显示名称**" 中, 输入连接器的名称。  
    ![用于创建 AWS 连接器的页面示例](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. 还可以选择默认管理组。 它将存储所有发现的链接帐户。 稍后可以进行设置。
7. 如果你想要确保在预览版过期时连续操作, 请在 "**计费**" 部分选择 "**在公开上市时自动向 1% 收费"** 。 如果选择 "自动" 选项, 则必须选择计费订阅。
8. 对于 "**角色 ARN**", 请输入在 AWS 中设置角色时使用的值。
9. 对于 "**外部 ID**", 输入在 AWS 中设置角色时使用的值。
10. 对于 "**报表名称**", 请输入你在 AWS 中创建的名称。
11. 选择 "**下一步**", 然后选择 "**创建**"。

可能需要花费几个小时才能出现新的 AWS 范围、AWS 合并帐户、AWS 链接的帐户以及要显示的成本数据。

创建连接器后, 建议为其分配访问控制。 向用户分配对新发现的作用域的权限:AWS 合并的帐户和 AWS 链接的帐户。 创建连接器的用户是连接器的所有者、合并的帐户以及所有链接的帐户。

如果在发现之后向用户分配连接器权限, 则不会将权限分配给现有的 AWS 范围。 相反, 只向新的链接帐户分配权限。

## <a name="take-additional-steps"></a>执行附加步骤

- [设置管理组](../governance/management-groups/index.md#initial-setup-of-management-groups)(如果尚未这样做)。
- 检查是否已将新的作用域添加到作用域选取器。 选择 "**刷新**" 以查看最新数据。
- 在 "**云连接器**" 页面上, 选择你的连接器, 然后选择 "**前往计费帐户**" 将链接的帐户分配到管理组。

## <a name="manage-cloud-connectors"></a>管理云连接器

当你在 "**云连接器**" 页面上选择某个连接器时, 你可以:

- 选择 "**前往计费帐户**" 以查看 AWS 合并帐户的信息。
- 选择 "**访问控制**" 以管理连接器的角色分配。
- 选择 "**编辑**" 以更新连接器。 不能更改 AWS 帐号, 因为它显示在角色 ARN 中。 但你可以创建新的连接器。
- 选择 "**验证**" 以重新运行验证测试, 以确保成本管理可以使用连接器设置来收集数据。

![已创建 AWS 连接器的示例列表](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>设置 Azure 管理组

将 Azure 订阅和 AWS 链接的帐户放置在同一个管理组中, 以创建一个可在其中查看跨云提供程序信息的位置。 如果尚未配置具有管理组的 Azure 环境, 请参阅[管理组的初始设置](../governance/management-groups/index.md#initial-setup-of-management-groups)。

如果要将成本分开, 可以创建一个管理组, 该管理组只保存 AWS 链接的帐户。

## <a name="set-up-an-aws-consolidated-account"></a>设置 AWS 合并帐户

AWS 合并帐户结合了多个 AWS 帐户的计费和付款。 它还充当 AWS 链接帐户。

![AWS 合并帐户的示例详细信息](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

在该页中, 您可以:

- 选择 "**更新**" 以批量更新 AWS 链接帐户与管理组的关联。
- 选择 "**访问控制**", 设置作用域的角色分配。

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS 合并帐户的权限

默认情况下, 根据 AWS 连接器权限, 在创建帐户时设置 AWS 合并帐户的权限。 连接器创建者是所有者。

你可以使用 AWS 合并帐户的 "**访问级别**" 页来管理访问级别。 但是, AWS 链接帐户不会继承 AWS 合并帐户的权限。

## <a name="set-up-an-aws-linked-account"></a>设置 AWS 链接帐户

AWS 链接帐户是在其中创建和管理 AWS 资源的位置。 链接帐户也充当安全边界。

从此页中, 你可以:

- 选择 "**更新**" 更新与管理组的 AWS 链接帐户关联。
- 选择 "**访问控制**", 设置作用域的角色分配。

![AWS 链接帐户页的示例](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>AWS 链接帐户的权限

默认情况下, 根据 AWS 连接器权限, 在创建时设置 AWS 链接帐户的权限。 连接器创建者是所有者。 可以使用 AWS 链接帐户的 "**访问级别**" 页来管理访问级别。 AWS 链接帐户不会从 AWS 合并帐户继承权限。

AWS 链接帐户始终从其所属的管理组继承权限。

## <a name="next-steps"></a>后续步骤

- 现在, 你已设置并配置了 AWS 成本和使用情况报告集成, 接下来请继续[管理 AWS 成本和使用情况](aws-integration-manage.md)。
- 如果不熟悉成本分析, 请参阅[利用成本分析快速入门探索和分析成本](quick-acm-cost-analysis.md)。
- 如果不熟悉 Azure 中的预算, 请参阅[创建和管理 azure 预算](tutorial-acm-create-budgets.md)。
