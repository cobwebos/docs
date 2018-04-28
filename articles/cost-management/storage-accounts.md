---
title: 配置 Azure 成本管理的存储帐户 | Microsoft Docs
description: 本文介绍如何为 Azure 成本管理配置 Azure 存储帐户和 AWS 存储 Bucket。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 76a9fc586a1932b8b5e664b6c964f0c7d3eac4d4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="configure-storage-accounts-for-cost-management"></a>为成本管理配置存储帐户

<!--- intent: As a Cost Management user, I want to configure Cost Management to use my cloud service provider storage account to store my reports. -->

可以将成本管理报表保存在 Cloudyn 门户、Azure 存储或 AWS 存储 Bucket 中。 将报表保存到 Cloudyn 门户的操作免费。 但是，将报表保存到云服务提供商的存储为可选操作，需要额外付费。 本文介绍如何配置用于存储报表的 Azure 存储帐户和 Amazon Web Services (AWS) 存储 Bucket。

## <a name="prerequisites"></a>先决条件

必须有 Azure 存储帐户或 Amazon 存储 Bucket。

如果没有 Azure 存储帐户，则需创建一个。 有关如何创建 Azure 存储帐户的详细信息，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。

如果没有 AWS 简单存储服务 (S3) Bucket，则需创建一个。 有关如何创建 S3 Bucket 的详细信息，请参阅 [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)（创建存储 Bucket）。

## <a name="configure-your-azure-storage-account"></a>配置 Azure 存储帐户

配置可供成本管理使用的 Azure 存储很简单。 收集存储帐户的详细信息，并在 Cloudyn 门户中复制这些信息。

1. 通过 http://portal.azure.com 登录到 Azure 门户。
2. 单击“所有服务”，选择“存储帐户”，滚动到要使用的存储帐户，然后选择该帐户。
3. 在存储帐户页的“设置”下，单击“访问密钥”。
4. 复制**存储帐户名称**和 key1 下的**连接字符串**。  
![Azure 存储访问密钥](./media/storage-accounts/azure-storage-access-keys.png)  
5. 可以从 Azure 门户打开 Cloudyn 门户，也可以导航到 https://azure.cloudyn.com 并登录。
6. 单击齿轮符号，然后选择“报表存储管理”。
7. 单击“新增 +”，确保选中“Microsoft Azure”。 在“名称”区域中粘贴 Azure 存储帐户名称。 在相应的区域中粘贴**连接字符串**。 输入容器名称，然后单击“保存”。  
![为 Azure 配置的 Cloudyn 存储](./media/storage-accounts/azure-cloudyn-storage.png)

  此时新的 Azure 报表存储项会显示在存储帐户列表中。  
    ![列表中的新 Azure 报表存储](./media/storage-accounts/azure-storage-entry.png)


现在可以将报表保存到 Azure 存储了。 在任意报表中单击“操作”，然后选择“计划报表”。 为报表命名，然后添加自己的 URL 或使用自动创建的 URL。 选择“保存到存储”，然后选择存储帐户。 输入将添加到报表文件名中的前缀。 选择 CSV 或 JSON 文件格式，然后保存报表。

## <a name="configure-an-aws-storage-bucket"></a>配置 AWS 存储 Bucket

Cloudyn 使用现有的 AWS 凭据“用户”或“角色”将报表保存到存储 Bucket。 为了测试访问权限，Cloudyn 会尝试将一个文件名为 _check-bucket-permission.txt_ 的小型文本文件保存到 Bucket。

请向 Bucket 提供具有 PutObject 权限的 Cloudyn 角色或用户。 然后，使用现有的 Bucket 或新建一个来保存报表。 最后，决定如何管理存储类、设置生命周期规则或删除任何不必要的文件。

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>向 AWS 用户或角色分配权限

创建新策略时，请提供将报表保存到 S3 Bucket 所需的确切权限。

1. 登录到 AWS 控制台，选择“服务”。
2. 从服务列表中选择“IAM”。
3. 在控制台左侧选择“策略”，然后单击“创建策略”。
4. 单击“JSON”选项卡。
5. 以下策略允许将报表保存到 S3 Bucket。 将以下策略示例复制并粘贴到“JSON”选项卡。将 &lt;bucketname&gt; 替换为 Bucket 名称。

  ```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
}
```

6. 单击“查看策略”。  
    ![查看策略](./media/storage-accounts/aws-policy.png)  
7. 在“查看策略”页上，键入策略的名称。 例如，_CloudynSaveReport2S3_。
8. 单击“创建策略”。

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>将策略附加到帐户中的 Cloudyn 角色或用户

若要附加新策略，请打开 AWS 控制台，然后编辑 Cloudyn 角色或用户。

1. 登录到 AWS 控制台，选择“服务”，然后从服务列表中选择“IAM”。
2. 在控制台左侧选择“角色”或“用户”。

**对于角色：**

  1. 单击 Cloudyn 角色名称。
  2. 在“权限”选项卡上，单击“附加策略”。
  3. 搜索创建的策略，将其选中，然后单击“附加策略”。
    ![AWS - 附加角色的策略](./media/storage-accounts/aws-attach-policy-role.png)

**对于用户：**

1. 选择 Cloudyn 用户。
2. 在“权限”选项卡上，单击“添加权限”。
3. 在“授予权限”部分，选择“直接附加现有策略”。
4. 搜索创建的策略，将其选中，然后单击“下一步: 查看”。
5. 在“将权限添加到角色名称”页上，单击“添加权限”。  
    ![AWS - 附加用户的策略](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>可选：使用 Bucket 策略设置权限

也可将权限设置为使用 Bucket 策略创建关于 S3 Bucket 的报表。 在经典 S3 视图中执行以下操作：

1. 创建或选择现有的 Bucket。
2. 选择“权限”选项卡，然后单击“Bucket 策略”。
3. 复制并粘贴以下策略示例。 将 &lt;bucket\_name&gt; 和 &lt;Cloudyn\_principle&gt; 替换为 Bucket 的 ARN。 替换 Cloudyn 使用的角色或用户的 ARN。

  ```
{
  "Id": "Policy1485775646248",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
  ]
}
```

4. 在 Bucket 策略编辑器中，单击“保存”。

### <a name="add-aws-report-storage-to-cloudyn"></a>将 AWS 报表存储添加到 Cloudyn

1. 可以从 Azure 门户打开 Cloudyn 门户，也可以导航到 https://azure.cloudyn.com 并登录。
2. 单击齿轮符号，然后选择“报表存储管理”。
3. 单击“新增 +”，确保选中“AWS”。
4. 选择帐户和存储 Bucket。 AWS 存储 Bucket 的名称会自动填充。  
    ![为 AWS Bucket 添加报表存储](./media/storage-accounts/aws-cloudyn-storage.png)  
5. 单击“保存”，然后单击“确定”。

    此时新的 AWS 报表存储项会显示在存储帐户列表中。  
    ![列表中的新 AWS 报表存储](./media/storage-accounts/aws-storage-entry.png)


现在可以将报表保存到 Azure 存储了。 在任意报表中单击“操作”，然后选择“计划报表”。 为报表命名，然后添加自己的 URL 或使用自动创建的 URL。 选择“保存到存储”，然后选择存储帐户。 输入将添加到报表文件名中的前缀。 选择 CSV 或 JSON 文件格式，然后保存报表。

## <a name="next-steps"></a>后续步骤

- 查看[了解成本管理报表](understanding-cost-reports.md)，了解成本管理报表的基本结构和功能。
