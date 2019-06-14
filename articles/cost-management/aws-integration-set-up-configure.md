---
title: 设置和使用 Azure 成本管理中配置 AWS 成本和使用情况报表集成
description: 本文将指导你完成设置和使用 Azure 成本管理中配置 AWS 成本和使用情况报表集成。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002129"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>设置和配置 AWS 成本和使用情况报表集成

与 Amazon Web Services (AWS) 可以监视并控制在 Azure 成本管理中 AWS 支出的成本和使用情况报告 （当前） 集成。 集成，可以在其中监视的 Azure 门户和 Azure 和 AWS 支出的控件中单个位置。 此文章介绍了如何设置集成并将其配置，以便使用 Azure 成本管理功能来分析成本和查看预算。

成本管理流程通过 AWS 访问凭据来获取报表定义和下载的报表 GZIP CSV 文件存储在一个 S3 存储桶的 AWS 成本和使用情况报告。

## <a name="create-a-cost-and-usage-report-in-aws"></a>在 AWS 中创建的成本和使用情况报表

使用成本和使用情况报表是 AWS 建议的方法来收集和处理 AWS 成本。 有关详细信息，请参阅[AWS 成本和使用情况报告](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)文档。

使用**成本和使用情况报表**计费和成本管理控制台在 AWS 中使用以下步骤创建的成本和使用情况报表页：

1. 登录到 AWS 管理控制台并打开[计费和成本管理控制台](https://console.aws.amazon.com/billing)。
2. 在导航窗格中，选择**成本和使用情况报表**。
3. 选择**创建报表**。
4. 有关**报表名称**，输入您的报表的名称。
5. 有关**另一个报表的详细信息**，以包括在报表中，选择每个资源 Id**包括资源 Id**。
6. 有关**的数据刷新设置**中，选择是否要刷新如果 AWS 应用退款，信用额度，AWS 成本和使用情况报表或正在完成你的帐单后，支持你的帐户的费用。 报表刷新时，新的报表上传到 Amazon S3。 建议在将此设置。
7. 选择“**下一步**”。
8. 有关**S3 存储桶**，选择**配置**。
9. 在配置 S3 存储桶对话框中，执行以下操作：
    1. 从下拉列表中选择现有的 bucket，然后选择**下一步**。
    2. 输入存储桶名称以及你想要创建新的存储桶，并选择的区域**下一步**。
10. 选择我确认此策略已更正，然后选择保存。
11. （可选）对于报表路径前缀，输入要预置到报表的名称的报表路径前缀。
如果未指定前缀，默认的前缀是您指定在步骤 4 中的报表和报表，采用以下格式的日期范围的名称： `/report-name/date-range/`
12. 有关**时间单位**，选择**每小时**。
13. 有关**报表版本控制**，选择是否想要覆盖以前版本的报表，或传递除了以前版本的报表的每个版本。
14. 有关**启用数据集成以便进行**，没有选定内容是必需的。
15. 有关**压缩**，选择**GZIP**。
16. 选择“**下一步**”。
17. 已为您的报表中查看设置后，请选择**评审和已完成**。

    请注意报表名称。 将在后续步骤中使用它。

可能需要 24 小时 AWS 着手将报表传递到 Amazon S3 存储桶。 传递开始后，AWS 更新 AWS 成本和使用情况报表文件至少一次一天。 你可以继续配置 AWS 环境而无需等待交付来启动。

## <a name="create-a-role-and-policy-in-aws"></a>在 AWS 中创建角色和策略

Azure 成本管理访问成本和使用情况报表所在的位置每天多次的 S3 存储桶。 该服务需要凭据以检查有新的数据访问。 在 AWS 以允许访问它的成本管理中创建角色和策略。

若要启用基于角色的访问成本管理中的 AWS 帐户，请在 AWS 控制台中创建角色。 需要能够_角色 ARN_并_外部 ID_从 AWS 控制台。 更高版本上使用它们**创建 AWS 连接器**在成本管理中的页。

使用创建新的角色向导：

1. 登录到 AWS 控制台，然后选择**Services**。
2. 在服务列表中，选择**IAM**。
3. 选择**角色**，然后选择**Create Role**。
4. 在下一页上，选择**另一个 AWS 帐户**。
5. 在中**帐户 ID**，输入**432263259397**。
6. 在中**选项**，选择**需要外部 ID （第三方将假定此角色时，最佳做法）** 。
7. 在中**外部 ID**，输入外部 id。 外部 ID 是 AWS 角色和 Azure 成本管理之间共享的密码。 在也使用相同的外部 ID**新的连接器**在成本管理中的页。 例如，类似于外部 ID _Companyname1234567890123_。

    > [!NOTE]
    > 不会更改为所选内容**要求使用 MFA**。 它应保留已清除。
8. 在完成时选择“下一步:  权限”。
9. 选择**创建策略**。 此时会打开新浏览器标签。 这是你在其中创建策略。
10. 选择**选择服务**。

配置权限的成本和使用情况报告：

1. 输入**成本和使用情况报告**。
2. 选择**访问级别** > **读取** > **DescribeReportDefinitions**。 此步骤可让成本管理来读取定义哪些 CUR 报告并确定它们是否匹配到报表定义系统必备组件。
3. 选择**将其他权限添加**。

配置 S3 存储桶和对象的权限：

1. 选择**选择服务**。
2. 输入**S3**。
3. 选择**访问级别** > **列表** > **ListBucket**。 此操作在 S3 存储桶中获取的对象的列表。
4. 选择**访问级别** > **读取** > **GetObject**。 此操作允许计费的文件的下载。
5. 选择**资源**。
6. 选择**存储桶 – 添加 ARN**。
7. 在中**存储桶名称**，输入用于存储当前文件的存储桶。
8. 选择**对象 – 添加 ARN**。
9. 在中**存储桶名称**，输入用于存储当前文件的存储桶。
10. 在中**对象名称**，选择**任何**。
11. 选择**将其他权限添加**。

配置成本资源管理器的权限：

1. 选择**选择服务**。
2. 输入**成本资源管理器服务**。
3. 选择**所有成本资源管理器服务动作 (ce:\*)** 。 此操作将验证集合正确。
4. 选择**将其他权限添加**。

添加 AWS 组织的权限：

1. 输入**组织**。
2. 选择**访问级别** > **列表** > **ListAccounts**。 此操作获取的帐户的名称。
3. 在中**查看策略**，输入新策略的名称。 检查输入正确的信息，然后选择**创建策略**。
4. 返回到上一个选项卡并刷新浏览器的网页。 在搜索栏中，搜索新策略。
5. 选择**下一步： 查看**。
6. 输入新角色的名称。 检查输入正确的信息，然后选择**Create Role**。

    请注意，角色 ARN 和外部 ID 前面的步骤中创建角色时。 设置 Azure 成本管理连接器时，你将从更高版本使用它们。

策略 JSON 应类似于下面的示例。 替换_bucketname_ S3 存储桶的名称。

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

## <a name="set-up-a-new-aws-connector-in-azure"></a>设置在 Azure 中的新 AWS 连接器

使用以下信息创建 AWS 连接器并开始监视你的 AWS 成本：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 转到**成本管理 + 计费** > **成本管理**。
3. 下**设置**，选择**云连接器 （预览版）** 。  
    ![显示云连接器 （预览版） 示例设置)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)。
4. 选择 **+ 添加**顶部的创建连接器页。
5. 上**创建 AWS 连接器**页上，在**显示名称**，输入你的连接器的名称。  
    ![创建 AWS 连接器的页的示例](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. （可选） 选择的默认管理组。 它将存储所有已发现的链接的帐户。 您可以将其设置更高版本。
7. 在中**计费**部分中，选择**自动收费公开上市时 1%** 如果你想要在预览过期时确保连续运营。 如果选择自动选项，则必须选择计费订阅。
8. 有关**角色 ARN**，输入您设置在 AWS 中的角色时使用的值。
9. 有关**外部 ID**，输入您设置在 AWS 中的角色时使用的值。
10. 有关**报表名称**，输入在 AWS 中创建的名称。
11. 选择**下一步**，然后选择**创建**。

可能需要几个小时的新 AWS 作用域，AWS 整合的帐户和链接的 AWS 帐户，以及要显示其成本数据。

创建连接器后，我们建议为其分配访问控制。 用户权限分配给新发现作用域：AWS 合并帐户和 AWS 链接帐户。 创建连接器的用户是连接器、 合并的帐户和所有链接的帐户的所有者。

将连接器权限分配给用户，发现发生后不将权限分配给现有的 AWS 作用域。 相反，唯一的新链接的帐户分配权限。

## <a name="take-additional-steps"></a>执行其他步骤

- [将管理组设置](../governance/management-groups/index.md#initial-setup-of-management-groups)，如果你尚未准备好。
- 检查新的作用域都添加到作用域选择器。 选择**刷新**若要查看最新数据。
- 上**云连接器**页上，选择你的连接器并选择**转到计费帐户**将链接的帐户分配到管理组。

## <a name="manage-cloud-connectors"></a>管理云连接器

当上选择一个连接器**云连接器**页上，你可以：

- 选择**转到计费帐户**要查看的 AWS 信息合并的帐户。
- 选择**访问控制**管理连接器角色分配。
- 选择**编辑**更新连接器。 无法更改 AWS 帐户号，因为它出现在角色 ARN。 但是，您可以创建一个新的连接器。
- 选择**验证**重新运行验证测试，以确保成本管理，可以使用连接器设置收集数据。

![创建 AWS 连接器的示例列表](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>设置 Azure 管理组

若要创建在一个位置即可查看跨云提供程序的信息，您需要将你的 Azure 订阅和链接的 AWS 帐户放在相同的管理组中。 如果你尚未使用管理组配置 Azure 环境，请参阅[初始的管理组设置](../governance/management-groups/index.md#initial-setup-of-management-groups)。

如果你想要单独的成本，可以创建包含只是链接的 AWS 帐户的管理组。

## <a name="set-up-an-aws-consolidated-account"></a>设置 AWS 合并帐户

合并的 AWS 帐户结合了计费和付款的多个 AWS 帐户。 它还可作为链接的 AWS 帐户。

![AWS 示例的详细信息合并帐户](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

从页中，你可以：

- 选择**更新**要批量更新 AWS 的关联链接与管理组的帐户。
- 选择**访问控制**设置作用域的角色分配。

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS 的权限合并的帐户

默认情况下，该帐户的创建后，基于 AWS 连接器权限设置的权限合并的 AWS 帐户。 连接器创建者是所有者。

使用管理的访问级别**访问级别**AWS 页合并的帐户。 但是，AWS 链接帐户不继承对合并的 AWS 帐户的权限。

## <a name="set-up-an-aws-linked-account"></a>设置链接的 AWS 帐户

链接的 AWS 帐户是其中创建和管理 AWS 资源。 链接的帐户还充当安全边界。

在此页上，你可以：

- 选择**更新**要更新的 AWS 关联链接与管理组的帐户。
- 选择**访问控制**设置作用域的角色分配。

![AWS 链接帐户页的示例](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>AWS 的权限链接帐户

默认情况下，创建后，基于 AWS 连接器权限设置的链接的 AWS 帐户的权限。 连接器创建者是所有者。 使用管理的访问级别**访问级别**AWS 页链接帐户。 链接的 AWS 帐户不从合并的 AWS 帐户继承权限。

AWS 链接帐户始终从其所属的管理组中继承权限。

## <a name="next-steps"></a>后续步骤

- 现在，设置并配置 AWS 成本和使用情况报表集成后，继续执行[管理 AWS 成本和用量](aws-integration-manage.md)。
- 如果已熟悉成本分析，请参阅[探索和分析使用成本分析成本](quick-acm-cost-analysis.md)快速入门。
- 如果您熟悉在 Azure 中的预算，请参阅[创建和管理 Azure 预算](tutorial-acm-create-budgets.md)。
