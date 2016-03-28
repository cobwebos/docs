<properties
   pageTitle="Service Fabric 的持续集成 | Microsoft Azure"
   description="大致了解如何使用 Visual Studio Team Services (VSTS) 为 Service Fabric 应用程序设置持续集成。"
   services="service-fabric"
   documentationCenter="na"
   authors="cawams"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.date="01/27/2015"
   wacn.date="" />

# 使用 Visual Studio Team Services (VSTS) 为 Service Fabric 应用程序设置持续集成

本文介绍使用 Visual Studio Team Services (VSTS) 为 Service Fabric 应用程序设置持续集成，从而确保以自动方式生成、打包和部署应用程序的步骤。请注意，这些指令每次都将从零开始重新创建群集。

本文档反映当前过程，应随时间推移而更改。

## 先决条件

首先，请在 Visual Studio Team Services 上设置项目：

1. 如果你尚未创建 Team Services 帐户，请使用你的 [Microsoft 帐户](http://www.microsoft.com/account)设置它。

2. 使用 Microsoft 帐户在 Team Services 上创建新项目。

3. 将新的或现有 Service Fabric 应用的源推送到此项目。

有关处理 Team Services 项目的详细信息，请参阅[连接到 Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

## 设置服务主体

### 为自动化设置身份验证

你需要先创建生成代理将用于对 Azure 进行身份验证的[服务主体](/documentation/articles/resource-group-create-service-principal-portal)，才能设置生成计算机。你还需要创建证书并将其上传到 Azure 密钥保管库，因为密钥保管库不支持服务主体身份验证。可以从任意计算机进行这些步骤。你的开发计算机是一个不错的选择。

### 安装 Azure PowerShell 并登录

1.	安装 Azure PowerShell 中的说明进行操作。
2. 安装 PowerShellGet。若要执行此操作，请安装 [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729)，它包括 PowerShellGet。

    >[AZURE.NOTE] 如果正在运行具有最新更新的 Windows 10，你可以跳过此步骤。

3.	安装和更新 AzureRM 模块。
如果你安装了 Azure PowerShell 的任何以前版本，请将其删除：

    a.右键单击开始按钮，然后选择“添加/删除程序”。

    b.搜索“Azure PowerShell”并将其卸载。

    c.启动 PowerShell 命令提示符。

    d.使用命令 `Install-Module AzureRM` 安装 AzureRM 模块。

    e.使用命令 `Update-AzureRM` 更新 AzureRM 模块。

3.	禁用（或启用）Azure 数据收集。

    Azure cmdlet 将提示你选择加入或退出数据收集，直到你做出选择为止。这些提示将阻止自动化，同时等待用户输入。若要取消这些提示，请通过运行下列命令之一提前进行选择：

    - Enable-AzureRmDataCollection

    - 禁用 AzureRmDataCollection

4.	登录到 Azure PowerShell。

    a.运行命令 `Login-AzureRmAccount`。

    b.在出现的对话框中，请输入你的 Azure 凭据。

    c.运行命令 `Get-AzureRmSubscription`。

    d.找到你想要使用的订阅。

    e.运行命令 `Select-AzureRmSubscription -SubscriptionId <id for your subscription>`。

### 创建服务主体

1.	下载 [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) 并将其解压缩到此计算机上的文件夹。

2.	在管理员 PowerShell 命令提示符下，更改为解压缩存档内的目录 `Powershell\Manual`。

3.	使用以下命令为服务主体选择密码。请记住此密码，因为会将其用作生成变量。

    ```
    $password = Read-Host -AsSecureString
    ```
4.	使用以下参数运行 PowerShell 脚本Create-ServicePrincipal.ps1：

|参数|值|
|---|---|
|DisplayName|任意名称。|
|HomePage|任意 URI。无需实际存在。|
|IdentifierUri|任意唯一的 URI。无需实际存在。|
|SecurePassword|$password|

在脚本结束后，它将输出下列三个值。请注意这些值，因为它们被用作生成变量。

*  `ServicePrincipalId`
*  `ServicePrincipalTenantId`
*  `ServicePrincipalSubscriptionId`

### 创建证书并将其上传到新的 Azure 密钥保管库

>[AZURE.NOTE] 此示例脚本生成自签名证书，这不是安全的做法，仅可接受将其用于试验。按照组织的指导原则改为获取合法证书。

1.	在管理员 PowerShell 提示符下，更改为从中解压 `Manual.zip` 的目录。

2.	使用以下参数运行 PowerShell 脚本 `CreateAndUpload-Certificate.ps1`：

| 参数 | 值 |
| --- | --- |
| KeyVaultLocation | 任何值。此参数必须与你打算在其中创建群集的位置匹配。 |
| CertificateSecretName | 任何值。 |
| SecureCertificatePassword | 任何值。在生成计算机上导入证书时，使用此参数。 |
| KeyVaultResourceGroupName | 任何值。但是，请勿使用计划用于群集的资源组名称。 |
| KeyVaultName | 任何值。 |
| PfxFileOutputPath|任何值。使用此文件将证书导入到生成计算机上。 |

在脚本结束后，它将输出下列三个值。请注意这些值，因为它们被用作生成变量。

* `ServiceFabricCertificateThumbprint`
* `ServiceFabricKeyVaultId`
* `ServiceFabricCertificateSecretId`

## 设置生成计算机

### 安装 Visual Studio 2015

如果你已经配置计算机（或打算提供你自己的计算机），请在所选计算机上安装 [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)。



### 安装 Service Fabric SDK

在你的计算机上安装 [Service Fabric SDK](/home/features/service-fabric)。

### 安装 Azure PowerShell

若要安装 Azure PowerShell，请遵循上一节**安装 Azure PowerShell 并登录**中的步骤。跳过**登录到 Azure PowerShell** 小节。

### 使用本地服务帐户注册 Azure PowerShell 模块

>[AZURE.NOTE] 在启动生成代理之前执行此操作，否则它不会接受新的环境变量。

1. 按下 Win + R，然后键入 **regedit** 并按 Enter。

2. 右键单击节点 `HKEY_Users\.Default\Environment`，然后选择“新建”>“可扩展字符串值”。

3. 输入 `PSModulePath` 作为名称，输入 `%PROGRAMFILES%\WindowsPowerShell\Modules` 作为值。将 `%PROGRAMFILES%` 替换为 `PROGRAMFILES` 环境变量的值。

### 导入你的自动化证书

1.	将证书导入到你的生成计算机上。为此，请按以下步骤操作：

    a.将由脚本 CreateAndUpload-Certificate.ps1 创建的 PFX 文件复制到生成计算机。

    b.使用你之前传递给 `CreateAndUpload-Certificate.ps1` 的密码，打开管理员 PowerShell 提示符并运行以下命令。

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```

2.	运行证书管理器。

    a.打开 Windows 控制面板。右键单击“开始”按钮并选择“控制面板”。

    b.搜索**证书**。

    c.选择“管理工具”>“管理计算机证书”。

3.	授权本地服务帐户使用自动化证书。

    a.在“证书 - 本地计算机”下，展开“个人”，然后选择“证书”。

    b.在列表中查找你的证书。

    c.右键单击你的证书，然后选择“所有任务”>“管理私钥”。

    d.选择“添加”按钮，然后输入“本地服务”并单击”检查名称”。

    e.选择“确定”按钮，然后关闭证书管理器。

![](./media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### 注册你的生成代理

1.	下载 agent.zip。为此，请按以下步骤操作：

    a.登录到你的团队项目，如 **https://[your-VSTS-account-name].visualstudio.com**。

    b.选择屏幕右上角的齿轮图标。

    c.从控制面板中选择“代理池”选项卡。

    d.选择“下载代理”以下载 agent.zip 文件。

    e.将 agent.zip 复制到先前创建的生成计算机。

    f.将 agent.zip 解压缩到生成计算机上的 `C:\agent`（或任何具有短路径的位置）。

    >[AZURE.NOTE] 如果你打算构建 ASP.NET 5 Web 服务，建议为此文件夹选择可能的最短名称，以免在部署期间遇到 **PathTooLongExceptions** 错误。

2.	在管理员命令提示符下，运行 `C:\agent\ConfigureAgent.cmd`。该脚本会提示你输入以下参数：

|参数|值|
|---|---|
|代理名称|接受默认值，`Agent-[machine name]`。|
|TFS Url|将 URL 输入到你的团队项目，如 `https://[your-VSTS-account-name].visualstudio.com`。|
|代理池|输入代理池的名称。（如果尚未创建代理池，则接受默认值。）|
|工作文件夹|接受默认值。生成代理将在此文件夹中实际生成你的应用程序。注意：如果你打算构建 ASP.NET 5 Web 服务，建议为此文件夹选择可能的最短名称，以免在部署期间遇到 PathTooLongExceptions 错误。|
|安装为 Windows 服务？|默认值是 N。请将值更改为 **Y**。|
|用于运行服务的用户帐户|接受默认值，`NT AUTHORITY\LocalService`。|
|是否取消配置现有代理？|接受默认值，**N**。|

3.  系统将提示你输入凭据。输入有权访问你的团队项目的 Microsoft 帐户的凭据。

4.  验证已注册你的生成代理。为此，请按以下步骤操作：

    a.返回到 Web 浏览器（应位于 `https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`），并刷新页面。

    b.选择你之前在运行 ConfigureAgent.ps1 时选择的代理池。

    c.验证你的生成代理显示于列表中且其状态突出显示为绿色。如果突出显示为红色，则表示生成代理连接到 Team Services 时遇到问题。

![](./media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## 创建生成定义

>[AZURE.NOTE] 即使在单独的计算机上，从这些说明创建的生成定义也将不支持多个并发生成。这是因为每个生成会争夺同一个资源组/群集。如果想要运行多个生成代理，你将需要修改以下说明/脚本，以防止这种干扰。

### 将持续集成脚本添加到应用程序的源代码管理

1.	将 [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) 解压缩到到你的计算机上的任何文件夹。将 `Powershell\Automation` 的内容复制到源代码管理中的任何文件夹。

2.	签入生成的文件。

### 创建生成定义

1.	创建空的生成定义。为此，请按以下步骤操作：

    a.在 Visual Studio Team Services 中打开你的项目。

    b.选择“生成”选项卡。

    c.选择绿色“+”号以创建新的生成定义。

    d.选择“空”，然后选择“下一步”按钮。

    e.验证是否选择了正确的存储库和分支。

    f.选择向其注册你的生成代理的代理队列，并选中“持续集成”复选框。

2.	在“变量”选项卡上，使用这些值创建以下变量。

|变量|值|密钥|允许在队列时|
|---|---|---|---|
|BuildConfiguration|版本||X|
|BuildPlatform|x64|||
|ServicePrincipalPassword|传递给 CreateServicePrincipal.ps1 的密码|X||
|ServicePrincipalId|在 CreateServicePrincipal.ps1 的输出中|||
|ServicePrincipalTenantId|在 CreateServicePrincipal.ps1 的输出中|||
|ServicePrincipalSubscriptionId|在 CreateServicePrincipal.ps1 的输出中|||
|ServiceFabricCertificateThumbprint|在 GenerateCertificate.ps1 的输出中|||
|ServiceFabricKeyVaultId|在 GenerateCertificate.ps1 的输出中|||
|ServiceFabricCertificateSecretId|在 GenerateCertificate.ps1 的输出中|||
|ServiceFabricClusterName|所需的任意名称。|||
|ServiceFabricClusterResourceGroupName|所需的任意名称。|||
|ServiceFabricClusterLocation|与密钥保管库的位置匹配的任何名称。|||
|ServiceFabricClusterAdminPassword|所需的任意名称。|X||
|ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
|ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>`注意：将忽略发布配置文件中的连接终结点。改为使用临时群集的连接终结点。|||
|ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
|ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` 它应是包含 .sfproj 文件的文件夹。||||

3.  保存生成定义并为其命名。（稍后可根据需要更改此名称。）

### 添加“生成”步骤。
@<Author GitHub alias> – 请检查对你的文章的复制编辑，解决我在注释中输入的任何问题，并让我了解我是否在任何位置更改了技术含义。

1.	在“生成”选项卡上，选择“添加生成步骤...”命令。

2.	选择“生成”>“MSBuild”。

3.	选择生成步骤的名称旁边的铅笔图标，并将其重命名为“生成”。

4.	选择“解决方案”字段旁边的“...”按钮，然后选择你的 .sln 文件。

5.	输入 `$(BuildPlatform)` 作为“平台”。

6.	输入 `$(BuildConfiguration)` 作为“配置”。

7.	选中“还原 NuGet 包”复选框（如果尚未选中）。

8.	保存生成定义。

### 添加“包”步骤

1.	在“生成”选项卡上，选择“添加生成步骤...”命令。

2.	选择“生成”>“MSBuild”。

3.	选择生成步骤的名称旁边的铅笔图标并将其重命名为“包”。

4.	选择“解决方案”字段旁边的“…”按钮，并选择应用程序项目的 .sfproj 文件。

5.	输入 `$(BuildPlatform)` 作为“平台”。

6.	输入 `$(BuildConfiguration)` 作为“配置”。

7.	输入 `/t:Package`作为“MSBuild 参数”。

8.	清除“还原 NuGet 包”复选框（如果尚未清除）。

9.	保存生成定义。

### 添加“删除群集资源组”步骤

如果上一个生成在其自身后未清理（例如，如果由于取消了生成而未完成清理），可能存在与新资源组冲突的现有资源组。为了避免冲突，在创建新资源组前，请清理任何剩余的资源组（及其关联资源）。

1.	在“生成”选项卡上，选择“添加生成步骤...”命令。

2.	选择“实用程序”>“PowerShell”。

3.	选择生成步骤的名称旁边的铅笔图标并将其重命名为“删除群集资源组”。

4.	选择“脚本文件名”旁边的“...”命令。导航到你解压缩自动化脚本的位置，然后选择“Remove-ClusterResourceGroup.ps1”。

5.	输入 `-ServicePrincipalPassword "$(ServicePrincipalPassword)"` 作为“参数”。

6.	保存生成定义。

### 添加“设置并部署到安全群集”步骤

1.	在“生成”选项卡上，选择“添加生成步骤...”命令。

2.	选择“实用程序”>“PowerShell”。

3.	选择生成步骤的名称旁边的铅笔图标并将其重命名为“设置并部署到安全群集”。

4.	选择“脚本文件名”旁边的“...”按钮。导航到你解压缩自动化脚本的位置，然后选择“ProvisionAndDeploy-SecureCluster.ps1”。

5.	输入 `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"` 作为“参数”。

6.	保存生成定义。

### 添加“删除群集资源组”步骤

现在，你已使用完临时群集，应对其进行清理。如果不这样做，你将继续为临时群集付费。此步骤将删除资源组，从而删除组中的群集和所有其他资源。

>[AZURE.NOTE] 此步骤与前面的“删除群集资源组”步骤之间有一个区别：此步骤应检查“始终运行”。

1.	在“生成”选项卡上，选择“添加生成步骤...”命令。

2.	选择“实用程序”>“PowerShell”。

3.	选择生成步骤的名称旁边的铅笔图标并将其重命名为“删除群集资源组”。

4.	选择“脚本文件名”旁边的“...”按钮。导航到你解压缩自动化脚本的位置，然后选择“RemoveClusterResourceGroup.ps1”。

5.	输入 `-ServicePrincipalPassword "$(ServicePrincipalPassword)` 作为“参数”。

6.	在“控制选项”下，选中“始终运行”复选框。

7.	保存生成定义。

### 试用

单击“为生成排队”以启动生成。在推送或签入时也将触发生成。


## 备选解决方案

前面的说明为每个生成创建新的群集，并在生成的末尾删除它。如果你希望改为让每个生成执行应用程序升级（到现有群集），请使用以下步骤：

1.	通过 Azure 门户或 Azure PowerShell 手动创建测试群集。你可以参阅 `ProvisionAndDeploy-SecureCluster.ps1` 脚本作为参考。

2.	按照[这些说明](/documentation/articles/service-fabric-visualstudio-configure-upgrade)配置发布配置文件，以支持应用程序升级。

3.	将“设置并部署到安全群集”步骤替换为直接调用 Deploy-FabricApplication.ps1（并将其传递给你的发布配置文件）的步骤。

4.	从你的生成定义中删除两个“删除群集资源组”生成步骤。

## 后续步骤

若要了解有关与 Service Fabric 应用程序持续集成的详细信息，请阅读以下文章：

- [生成文档主页](https://msdn.microsoft.com/zh-cn/Library/vs/alm/Build/overview)
- [部署生成代理](https://msdn.microsoft.com/zh-cn/Library/vs/alm/Build/agents/windows)
- [创建和配置生成定义](https://msdn.microsoft.com/zh-cn/Library/vs/alm/Build/vs/define-build)

<!---HONumber=Mooncake_0321_2016-->