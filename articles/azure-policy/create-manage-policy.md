---
title: "使用 Azure 策略来创建和管理策略以强制实施组织符合性 | Microsoft Docs"
description: "使用 Azure 策略强制执行标准、满足法规遵从性、审核需求、控制成本、维护安全和性能的一致性，并实施企业范围的设计原则。"
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 55e5a60294fc5ccb2a55b1e572af2fd27c68f462
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>创建和管理策略以强制实施符合性

了解如何在 Azure 中创建和管理策略对于保持与公司标准和服务级别协议的符合性来说非常重要。 本教程介绍如何使用 Azure 策略来执行某些与在组织中创建、分配和管理策略相关的常见任务，例如：

> [!div class="checklist"]
> * 分配策略，对将来创建的资源强制执行条件
> * 创建并分配计划定义，跟踪多个资源的符合性
> * 解决不符合或遭拒绝的资源
> * 在组织中实施新策略

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="opt-in-to-azure-policy"></a>选择加入 Azure 策略

Azure 策略现已在有限预览版中提供，因此需要注册才能请求访问权限。

1. 在 https://aka.ms/getpolicy 转到 Azure 策略，然后选择左侧窗格中的“注册”。

   ![搜索策略](media/assign-policy-definition/sign-up.png)

2. 通过选择“订阅”列表中想要使用的订阅来选择加入 Azure 策略。 然后选择“注册”。

   订阅列表包括所有 Azure 订阅。

   ![选择加入使用 Azure 策略](media/assign-policy-definition/preview-opt-in.png)

   我们可能需要一两天时间来接受注册请求，具体视情况而定。 我们接受请求后，你将获得电子邮件通知，告知可以开始使用服务。

## <a name="assign-a-policy"></a>分配策略

使用 Azure 策略强制实施符合性的第一步是分配策略定义。 策略定义用于定义实施策略的条件，以及要执行的操作。 在此示例中，分配名为“需要 SQL Server 版本 12.0”的内置策略定义，强制执行“所有 SQL Server 数据库都必须是 v12.0 才视为符合”的条件。

1. 通过搜索并选择左侧窗格中的“策略”，启动 Azure 门户中的 Azure 策略服务。

   ![搜索策略](media/assign-policy-definition/search-policy.png)

2. 选择 Azure 策略页左侧窗格中的“分配”。 分配即为在特定范围内分配策略以供执行。
3. 选择“分配”窗格顶部的“分配策略”。

   ![分配策略定义](media/create-manage-policy/select-assign-policy.png)

4. 在“分配策略”页上，单击“策略”字段旁边的![策略定义按钮](media/assign-policy-definition/definitions-button.png)，打开可用定义的列表。

   ![打开可用策略定义](media/create-manage-policy/open-policy-definitions.png)

5. 选择“需要 SQL Server 版本 12.0”。
   
   ![查找策略](media/create-manage-policy/select-available-definition.png)

6. 提供策略分配的显示名称。 在此示例中，我们使用“需要 SQL Server 版本 12.0”。 还可根据需要添加“说明”。 该说明详细介绍了此策略分配如何确保此环境中创建的所有 SQL Server 版本均为 12.0。
7. 将定价层更改为“标准”，确保策略应用于现有资源。

   Azure 策略内有两个定价层 - 免费和标准。 使用免费层，只能对将来资源强制实施策略；使用标准层，还可对现有资源强制实施策略，更好地了解符合性状态。 由于我们处于受限预览版，尚未发布定价模型，因此你不会收到选择标准的帐单。 若要了解有关定价的详细信息，请参阅 [Azure 策略定价](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/)。

8. 选择“范围”- 之前在选择加入 Azure 策略时注册的订阅（或资源组）。 范围用于确定对其强制执行策略分配的资源或资源组。 它可以从订阅延伸至资源组。

   此示例使用此订阅 -“Azure 分析容量开发”。 你的订阅将有所不同。

10. 选择“分配”。

## <a name="implement-a-new-custom-policy"></a>实施新的自定义策略

我们现已分配策略定义，接下来将创建新策略，通过确保环境中创建的 VM 不属于 G 系列来节约成本。 这样，当组织中用户每次尝试创建 G 系列的 VM 时，请求将被拒绝。

1. 选择左侧窗格中“创作”下的“定义”。

   ![创作下的定义](media/create-manage-policy/definition-under-authoring.png)

2. 选择“+ 策略定义”。
3. 输入以下内容：

   - 策略定义的名称 - 需要 VM SKU 小于 G 系列
   - 想通过策略定义实现的操作的说明 - 此策略定义强制此范围中创建的所有 VM 具有的 SKU 都小于 G 系列，以减少成本。
   - 将执行策略定义的订阅 - 本示例中，策略定义位于“顾问分析容量开发”中。 你的订阅列表将有所不同。
   - 使用以下内容编写 json 代码：
      - 策略参数。
      - 策略规则/条件，此示例中为 - VM SKU 大小等于 G 系列
      - 策略效果，此示例中为“拒绝”。
   
   json 应如下所示

```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/sku.name",
            "like": "Standard_G*"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
}
```

   若要查看 json 代码的示例，请参阅此文章 - [资源策略概述](../azure-resource-manager/resource-manager-policy.md)
   
4. 选择“保存”。

## <a name="create-and-assign-an-initiative-definition"></a>创建并分配计划定义

通过计划定义，可以组合某些策略定义以实现首要目标。 创建计划定义，确保定义范围内的资源符合构成计划定义的策略定义。  有关计划定义的详细信息，请参阅 [Azure 策略概述](./azure-policy-introduction.md)。

### <a name="create-an-initiative-definition"></a>创建计划定义

1. 选择左侧窗格中“创作”下的“定义”。

   ![选择定义](media/create-manage-policy/select-definitions.png)

2. 选择页面顶部的“计划定义”，然后将显示“计划定义”窗体。
3. 输入计划的名称和说明。

   在此示例中，我们想要确保资源符合有关保证安全的策略定义，计划的名称为“保证安全”，说明为：创建了此计划，处理所有与保护资源相关的策略定义。

   ![计划定义](media/create-manage-policy/initiative-definition.png)

4. 浏览“可用定义”列表，然后选择要添加到该计划的策略定义。 对于“保证安全”计划，添加以下内置策略定义：
   - 需要 SQL Server 版本 12.0
   - 监视安全中心内未受保护的 Web 应用。
   - 监视安全中心内的许可网络。
   - 监视安全中心内列为白名单的可能的应用。
   - 监视安全中心内未加密的 VM 磁盘。

   ![计划定义](media/create-manage-policy/initiative-definition-2.png)

   从列表中选择策略定义后，该策略定义将显示在“策略和参数”下，如上所示。

5. 选择“创建” 。

### <a name="assign-an-initiative-definition"></a>分配计划定义

1. 转到“创作”下的“定义”选项卡。
2. 搜索所创建的“保证安全”计划定义。
3. 选择此计划定义，然后选择“分配”。

   ![分配定义](media/create-manage-policy/assign-definition.png)

4. 输入以下内容，填写“分配”窗体：
   - 名称：保证安全分配
   - 说明：此计划分配旨在在“Azure 顾问容量开发”订阅中强制执行这组策略定义
   - 定价层：标准
   - 想要应用于此分配的范围：Azure 顾问容量开发

5. 选择“分配”。 

## <a name="resolve-a-non-compliant-or-denied-resource"></a>解决不符合或遭拒绝的资源

继续以上示例，在分配策略定义、要求使用 SQL server 版本 12.0 以后，通过其他版本创建的 SQL Server 将被拒绝。 本部分介绍解决拒绝尝试创建不同版本的 SQL Server 的问题。

1. 选择左侧窗格中的“分配”。
2. 浏览所有策略分配并启动“需要 SQL Server 版本 12.0”分配。
3. 请求排除尝试在其中创建 SQL Server 的资源组。 此示例排除 Microsoft.Sql/servers/databases：baconandbeer/Cheetos 和 baconandbeer/Chorizo。

   ![请求排除](media/create-manage-policy/request-exclusion.png)

   可解决遭拒绝资源的其他方法包括：如果具有需要创建 SQL Server 的强有力理由，请联系与策略相关的联系人；如果具有策略的访问权限，请直接编辑策略。

4. 选择“保存”。

本部分中，通过请求对资源进行排除解决了尝试创建版本为 12.0 的 SQL Server 遭拒绝的问题。

## <a name="clean-up-resources"></a>清理资源

如果计划继续浏览后续教程，请勿清除在本指南中创建的资源。 如果不打算继续学习，请通过以下步骤删除上面创建的所有分配或定义：

1. 选择左侧窗格中的“定义”（如果尝试删除分配，则选择“分配”）。
2. 搜索刚创建的新计划或策略定义（或分配）。
3. 选择定义或分配末端的省略号，并选择“删除定义”（或“删除分配”）。

## <a name="next-steps"></a>后续步骤

在本教程中，你已成功完成以下操作：

> [!div class="checklist"]
> * 分配策略，对将来创建的资源强制执行条件
> * 创建并分配计划定义，跟踪多个资源的符合性
> * 解决不符合或遭拒绝的资源
> * 在组织中实施新策略

若要了解有关策略定义结构的详细信息，请查看以下文章：

> [!div class="nextstepaction"]
> [策略定义结构](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure)
