<properties
	pageTitle="更新 Azure 资源组项目 PowerShell 脚本 | Azure"
	description="描述手动更新 Azure 资源组部署项目附带的 PowerShell 脚本所需的步骤。"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.date="02/04/2016"
	wacn.date="" />

# 更新 Azure 资源组项目 PowerShell 脚本

Visual Studio 中的 Azure 资源组部署项目使用 Azure PowerShell 脚本来帮助准备资源并将其从 Visual Studio 部署到 Azure。项目中脚本使用的 Azure PowerShell 的版本为 0.9.8。但已发布新版本的 Azure PowerShell（1.0 版）。

虽然新版本的 Azure PowerShell 带来了新功能并且让使用 Azure Resource Manager 拥有一流的体验，但也对几个 cmdlet 功能做了更改而使脚本的早期版本不兼容。但是，你可以修改脚本来修复这些问题，然后在 Visual Studio 外的 PowerShell 命令窗口中运行该脚本。本文提供了有关待修复问题及其位置的信息。除了特定的修复外，本文结尾处还列出了已完全修改的脚本的副本。

## 解决方法选项

安装新的 Azure PowerShell 后，Azure 资源组项目中可能以两种方式出现问题。

- 因为 Visual Studio 中的部署对话框无法找到新的 Azure cmdlet，所以系统会提示你安装它们。然而，由于新版本的 cmdlet 中已重命名模块，所以 Visual Studio 将无法找到新模块。因此，如果你尝试从 Visual Studio 中安装它们，就会陷入循环。

- 如果在 Visual Studio 外的 PowerShell 命令窗口中运行脚本，你可能收到错误：

	““Switch-AzureMode”术语未识别为 cmdlet、函数、脚本文件或可运行程序的名称。检查名称的拼写，如果包含路径，请验证该路径是否正确，然后重试”。

如果新的 Azure PowerShell 阻止你部署 Azure 资源组项目，可通过以下选项进行修复。

- 可以卸载最新版本的 Azure PowerShell 并使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)重新安装以前的版本 (0.9.8)。

- 可以对部署项目的 PowerShell 脚本做特定定向更改以解决此问题，然后在 PowerShell 命令窗口中手动运行该脚本。不能从 Visual Studio 中的“部署”菜单项运行脚本。本文稍后列出了更新 PowerShell 脚本的步骤，并给出了已完全更新的脚本的副本。

## 更新 Azure PowerShell 脚本
这些说明指的是行号。若要在 Visual Studio 中启用行编号，请参阅[如何：在编辑器中显示行号](https://msdn.microsoft.com/library/ms165340.aspx)。

1. 在第 61 行，替换以下代码：

	```
	if ($StorageAccountResourceGroupName) {
	        Switch-AzureMode AzureResourceManager
	        $StorageAccountKey = (Get-AzureStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	    }
	    else {
	        Switch-AzureMode AzureServiceManagement
	        $StorageAccountKey = (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
	    }
	$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
	```

	替换为：

	```
	$StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	$StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
	```

1. 在第 87 行，替换以下代码：

	```
	$ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission are
	```

	替换为：

	```
	$ArtifactsLocationSasToken = New-AzureRMStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
	```
1. 在第 95 行，替换此代码：

	```
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Force –Verbose
	```

	替换为以下代码：

	```
	New-AzureRMResourceGroup `
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-Verbose -Force -ErrorAction Stop

	New-AzureRMResourceGroupDeployment `
		-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
		-ResourceGroupName $ResourceGroupName `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Verbose -Force
	```

## 已更新的脚本
以进行前述所有更新后的已修改脚本。

```
#Requires -Version 3.0

Param(
  [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
  [string] $ResourceGroupName = '$defaultResourceGroupName$',  
  [switch] $UploadArtifacts,
  [string] $StorageAccountName,
  [string] $StorageAccountResourceGroupName,
  [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
  [string] $TemplateFile = '..\Templates\$deployTemplateFileName$.json',
  [string] $TemplateParametersFile = '..\Templates\$deployTemplateFileName$.parameters.json',
  [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
  [string] $AzCopyPath = '..\Tools\AzCopy.exe',
  [string] $DSCSourceFolder = '..\DSC'
)

Import-Module Azure -ErrorAction SilentlyContinue

try {
  [Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("VSAzureTools-$UI$($host.name)".replace(" ","_"), "2.7.2")
} catch { }

Set-StrictMode -Version 3

$OptionalParameters = New-Object -TypeName Hashtable
$TemplateFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateFile)
$TemplateParametersFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateParametersFile)

if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)

    # Parse the parameter file and update the values of artifacts location and artifacts location SAS token if they are present
    $JsonContent = Get-Content $TemplateParametersFile -Raw | ConvertFrom-Json
    $JsonParameters = $JsonContent | Get-Member -Type NoteProperty | Where-Object {$_.Name -eq "parameters"}

    if ($JsonParameters -eq $null) {
        $JsonParameters = $JsonContent
    }
    else {
        $JsonParameters = $JsonContent.parameters
    }

    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }

    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context

    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }

    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }

    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }

    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
}

# Create or update the resource group using the specified template file and template parameters file
New-AzureRMResourceGroup `
	-Name $ResourceGroupName `
	-Location $ResourceGroupLocation `
	-Verbose -Force -ErrorAction Stop

New-AzureRMResourceGroupDeployment `
	-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
	-ResourceGroupName $ResourceGroupName `
	-TemplateFile $TemplateFile `
	-TemplateParameterFile $TemplateParametersFile `
	@OptionalParameters `
	-Verbose -Force

```

<!---HONumber=Mooncake_0516_2016-->