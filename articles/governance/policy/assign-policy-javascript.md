---
title: 快速入门：使用 JavaScript 新建策略分配
description: 本快速入门介绍如何使用 JavaScript 创建 Azure Policy 分配以识别不合规的资源。
ms.date: 09/24/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 7548053e4bd5be214bf7de3eef3dc4c6c95442d4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91347879"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>快速入门：使用 JavaScript 创建策略分配以识别不合规的资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。 在本快速入门中，我们将创建策略分配，以识别未使用托管磁盘的虚拟机。 完成后，我们便可以识别不合规的虚拟机。 

JavaScript 库用于从命令行或脚本管理 Azure 资源。 本指南介绍如何使用 JavaScript 库来创建策略分配。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- **Node.js**：需要 [Node.js](https://nodejs.org/) 版本 12 或更高版本。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>添加 Policy 库

要使 JavaScript 能够使用 Azure Policy，必须添加这些库。 这些库适用于可使用 JavaScript 的任何环境，包括 [Windows 10 上的 Bash](/windows/wsl/install-win10)。

1. 运行以下命令，设置新的 Node.js 项目。

   ```bash
   npm init -y
   ```

1. 添加对 yargs 库的引用。

   ```bash
   npm install yargs
   ```

1. 添加对 Azure Policy 库的引用。

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. 添加对 Azure 身份验证库的引用。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > 在 package.json 中验证 `@azure/arm-policy` 是否为版本 3.1.0 或更高版本、`@azure/arm-policyinsights` 是否为版本 3.2.0 或更高版本，以及 `@azure/ms-rest-nodeauth` 是否为版本 3.0.5 或更高版本  。

## <a name="create-a-policy-assignment"></a>创建策略分配

本快速入门将创建一个策略分配，并分配“审核未使用托管磁盘的 VM”(`06a78e20-9358-41c9-923c-fb736d382a4d`) 定义。 此策略定义可识别不符合策略定义中设置的条件的资源。

1. 新建一个名为 policyAssignment.js 的文件，并输入以下代码。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. 在终端中输入以下命令：

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

上述命令使用以下信息：

- **subID** - 身份验证上下文的订阅 ID。 请确保将 `{subscriptionId}` 替换为你的订阅。
- **name** - 策略分配对象的唯一名称。 上面的示例使用 audit-vm-manageddisks。
- **displayName** - 策略分配的显示名称。 本例使用了“审核未使用托管磁盘分配的虚拟机”  。
- **policyDefID** - 策略定义路径，用作创建分配的依据。 在本例中，它为策略定义“审核未使用托管磁盘的 VM”的 ID  。
- **说明** - 有关策略用途或将其分配到此范围的原因的更深入说明。
- **作用域** - 作用域确定要对哪些资源或资源分组实施策略分配。 它的范围可以从管理组到单个资源。 请确保将 `{scope}` 替换为以下某个模式：
  - 管理组：`/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 订阅：`/subscriptions/{subscriptionId}`
  - 资源组：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 资源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

你现已准备好识别不合规的资源，了解环境的符合性状态。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

创建策略分配后，可以识别不合规的资源。

1. 新建一个名为 policyState.js 的文件，并输入以下代码。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. 在终端中输入以下命令：

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

将 `{subscriptionId}` 替换为你要在其中查看前面步骤中已创建的名为“audit-vm-manageddisks”的策略分配的符合性结果订阅。 有关用于汇总数据的其他作用域和方法的列表，请参阅[PolicyStates*](/javascript/api/@azure/arm-policyinsights/) 方法。

结果应如以下示例所示：

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

结果与 Azure 门户视图中策略分配的“资源符合性”  选项卡中显示的内容相匹配。

## <a name="clean-up-resources"></a>清理资源

- 通过门户删除策略分配“审核不带托管磁盘分配的 VM”。 策略定义是内置的，因此没有要删除的定义。

- 如果希望从应用程序中删除已安装的库，请运行以下命令。

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>后续步骤

本快速入门已分配一个策略定义用于识别 Azure 环境中的不合规资源。

要了解有关分配策略定义以验证新资源是否符合要求的详细信息，请继续以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./tutorials/create-and-manage.md)