---
title: 测试驱动器的类型，Microsoft 商业应用商店
description: 商业应用商店中的测试驱动器类型
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: keferna
author: keferna
ms.openlocfilehash: 92fd4d629585ed465e2891be2dce1c1bdc8c88e6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287948"
---
# <a name="azure-resource-manager-test-drive"></a>Azure 资源管理器测试驱动器

如果你具有 Azure Marketplace 或 AppSource 上的产品/服务，但想要构建仅包含 Azure 资源的测试驱动器，请使用此类型。 Azure 资源管理器（ARM）模板是你设计以最好地表示你的解决方案的 Azure 资源的编码容器。 测试驱动器采用提供的 ARM 模板，并将它所需的所有资源部署到资源组。 这是虚拟机或 Azure 应用产品/服务的唯一测试驱动器选项。

如果你不熟悉 ARM 模板的定义，请阅读[什么是 Azure 资源管理器？](../azure-resource-manager/resource-group-overview.md)并[了解 arm 模板的结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)，以便更好地了解如何生成和测试你自己的模板。

有关**托管**或**逻辑应用**测试驱动器的信息，请参阅[什么是测试驱动器？](what-is-test-drive.md)

## <a name="technical-configuration"></a>技术配置

部署模板包含构成解决方案的所有 Azure 资源。 适合此方案的产品仅使用 Azure 资源。 在 "合作伙伴中心" 中设置以下属性：

- **区域**（必需）- 当前存在 26 个 Azure 支持的区域，可在这些区域提供你的体验版。 通常情况下，你希望在预计客户数最多的区域中提供你的体验版，以便他们可以选择最接近的区域以获得最佳性能。 需确保允许订阅在所选的各个区域中部署所需的所有资源。

- **实例** - 选择类型（热或冷）和可用实例的数量，将其乘以提供产品/服务的区域数量。

  - **热** - 部署此类型的实例并等待每个所选区域的访问。 客户无需等待部署，就能立即访问体验版的热实例。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议拥有至少一个热实例，因为多数客户不想等待完整部署，因此，如果没有热实例可用，则会导致客户使用量下降 。

  - **冷** - 此类型的实例表示每个区域可能部署的实例总数。 冷实例要求在客户请求体验版时部署整个体验版资源管理器模板，因此，冷实例的加载速度比热实例要慢得多 。 弊端在于你只需为体验版的持续时间付费，它并不像热实例一样始终在 Azure 订阅上运行 。

- **体验版 Azure 资源管理器模板** - 上传包含 Azure 资源管理器模板的 .zip。 若要详细了解如何创建 Azure 资源管理器模板，请参阅快速入门文章[使用 Azure 门户创建和部署 Azure 资源管理器模板](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)。

- **体验版持续时间**（必需）- 输入体验版保持活动状态的小时数。 此时间段结束后，体验版会自动终止。 仅使用整数（例如，2 小时有效，1.5 小时无效）。

## <a name="write-the-test-drive-template"></a>写入测试驱动器模板

在设计了所需的资源包后，请编写并生成测试驱动器 ARM 模板。 由于测试驱动器以完全自动的模式运行部署，因此，测试驱动器模板有一些限制：

### <a name="parameters"></a>参数

大多数模板都具有一组参数，这些参数定义资源名称、资源大小（例如存储帐户或虚拟机大小的类型）、用户名和密码、DNS 名称，等等。 使用 Azure 门户部署解决方案时，可以手动填充所有的这些参数、选择可用的 DNS 名称或存储帐户名称等。

![Azure 资源管理器中的参数列表](media/test-drive/resource-manager-parameters.png)

但是，无需人工交互即可自动运行测试驱动器，因此它只支持一组有限的参数类别。 如果测试驱动器 ARM 模板中的某个参数不属于受支持的类别之一，则必须将此参数替换为变量或常量值。

你可以使用任何有效的参数名称;通过使用元数据类型值，测试驱动器可识别参数类别。 指定每个模板参数的元数据类型，否则模板将无法通过验证：

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> 所有参数都是可选的，因此，如果不想使用任何参数，就不必这样做。

### <a name="accepted-parameter-metadata-types"></a>接受的参数元数据类型

| 元数据类型   | 参数类型  | 说明     | 示例值    |
|---|---|---|---|
| **baseuri**     | string          | 部署包的基 URI| `https:\//\<\..\>.blob.core.windows.net/\<\..\>` |
| **username**    | string          | 随机的新用户名。| admin68876      |
| password    | 安全字符串    | 随机的新密码 | Lp!ACS\^2kh     |
| **会话 id**   | string          | 唯一的测试驱动器会话 ID （GUID）    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

Test drive 使用部署包的**基本 Uri**初始化此参数，因此可以使用此参数来构造包中包含的任何文件的 uri。

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

在模板中使用此参数来构造测试驱动器部署包中任何文件的 Uri。 下面的示例演示如何构造链接模板的 Uri：

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

使用新的随机用户名初始化此参数：

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

示例值：`admin68876`

可以将随机的或恒定的用户名用于解决方案。

#### <a name="password"></a>password

试用版：使用新的随机密码初始化此参数：

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

示例值：`Lp!ACS^2kh`

可以将随机的或恒定的密码用于解决方案。

#### <a name="session-id"></a>会话 ID

试用版将此参数初始化为唯一的 GUID，它表示测试驱动器会话 ID：

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

示例值：`b8c8693e-5673-449c-badd-257a405a6dee`

如果需要，可以使用此参数来唯一标识测试驱动器会话。

### <a name="unique-names"></a>唯一的名称

一些 Azure 资源（如存储帐户或 DNS 名称）需要具有全局唯一的名称。 这意味着每次测试驱动器部署 ARM 模板时，都会创建一个新的资源组，其所有资源都具有唯一的名称。 因此，你必须使用与资源组 Id 上的变量名称相连接的[uniquestring](../azure-resource-manager/templates/template-functions.md)函数来生成随机唯一值：

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

确保使用唯一的字符串输出（）连接参数/变量字符串（ `contosovm` ） `resourceGroup().id` ，因为这样可以保证每个变量的唯一性和可靠性。

例如，大多数的资源名称不能以数字开头，而唯一字符串函数可以返回一个以数字开头的字符串。 因此，若使用原始的唯一字符串输出，部署将失败。

有关详细信息，可查看[本文](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)中的资源命名规则和限制。

### <a name="deployment-location"></a>部署位置

可以让你在不同的 Azure 区域中提供测试驱动器。 此想法是希望让用户选择最近的区域，进而提供最佳的用户体验。

当测试驱动器创建实验室的实例时，它始终在用户选择的区域中创建资源组，然后在此组上下文中执行部署模板。 因此，模板应从资源组选择部署位置：

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

然后将此位置用于特定实验室实例的各个资源：

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

确保允许订阅在所选的每个区域中部署所需的所有资源。 此外，请确保你的虚拟机映像在你将启用的所有区域中都可用，否则你的部署模板将不能用于某些区域。

### <a name="outputs"></a>输出

正常情况下，可以部署资源管理器模板，而无需生成任何输出。 因为你知道用于填充模板参数的所有值，并且始终可以手动检查任意资源的属性。

但对于测试驱动器资源管理器模板，请务必返回到测试驱动器，以获取对实验室（网站 Uri、虚拟主机名、用户名和密码）的访问权限所需的所有信息。 确保所有输出名称都是可读的，因为这些变量将呈现给客户。

对于模板输出，没有任何限制。 测试驱动器将所有输出值转换为字符串，因此，如果将对象发送到输出，用户将看到 JSON 字符串。

例如：

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>订阅限制

不要忘记订阅和服务限制。 例如，如果想要部署多达10个4核虚拟机，则需要确保用于实验室的订阅允许使用40核心。 有关 Azure 订阅和服务限制的详细信息，请参阅[azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。 可以同时执行多个测试驱动器，验证你的订阅是否可以处理核心数乘以可执行的并发测试驱动器总数。

### <a name="what-to-upload"></a>要上传的内容

测试驱动器 ARM 模板上传为 zip 文件，其中可能包括各种部署项目，但必须具有一个名为的文件 `main-template.json` 。 这是测试驱动器用于实例化实验室的 ARM 部署模板。 如果此文件之外还有其他资源，可以在模板中将其作为外部资源引用，或将它们包含在 zip 文件中。

在发布认证过程中，测试驱动器会解压缩部署包，并将其内容放入内部测试驱动器 blob 容器。 此容器结构反映部署包的结构：

| package.zip                       | 测试驱动器 blob 容器         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

我们将此 blob 容器的 URI 称为基 URI。 因为实验室的每个版本都有其自己的 blob 容器，所以，每个实验室版本都有其自己的基本 Uri。 测试驱动器可以通过模板参数将解压缩的部署包的基本 Uri 传递到模板。

### <a name="transform-template-examples-for-test-drive"></a>测试驱动器的转换模板示例

将资源体系结构转换为测试驱动器资源管理器模板的过程可能会很困难。 有关更多帮助，请参阅有关如何最好地转换当前部署模板的示例，请参阅[什么是测试驱动器？](what-is-test-drive.md#transforming-examples)。

## <a name="test-drive-deployment-subscription-details"></a>测试驱动器部署订阅详细信息

要完成的最后一个部分是能够通过连接 Azure 订阅和 Azure Active Directory （AD）自动部署测试驱动器。

![测试驱动器部署订阅详细信息](media/test-drive/deployment-subscription-details.png)

1. 获取**Azure 订阅 ID**。 此项授予对 Azure 服务和 Azure 门户的访问权限。 将在该订阅中报告资源用量和计收服务费用。 如果你还没有单独的 Azure 订阅用于测试驱动器，请创建一个。 你可以 `1a83645ac-1234-5ab6-6789-1h234g764ghty1` 通过登录到 Azure 门户并从左侧导航菜单中选择 "**订阅**" 来查找 Azure 订阅 id （例如）。

   ![Azure 订阅](media/test-drive/azure-subscriptions.png)

2. 获取**Azure AD 租户 ID**。 如果你已经有可用的租户 id，可以在**Azure Active Directory**  >  **Properties**  >  **Directory ID**中找到它：

   ![Azure Active Directory 属性](media/test-drive/azure-active-directory-properties.png)

   如果没有租户 ID，请在 Azure Active Directory 中创建一个新的 ID。 有关设置租户的帮助，请参阅[快速入门：设置租户](../active-directory/develop/quickstart-create-new-tenant.md)。

3. **AZURE AD 应用 ID** –创建并注册新的应用程序。 我们将使用此应用程序在你的测试驱动器实例上执行操作。

   1. 导航到新创建的目录或现有的目录，然后在 "筛选器" 窗格中选择 "Azure Active Directory"。
   2. 搜索**应用注册**，然后选择 "**添加**"。
   3. 提供应用程序名称。
   4. 选择**Web 应用/API**的**类型**。
   5. 提供 "登录 URL" 中的任何值，则不使用此字段。
   6. 选择“创建”。
   7. 创建应用程序后，请选择 "**属性**  >  **"，将应用程序设置为多租户**，并**保存**。

4. 选择“保存” 。

5. 复制此已注册应用程序的应用程序 ID，并将其粘贴到 "测试驱动器" 字段。

   ![Azure AD 应用程序 ID 详细信息](media/test-drive/azure-ad-application-id-detail.png)

6. 由于我们使用的是要部署到订阅的应用程序，因此我们需要将该应用程序添加为订阅的参与者：

   1. 选择要用于测试驱动器的**订阅**类型。
   1. 选择“访问控制 (IAM)”。
   1. 选择 "**角色分配**" 选项卡，然后**添加角色分配**。

      ![添加新的访问控制主体](media/test-drive/access-control-principal.jpg)

   1. 设置**角色**并**将访问权限分配给**，如下所示。 在 "**选择**" 字段中，输入 Azure AD 应用程序的名称。 选择要向其分配 "**参与者**" 角色的应用程序。

      ![添加权限](media/test-drive/access-control-permissions.jpg)

   1. 选择“保存” 。

7. 生成**Azure AD 应用**的身份验证密钥。 在 "**密钥**" 下，添加**密钥说明**，将 "持续时间" 设置为 "**永不过期**" （过期密钥将在生产中中断测试驱动器），然后选择 "**保存**"。 将此值复制并粘贴到 "所需的测试驱动器" 字段。

![显示 Azure AD 应用程序密钥](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>重新发布

现在所有的测试驱动器字段都已完成，请重新**发布**产品/服务。 一旦您的测试驱动器通过了认证，就可以在您的产品/服务预览版中测试客户体验：

1. 在 UI 中启动测试驱动器。
1. 在 Azure 门户中打开 Azure 订阅。
1. 验证测试驱动器是否正在正确部署。

   ![Azure 门户](media/test-drive/azure-portal.png)

请勿删除为客户预配的任何测试驱动器实例;当客户完成这些资源组后，该服务将自动清理这些资源组。

一旦你熟悉预览版产品，就可以**开始**了！ 要仔细检查所有的端到端体验，请查看最终的审查过程。 如果我们拒绝该产品/服务，我们将向你的产品/服务发送电子邮件，说明需要修复的内容。

## <a name="next-steps"></a>后续步骤

- 如果按照说明在合作伙伴中心创建产品/服务，请使用后退箭头返回到该主题。
- 有关其他类型的测试驱动器的详细信息[，请参阅什么是测试驱动器？](what-is-test-drive.md)。
