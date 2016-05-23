<properties
	pageTitle="Azure 资源组项目部署脚本概述 | Azure"
	description="介绍 Azure 资源组部署项目中 PowerShell 脚本的工作方式。"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.date="02/04/2016"
	wacn.date="" />

# Azure 资源组项目部署脚本概述

Azure 资源组部署项目可帮助你将文件及其他项目暂存和部署到 Azure。当你在 Visual Studio 中创建 Azure Resource Manager 部署项目时，会将一个名为 **Deploy-AzureResourceGroup.ps1** 的 PowerShell 脚本添加到项目。本主题提供有关此脚本的作用以及如何在 Visual Studio 内部和外部执行该脚本的详细信息。

## 该脚本有什么作用？

Deploy-AzureResourceGroup.ps1 脚本将执行两项操作，这些操作对于部署工作流而言非常重要。

- 上载模板部署所需的任何文件或项目
- 部署模板

脚本的第一个部分将上载部署的文件和项目，脚本中的最后一个 cmdlet 将实际部署模板。例如，如果需要使用脚本来配置虚拟机，部署脚本首先会将配置脚本安全上载到 Azure 存储帐户。这样，Azure Resource Manager 便可在预配期间配置虚拟机。

由于并非所有模板部署都需要上载额外的项目，因此将评估名为 “uploadArtifacts” 的开关参数。如果需要上载任何项目，则在调用脚本时会设置 “uploadArtifacts” 开关。请注意，主模板文件和参数文件不需要上载。只有其他文件（例如配置脚本、嵌套部署模板和应用程序文件）需要上载。

## 详细的脚本描述

下面是 Deploy-AzureResourceGroup.ps1 Azure PowerShell 脚本的选择部分执行的操作描述。

>[AZURE.NOTE] 此部分描述 Deploy-AzureResourceGroup.ps1 脚本的版本 1.0。

1.	声明 Azure Resource Manager 部署项目所需的参数。在创建项目时已设置某些参数的默认值。可以在脚本中更改这些默认值，或者在执行脚本之前添加不同的参数值。

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

    |参数|说明|
    |---|---|
    |$ResourceGroupLocation|资源组的区域或数据中心位置，例如“美国东部”或“中国北部”。|
    |$ResourceGroupName|Azure 资源组的名称。|
    |$UploadArtifacts|二进制值，指示是否需要从系统将项目上载到 Azure。|
    |$StorageAccountName|上载项目时所在的 Azure 存储帐户的名称。|
    |$StorageAccountResourceGroupName|包含存储帐户的 Azure 资源组的名称。|
    |$StorageContainerName|用于上载项目的存储容器名称。|
    |$TemplateFile|部署文件 (`<app name>.json`) 在 Azure 资源组项目中的路径。|
    |$TemplateParametersFile|参数文件 (`<app name>.parameters.json`) 在 Azure 资源组项目中的路径。|
    |$ArtifactStagingDirectory|本地上载的项目在系统上的路径，包括 PowerShell 脚本根文件夹。此路径可以是脚本位置的绝对或相对路径。|
    |$AzCopyPath|AzCopy.exe 工具从中复制其 .zip 文件的路径，包括 PowerShell 脚本根文件夹。此路径可以是脚本位置的绝对或相对路径。|
    |$DSCSourceFolder|DSC (Desired State Configuration) 源文件夹的路径，包括 PowerShell 脚本根文件夹。此路径可以是脚本位置的绝对或相对路径。有关详细信息，请参阅 [Introducing the Azure PowerShell DSC (Desired State Configuration) extension](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)（Azure PowerShell DSC (Desired State Configuration) 扩展简介），如果适用。|

1.	检查项目是否需要上载到 Azure。如果不需要，则跳到步骤 11。否则，请执行以下步骤。

1.	将具有相对路径的所有变量转换为绝对路径。例如，将类似于 `..\Tools\AzCopy.exe` 的路径更改为 `C:\YourFolder\Tools\AzCopy.exe`。此外，将变量 “ArtifactsLocationName” 和 “ArtifactsLocationSasTokenName” 初始化为 null。“ArtifactsLocation” 和 “SaSToken” 可能是模板的参数。如果在参数文件中读取之后其值为 null，脚本将为它们生成值。

    Azure 工具将在模板中使用参数值 “\_artifactsLocation” 和 “\_artifactsLocationSasToken” 来管理项目。如果 PowerShell 脚本找到具有这些名称的参数，但参数值未提供，则脚本将上载项目，并针对这些参数返回适当的值。然后，通过 `@OptionsParameters` 将它们传递给 cmdlet。

	|变量|说明|
    |---|---|
    |ArtifactsLocationName|Azure 项目所在的路径。|
    |ArtifactsLocationSasTokenName|脚本用于在服务总线上进行身份验证的 SAS（共享访问签名）令牌名称。有关详细信息，请参阅 [Shared Access Signature Authentication with Service Bus](service-bus-shared-access-signature-authentication.md)（服务总线的共享访问签名身份验证）。|

	```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.	此部分检查 <app name>.parameters.json 文件（称为“参数文件”）是否包含名为 **parameters** 的父节点（在 `else` 块中）。如果不包含，则表示该文件没有父节点。可以接受任一格式。
    
	```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.	循环访问 JSON 参数的集合。如果向 “\_artifactsLocation” 或 “\_artifactsLocationSasToken” 分配了参数值，则使用这些值设置变量 “$OptionalParameters”。这可以防止脚本意外覆盖你提供的任何参数值。

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.	获取用于保存部署项目的存储帐户资源的存储帐户密钥和上下文。

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.	如果你使用 PowerShell DSC 来配置虚拟机，DSC 扩展要求项目位于单个 zip 文件中。因此，需针对 DSC 配置创建 .zip 存档文件。为此，请检查 $DSCSourceFolder 是否存在。如果 DSC 配置存在，请将它删除，然后创建一个名为 dsc.zip 的新压缩文件。

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.	如果未在参数文件中提供 Azure 项目的路径，请设置上载项目时使用的 PowerShell 脚本的路径。为此，请结合使用存储帐户的终结点路径和存储容器名称来创建一个路径。然后，使用此新路径更新参数文件。

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.	使用 **AzCopy** 实用工具（包含在 Azure 资源组部署项目的“工具”文件夹中），从本地存储放置路径将所有文件复制到你的联机 Azure 存储帐户。如果此步骤失败，请退出脚本，因为部署没有所需的项目不会成功。

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.	如果未在参数文件中提供项目位置的 SAS 令牌，请创建一个令牌以提供对联机存储容器的临时只读访问权限。然后，将该 SAS 令牌作为“optionalParameter”传递到 cmdline。 请注意，在 cmdline 中传递的任何参数将优先于参数文件中提供的值。

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  如果资源组不存在，请创建资源组，并检查模板和参数文件中是否存在导致无法成功部署的任何身份验证错误。

    ```
	# Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. 最后，请部署模板。此代码将使用时间戳为部署创建唯一名称。

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## 部署资源组

### 在 Visual Studio 中部署资源组

1. 在 Azure 资源组项目的快捷菜单中，选择“部署”>“新建部署”。

    ![][0]

1. 在“部署到资源组”对话框中的下拉列表框内选择要部署的现有资源组，或者选择“&lt;新建的...&gt;”以创建新资源组。

    ![][1]

1. 如果出现提示，请在“创建资源组”对话框中输入资源组名称和位置，然后选择“创建”按钮。

    ![][2]

1. 选择“编辑参数”按钮以查看“编辑参数”对话框，然后输入所有缺少的参数值。

    ![][3]

	>[AZURE.NOTE] 如果任何必需参数需要值，此对话框将在部署时自动出现。

    ![][4]

1. 输入完参数值后，选择“存储”按钮，然后选择“部署”按钮。

    部署脚本 (Deploy-AzureResourceGroup.ps1) 将会运行，模板和所有项目将部署到 Azure。

### 使用 PowerShell 部署资源组

如果你要运行脚本而不使用 Visual Studio 的“部署”命令和 UI，请在脚本的快捷菜单中选择“使用 PowerShell ISE 打开”。

![][5]


## 命令部署示例

### 使用默认值部署

本示例演示如何使用默认参数值运行脚本。（由于位置参数没有默认值，因此必须提供值。）

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### 通过覆盖默认值进行部署

本示例演示如何运行脚本来部署与默认值不同的模板和参数文件。

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### 使用 UploadArtifacts 进行暂存部署

本示例演示如何运行脚本以从发布文件夹上载项目，并部署非默认模板。

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

本示例演示如何在 Visual Studio Online 的 Azure PowerShell 任务中运行脚本。

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## 后续步骤
请阅读 [Azure Resource Manager Overview（Azure Resource Manager 概述）](/documentation/articles/resource-group-overview)，了解有关 Azure Resource Manager 的详细信息。

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png
<!---HONumber=Mooncake_0516_2016-->