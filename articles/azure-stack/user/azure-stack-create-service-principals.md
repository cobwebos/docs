---
title: 创建 Azure Stack 的服务主体 | Microsoft Docs
description: 描述如何创建可使用基于角色的访问控制 Azure 资源管理器中用来管理对资源的访问的服务主体。
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: mabrigg
ms.openlocfilehash: de5712fd7b48a759b366f5b9808bbbefc6e305cd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>通过创建服务主体授予对 Azure 堆栈资源的应用程序访问

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以让应用程序访问 Azure 堆栈资源通过创建服务主体，它使用 Azure 资源管理器。 服务主体可以委派特定的权限使用[基于角色的访问控制](azure-stack-manage-permissions.md)。

作为最佳做法是，你应为应用程序使用服务主体。 服务主体要优于运行应用程序使用您自己的凭据，原因如下：

* 可以向服务主体分配不同于自己的帐户权限的权限。 通常情况下，服务主体的权限仅限于完全什么应用程序需要执行操作。
* 无需更改应用程序的凭据，如果您的角色或职责更改。
* 证书可以用于运行无人参与的脚本时自动执行身份验证。

## <a name="example-scenario"></a>示例方案

具有需要清单使用 Azure 资源管理器的 Azure 资源的配置管理应用。 你可以创建服务主体，并将其分配到的读者角色。 此角色授予对 Azure 资源的应用程序只读访问权限。

## <a name="getting-started"></a>入门

使用本文中的步骤，作为指南到：

* 创建你的应用程序的服务主体。
* 注册你的应用程序并创建身份验证密钥。
* 为应用程序分配到角色。

为 Azure 堆栈配置 Active Directory 的方式确定如何创建服务主体。 选取下列选项之一：

* 创建服务主体的[Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad)。
* 创建服务主体的[Active Directory 联合身份验证服务 (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs)。

如何分配服务主体角色相同的 Azure AD 和 AD FS。 创建服务主体后，你可以[委派权限](azure-stack-create-service-principals.md#assign-role-to-service-principal)为其分配到角色。

## <a name="create-a-service-principal-for-azure-ad"></a>为 Azure AD 中创建服务主体

如果 Azure 堆栈使用 Azure AD 作为标识存储，你可以创建服务主体使用相同的步骤，如 Azure 中，使用 Azure 门户中所示。

>[!NOTE]
检查以查看你已经有[所需的 Azure AD 权限](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)在开始创建服务主体之前。

### <a name="create-service-principal"></a>创建服务主体

若要创建你的应用程序的服务主体：

1. 通过 [Azure 门户](https://portal.azure.com)登录 Azure 帐户。
2. 选择**Azure Active Directory** > **应用程序注册** > **添加**。
3. 为应用提供名称和 URL。 选择“Web 应用/API”或“本机”作为要创建的应用程序的类型。 设置这些值后，选择“创建”。

### <a name="get-credentials"></a>获取凭据

如果记录以编程方式，使用你的应用程序和身份验证密钥的 ID。 若要获取这些值：

1. 从 Active Directory 中的“应用注册”，选择应用程序。

2. 复制“应用程序 ID”并将其存储在应用程序代码中。 中的应用程序[示例应用程序](#sample-applications)使用**客户端 id**引用时**应用程序 ID**。

     ![应用程序的应用程序 ID](./media/azure-stack-create-service-principal/image12.png)
3. 若要生成身份验证密钥，请选择“密钥”。

4. 提供密钥说明和密钥持续时间。 完成后，选择“保存”。

>[!IMPORTANT]
保存键，该键后**值**显示。 记下此值，因为你无法检索该密钥更高版本。 将密钥值存储在应用程序可检索的位置。

![保存的密钥的密钥值警告。](./media/azure-stack-create-service-principal/image15.png)

最后一步是[分配你的应用程序角色](azure-stack-create-service-principals.md#assign-role-to-service-principal)。

## <a name="create-service-principal-for-ad-fs"></a>为 AD FS 创建服务主体

如果你部署 Azure 堆栈作为标识存储使用 AD FS，可将 PowerShell 用于以下任务：

* 创建服务主体。
* 为服务主体分配角色。
* 登录使用服务主体的标识。

### <a name="before-you-begin"></a>开始之前

[下载到本地计算机所需的 Azure 堆栈工具。](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>导入标识 PowerShell 模块

导航到 Azure 堆栈工具的下载文件夹，并通过使用以下命令导入标识 PowerShell 模块：

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

当你导入标识模块时，你可能会收到此错误消息:"AzureStack.Connect.psm1 未进行数字签名。 该脚本将不会在系统上执行”。

若要解决此问题，你需要配置以允许运行脚本的执行策略。 若要设置执行策略，请在提升的 PowerShell 会话中运行以下命令：

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>创建服务主体

你可以创建服务主体通过运行以下命令，同时确保更新**DisplayName**参数：

```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose

```

### <a name="assign-a-role"></a>分配角色

创建服务主体后，你必须[向角色分配](azure-stack-create-service-principals.md#assign-role-to-service-principal)。

### <a name="sign-in-using-powershell"></a>使用 PowerShell 登录

你可以登录到 Azure 堆栈通过运行以下命令，同时确保更新**EnvironmentName**替换为你的应用程序的名称的参数：

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId `
 -TenantId $directoryTenantId
```

## <a name="assign-the-service-principal-to-a-role"></a>为服务主体分配角色

要访问订阅中的资源，必须将应用程序分配到角色。 决定哪个角色表示应用程序的相应权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](../../role-based-access-control/built-in-roles.md)。

>[!NOTE]
您可以在订阅、 资源组或资源的级别设置角色的作用域。 较低级别的作用域将继承权限。 例如，使用资源组的读者角色应用程序意味着该应用可以读取任何资源组中的资源。

用于将角色分配给服务主体作为指南使用以下步骤。

1. 在 Azure 堆栈门户中，导航到你想要分配到应用程序的作用域的级别。 例如，若要在订阅范围内分配角色，选择“订阅”。

2. 选择要分配到应用程序的订阅。 在此示例中，该订阅是 Visual Studio Enterprise。

     ![选择为分配的 Visual Studio Enterprise 订阅](./media/azure-stack-create-service-principal/image16.png)

3. 选择**访问控制 (IAM)** 订阅。

     ![选择访问控制](./media/azure-stack-create-service-principal/image17.png)

4. 选择 **添加** 。

5. 选择要分配到应用程序的角色。

6. 搜索你的应用程序，并选择它。

7. 选择“确定”完成角色分配。 你可以查看分配给该作用域的角色的用户应用程序列表中。

现在，你已经创建服务主体，并分配的角色，你的应用程序可以访问 Azure 堆栈的资源。

## <a name="next-steps"></a>后续步骤

[管理用户权限](azure-stack-manage-permissions.md)
