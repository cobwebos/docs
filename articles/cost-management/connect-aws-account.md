---
title: 将 Amazon Web Services 帐户连接到 Azure 成本管理 | Microsoft Docs
description: 连接 Amazon Web Services 帐户以便在成本管理报表中查看成本和用量数据。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0c18fc065ae4d9a9401a8d603f051e9d6236c538
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="connect-an-amazon-web-services-account"></a>连接 Amazon Web Services 帐户

可以使用两个选项将 Amazon Web Services (AWS) 帐户连接到 Azure 成本管理。 可以使用 IAM 角色或只读的 IAM 用户帐户进行连接。 建议使用 IAM 角色，因为这样可以将定义的访问权限委托给可信的实体。 IAM 角色不要求共享长期访问密钥。 将 AWS 帐户连接到成本管理后，成本管理报表中会提供成本和用量数据。 本文档将逐步讲解如何使用这两个选项。

有关 AWS IAM 标识的详细信息，请参阅[标识（用户、组和角色）](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)。

此外，本文还会介绍如何启用详细的 AWS 计费报告，并将信息存储在 AWS 简单存储服务 (S3) 存储桶中。 详细计费报告包括每小时计费以及标记和资源信息。 存储报告可让成本管理从存储桶中检索这些信息，并在其报告中显示信息。


## <a name="aws-role-based-access"></a>基于 AWS 角色的访问

以下部分逐步讲解如何创建一个只读的 IAM 角色，以提供对成本管理的访问。

### <a name="get-your-cost-management-account-external-id"></a>获取成本管理帐户外部 ID

第一个步骤是从 Azure 成本管理门户中获取唯一的连接通行短语。 该通行短语在 AWS 中用作**外部 ID**。

1. 可以从 Azure 门户打开 Cloudyn 门户，也可以导航到 [https://azure.cloudyn.com](https://azure.cloudyn.com) 并登录。
2. 单击齿轮符号，然后选择“云帐户”。
3. 在“帐户管理”中选择“AWS 帐户”选项卡，然后单击“新增 +”。
4. 在“添加 AWS 帐户”对话框中，复制“外部 ID”并保存此值，以便在下一部分创建 AWS 角色时使用。 “外部 ID”是帐户唯一的 ID。 在下图中，示例外部 ID 是 _Contoso_ 后接一个数字。 你的 ID 与此不同。  
    ![外部 ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>添加基于 AWS 只读角色的访问

1. 通过 https://console.aws.amazon.com/iam/home 登录到 AWS 控制台，选择“角色”。
2. 单击“创建角色”，然后选择“另一个 AWS 帐户”。
3. 在“帐户 ID”框中粘贴 `432263259397`。 此帐户 ID 是 AWS 分配给 Cloudyn 服务的成本管理数据收集器帐户。 使用与显示内容完全相同的帐户 ID。
4. 在“选项”旁边，选择“需要外部 ID”。 粘贴前面从成本管理中的“外部 ID”字段复制的唯一值。 然后单击“下一步: 权限”。  
    ![创建角色](./media/connect-aws-account/create-role01.png)
5. 在“附加权限策略”下面的“策略类型”筛选搜索框中键入 `ReadOnlyAccess`，选择“ReadOnlyAccess”，然后单击“下一步: 查看”。  
    ![只读访问](./media/connect-aws-account/readonlyaccess.png)
6. 在“查看”页上，确保选择正确无误，然后键入一个**角色名称**。 例如 *Azure-Cost-Mgt*。输入**角色说明**， 例如“Azure 成本管理的角色分配”，然后单击“创建角色”。
7. 在“角色”列表中单击创建的角色，并复制“摘要”页中显示的“角色 ARN”值。 稍后在 Azure 成本管理中注册配置时，需要使用该“角色 ARN”（Amazon 资源名称）值。  
    ![角色 ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>在成本管理中配置 AWS IAM 角色访问

1. 可以从 Azure 门户打开 Cloudyn 门户，也可以导航到 https://azure.cloudyn.com/ 并登录。
2. 单击齿轮符号，然后选择“云帐户”。
3. 在“帐户管理”中选择“AWS 帐户”选项卡，然后单击“新增 +”。
4. 在“帐户名称”中，键入帐户的名称。
5. 在“访问类型”旁边，选择“IAM 角色”。
6. 在“角色 ARN”字段中，粘贴先前复制的值，然后单击“保存”。  
    ![添加 AWS 帐户框](./media/connect-aws-account/add-aws-account-box.png)


AWS 帐户显示在帐户列表中。 列出的“所有者 ID”与“角色 ARN”值匹配。 “帐户状态”应带有一个绿色勾选标记符号，指示成本管理可以访问你的 AWS 帐户。 启用详细 AWS 计费之前，合并状态会显示为“独立”。

![AWS 帐户状态](./media/connect-aws-account/aws-account-status01.png)

成本管理将开始收集数据并填充报表。 接下来，[启用详细 AWS 计费](#enable-detailed-aws-billing)。


## <a name="aws-user-based-access"></a>基于 AWS 用户的访问

以下部分逐步讲解如何创建一个只读的用户，以提供对成本管理的访问。

### <a name="add-aws-read-only-user-based-access"></a>添加基于 AWS 只读用户的访问

1. 通过 https://console.aws.amazon.com/iam/home 登录到 AWS 控制台，选择“用户”。
2. 单击“添加用户”。
3. 在“用户名”字段中键入用户名。
4. 对于“访问类型”，请选择“以编程方式访问”，然后单击“下一步: 权限”。  
    ![添加用户](./media/connect-aws-account/add-user01.png)
5. 对于权限，请选择“直接附加现有策略”。
6. 在“附加权限策略”下面的“策略类型”筛选搜索框中键入 `ReadOnlyAccess`，选择“ReadOnlyAccess”，然后单击“下一步: 查看”。  
    ![设置用户的权限](./media/connect-aws-account/set-permission-for-user.png)
7. 在“查看”页上，确保选择正确无误，然后单击“创建用户”。
8. “完成”页上显示了“访问密钥 ID”和“机密访问密钥”。 可以使用这些信息在成本管理中配置注册。
9. 单击“下载 .csv”，并将 credentials.csv 文件保存到安全的位置。  
    ![下载凭据](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>在成本管理中配置基于 AWS IAM 用户的访问

1. 可以从 Azure 门户打开 Cloudyn 门户，也可以导航到 https://azure.cloudyn.com/ 并登录。
2. 单击齿轮符号，然后选择“云帐户”。
3. 在“帐户管理”中选择“AWS 帐户”选项卡，然后单击“新增 +”。
4. 对于“帐户名称”，请键入帐户名称。
5. 在“访问类型”旁边，选择“IAM 用户”。
6. 在“访问密钥”中，粘贴 credentials.csv 文件中的“访问密钥 ID”值。
7. 在“机密密钥”中，粘贴 credentials.csv 文件中的“机密访问密钥”值，然后单击“保存”。  

AWS 帐户显示在帐户列表中。 “帐户状态”应该附带一个绿色的勾选符号。

成本管理将开始收集数据并填充报表。 接下来，[启用详细 AWS 计费](#enable-detailed-aws-billing)。

## <a name="enable-detailed-aws-billing"></a>启用详细 AWS 计费

使用以下步骤获取 AWS 角色 ARN。 使用角色 ARN 授予对计费存储桶的读取权限。

1. 通过 https://console.aws.amazon.com 登录到 AWS 控制台，选择“服务”。
2. 在“服务搜索”框中键入 *IAM*，并选择该选项。
3. 从左侧菜单中选择“角色”。
4. 在“角色”列表中，选择为授予 Cloudyn 访问权限而创建的角色。
5. 在“角色摘要”页上，单击相应的按钮复制“角色 ARN”。 请保存好角色 ARN，以便在后面的步骤中使用。

### <a name="create-an-s3-bucket"></a>创建 S3 存储桶

创建一个 S3 存储桶用于存储详细计费信息。

1. 通过 https://console.aws.amazon.com 登录到 AWS 控制台，选择“服务”。
2. 在“服务搜索”框中键入 *S3*，并选择“S3”。
3. 在“Amazon S3”页上，单击“创建存储桶”。
4. 在“创建存储桶”向导中选择存储桶名称和区域，然后单击“下一步”。  
    ![创建存储桶](./media/connect-aws-account/create-bucket.png)
5. 在“设置属性”页上保留默认值，然后单击“下一步”。
6. 在“检查”页上，单击“创建存储桶”。 此时会显示存储桶列表。
7. 单击创建的存储桶，依次选择“权限”选项卡和“存储桶策略”。 此时会打开存储桶策略编辑器。
8. 复制以下 JSON 示例，并将其粘贴到存储桶策略编辑器中。
  - 将 `<BillingBucketName>` 替换为 S3 存储桶的名称。
  - 将 `<ReadOnlyUserOrRole>` 替换为前面复制的角色或用户 ARN。

  ```
  {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
  }
  ```

9. 单击“ **保存**”。  
    ![存储桶策略编辑器](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>启用 AWS 计费报告

创建并配置 S3 存储桶后，在 AWS 控制台中导航到[计费首选项](https://console.aws.amazon.com/billing/home?#/preference)。

1. 在“首选项”页上，选择“接收计费报告”。
2. 在“接收计费报告”下，输入创建的存储桶的名称，然后单击“验证”。  
3. 选择所有四个报告粒度选项，然后单击“保存首选项”。  
    ![启用报告](./media/connect-aws-account/enable-reports.png)

启用详细计费后，成本管理将从 S3 存储桶检索详细计费信息并填充报告。 最长可能需要在 24 小时后，详细计费数据才会显示在 Cloudyn 控制台中。 提供详细计费数据后，帐户合并状态将显示为“已合并”。 帐户状态显示为“已完成”。

![帐户合并状态](./media/connect-aws-account/consolidated-status.png)

某些优化报告可能需要几天的数据才能获得足够的数据样本大小，以生成准确的建议。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 成本管理，请继续学习成本管理的[查看使用量和成本](tutorial-review-usage.md)教程。
