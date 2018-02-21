---
title: "将 Amazon Web Services 帐户连接到 Azure 成本管理 | Microsoft Docs"
description: "连接 Amazon Web Services 帐户以便在成本管理报表中查看成本和用量数据。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: a82d803b51859f809ca5a39ce177697a1f66a008
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>连接 Amazon Web Services 帐户

可以使用两个选项将 Amazon Web Services (AWS) 帐户连接到 Azure 成本管理。 可以使用 IAM 角色或只读的 IAM 用户帐户进行连接。 建议使用 IAM 角色，因为这样可以将定义的访问权限委托给可信的实体。 IAM 角色不要求共享长期访问密钥。 将 AWS 帐户连接到成本管理后，成本管理报表中会提供成本和用量数据。 本文档将逐步讲解如何使用这两个选项。

有关 AWS IAM 标识的详细信息，请参阅[标识（用户、组和角色）](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)。

## <a name="aws-role-based-access"></a>基于 AWS 角色的访问

以下部分逐步讲解如何创建一个只读的 IAM 角色，以提供对成本管理的访问。

### <a name="get-your-cost-management-account-external-id"></a>获取成本管理帐户外部 ID

第一个步骤是从 Azure 成本管理门户中获取唯一的连接通行短语。 该通行短语在 AWS 中用作**外部 ID**。

1. 从 Azure 门户打开 Cloudyn 门户，或者导航到 [https://azure.cloudyn.com](https://azure.cloudyn.com) 并登录。
2. 单击“设置”（齿轮图标），然后选择“云帐户”。
3. 在“帐户管理”中选择“AWS 帐户”选项卡，然后单击“新增 +”。
4. 在“添加 AWS 帐户”对话框中，复制“外部 ID”并保存此值，以便在下一部分创建 AWS 角色时使用。 在下图中，示例 ID 为 _Cloudyn_。 你的 ID 与此不同。  
    ![外部 ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>添加基于 AWS 只读角色的访问

1. 通过 https://console.aws.amazon.com/iam/home 登录到 AWS 控制台并选择“角色”。
2. 单击“创建角色”，然后选择“另一个 AWS 帐户”。
3. 在“帐户 ID”字段中粘贴标识 `432263259397`。 此帐户 ID 是必须使用的成本管理数据收集器帐户。
4. 在“选项”旁边，选择“需要外部 ID”，在“外部 ID”字段中粘贴先前复制的值，然后单击“下一步: 权限”。  
    ![创建角色](./media/connect-aws-account/create-role01.png)
5. 在“附加权限策略”下面的“策略类型”筛选搜索框中键入 `ReadOnlyAccess`，选择“ReadOnlyAccess”，然后单击“下一步: 查看”。  
    ![只读访问](./media/connect-aws-account/readonlyaccess.png)
6. 在“查看”页上，确保选择正确无误，然后键入一个**角色名称**。 例如 *Azure-Cost-Mgt*。输入**角色说明**， 例如“Azure 成本管理的角色分配”，然后单击“创建角色”。
7. 在“角色”列表中单击创建的角色，并复制“摘要”页中显示的“角色 ARN”值。 稍后在 Azure 成本管理中注册配置时，需要使用该“角色 ARN”值。  
    ![角色 ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>在成本管理中配置 AWS IAM 角色访问

1. 可以从 Azure 门户打开 Cloudyn 门户，或者导航到 https://azure.cloudyn.com/ 并登录。
2. 单击“设置”（齿轮图标），然后选择“云帐户”。
3. 在“帐户管理”中选择“AWS 帐户”选项卡，然后单击“新增 +”。
4. 在“帐户名称”中，键入帐户的名称。
5. 在“访问类型”旁边，选择“IAM 角色”。
6. 在“角色 ARN”字段中，粘贴先前复制的值，然后单击“保存”。  
    ![AWS 帐户状态](./media/connect-aws-account/aws-account-status01.png)

AWS 帐户显示在帐户列表中。 列出的“所有者 ID”与“角色 ARN”值匹配。 “帐户状态”应该附带一个绿色的勾选符号。

成本管理将开始收集数据并填充报表。 但是，某些优化报表可能需要先积累几天的数据，然后才能生成准确的建议。

## <a name="aws-user-based-access"></a>基于 AWS 用户的访问

以下部分逐步讲解如何创建一个只读的用户，以提供对成本管理的访问。

### <a name="add-aws-read-only-user-based-access"></a>添加基于 AWS 只读用户的访问

1. 通过 https://console.aws.amazon.com/iam/home 登录到 AWS 控制台并选择“用户”。
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

1. 可以从 Azure 门户打开 Cloudyn 门户，或者导航到 https://azure.cloudyn.com/ 并登录。
2. 单击“设置”（齿轮图标），然后选择“云帐户”。
3. 在“帐户管理”中选择“AWS 帐户”选项卡，然后单击“新增 +”。
4. 对于“帐户名称”，请键入帐户名称。
5. 在“访问类型”旁边，选择“IAM 用户”。
6. 在“访问密钥”中，粘贴 credentials.csv 文件中的“访问密钥 ID”值。
7. 在“机密密钥”中，粘贴 credentials.csv 文件中的“机密访问密钥”值，然后单击“保存”。  
    ![AWS 用户帐户状态](./media/connect-aws-account/aws-user-account-status.png)

AWS 帐户显示在帐户列表中。 “帐户状态”应该附带一个绿色的勾选符号。

成本管理将开始收集数据并填充报表。 但是，某些优化报表可能需要先积累几天的数据，然后才能生成准确的建议。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Cloudyn 提供的 Azure 成本管理的详细信息，请继续学习成本管理的[查看用量和成本](tutorial-review-usage.md)教程。
