---
title: Azure 映像生成器服务 DevOps 任务
description: Azure DevOps 任务：将生成项目注入 VM 映像，以便安装和配置应用程序和操作系统。
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 9f948fcc8ad36f8bef8b1ab6a1b74131faea9bd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88068116"
---
# <a name="azure-image-builder-service-devops-task"></a>Azure 映像生成器服务 DevOps 任务

本文介绍如何使用 Azure DevOps 任务将生成项目注入 VM 映像，以便安装和配置应用程序和操作系统。

## <a name="devops-task-versions"></a>DevOps 任务版本
有两个 Azure VM 映像生成器 (AIB) DevOps 任务：

* ["稳定" 的 AIB 任务](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)，这使我们可以将最新的更新和功能放入最新的更新和功能中，让客户对其进行测试，然后将其提升到 "稳定" 任务，稍后约为1周。 


* ["不稳定" 的 AIB 任务](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary)，这使我们可以将最新的更新和功能放入最新的更新和功能，让客户在将其升级到 "稳定" 任务之前对其进行测试。 如果没有报告的问题，并且我们的遥测未显示任何问题，请在大约1周后将任务代码提升为 "稳定"。 

## <a name="prerequisites"></a>必备条件

* [从 Visual Studio Marketplace 安装稳定的 DevOps 任务](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)。
* 你必须具有 VSTS DevOps 帐户，并创建了一个生成管道
* 在管道使用的订阅中注册并启用映像生成器功能要求：
    * [Az PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-powershell#register-features)
    * [Az CLI](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder#register-the-features)
    
* 在源映像资源组中创建标准的 Azure 存储帐户，可以使用其他资源组/存储帐户。 存储帐户用于将生成项目从 DevOps 任务传输到映像。

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>将任务添加到发布管道

选择**发布管道**  >  **编辑**

在用户代理上，选择 "添加"， *+* 然后搜索 " **映像生成器**"。 选择“添加”  。

设置以下任务属性：

### <a name="azure-subscription"></a>Azure 订阅

从下拉菜单中选择想要将图像生成器运行到的订阅。 使用源映像所在的同一订阅以及要在其中分发图像的位置。 需要为映像生成器参与者授予对订阅或资源组的访问权限。

### <a name="resource-group"></a>资源组

使用将在其中存储临时映像模板项目的资源组。 创建模板项目时，会创建一个附加的临时映像生成器资源组 `IT_<DestinationResourceGroup>_<TemplateName>_guid` 。 临时资源组存储图像元数据，如脚本。 在任务结束时，将删除图像模板项目和临时图像生成器资源组。
 
### <a name="location"></a>位置

此位置是将在其中运行映像生成器的区域。 仅支持一组数量的 [区域](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#regions) 。 源映像必须位于此位置。 例如，如果您使用的是共享映像库，则副本必须存在于该区域。

### <a name="managed-identity-required"></a>需要 (托管标识) 
映像生成器需要一个托管标识，该标识用于读取源自定义映像，连接到 Azure 存储，以及创建自定义映像。 请参阅[此处](https://aka.ms/azvmimagebuilder#permissions)了解详细信息。

### <a name="vnet-support"></a>VNET 支持

目前，DevOps 任务不支持指定现有的子网，但如果想要利用现有的 VNET，则可以使用 ARM 模板，其中嵌套了图像生成器模板，有关如何实现此目的的示例，请参阅 Windows 映像生成器模板示例，或者使用 [AZ AIB PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-powershell)。

### <a name="source"></a>源

源映像必须是受支持的映像生成器 OSs。 你可以选择在运行映像生成器的同一区域中的现有自定义映像：
* 托管映像-需要传入 resourceId，例如：
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azure 共享映像库-需要传入映像版本的 resourceId，例如：
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    如果需要获取最新的共享映像库版本，你可以先使用 AZ PowerShell 或 AZ CLI 任务，然后才能获取最新版本并设置 DevOps 变量。 在 AZ VM Image Builder DevOps 任务中使用该变量。 有关详细信息，请参阅 [示例](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery)。

*  (Marketplace) 基础映像有一个下拉列表，其中包含常用映像，这些映像将始终使用受支持的操作系统的 "最新版本"。 

    如果基本映像不在列表中，则可以使用指定确切的映像 `Publisher:Offer:Sku` 。

    基本映像版本 (可选) -可以提供要使用的映像版本，默认值为 `latest` 。

### <a name="customize"></a>自定义

#### <a name="provisioner"></a>配置程序

最初，支持两个定制器- **Shell** 和 **PowerShell**。 仅支持 inline。 如果要下载脚本，则可以传递内联命令来执行此操作。

对于操作系统，请选择 "PowerShell" 或 "Shell"。

#### <a name="windows-update-task"></a>Windows 更新任务

仅适用于 Windows，任务会在自定义结束时运行 Windows 更新。 它处理所需的重启。

执行以下 Windows 更新配置：
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
它将安装不是预览版的重要和推荐的 Windows 更新。

#### <a name="handling-reboots"></a>处理重启
目前，DevOps 任务不支持重启 Windows 版本，如果你尝试使用 PowerShell 代码重新启动，则生成将失败。 不过，你可以使用代码重新启动 Linux 版本。

#### <a name="build-path"></a>生成路径

该任务设计为能够将 DevOps 生成版本项目注入到映像中。 若要执行此操作，需要设置生成管道。 在发布管道的设置中，您必须添加生成项目的存储库。

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="选择 &quot;在发布管道中添加项目&quot;。&quot;:::

选择 &quot; **生成路径** " 按钮，选择要放置在映像上的生成文件夹。 映像生成器任务将复制其中的所有文件和目录。 正在创建映像时，图像生成器会根据 OS 将文件和目录部署到不同的路径中。

> [!IMPORTANT]
> 添加存储库项目时，可能会发现该目录以下划线 *_* 为前缀。 下划线会导致内联命令出现问题。 在命令中使用相应的引号。
> 

下面的示例说明了它的工作原理：

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="选择 &quot;在发布管道中添加项目&quot;。&quot;:::

选择 &quot; **生成路径** ":::


* Windows-文件存在于中 `C:\` 。 将创建一个名为的目录 `buildArtifacts` ，其中包含 `webapp` 目录。

* Linux-文件存在于中  `/tmp` 。 `webapp`此时会创建目录，其中包括所有文件和目录。 必须移动此目录中的文件。 否则，它们会被删除，因为它在临时目录中。

#### <a name="inline-customization-script"></a>内联自定义脚本

* Windows-可以输入以逗号分隔的 PowerShell 内联命令。 如果要在生成目录中运行脚本，可以使用：

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

* Linux-在 Linux 系统上，生成项目放置在 `/tmp` 目录中。 但是，在许多 Linux 操作系统上，重新启动时，将删除/tmp 目录内容。 如果希望项目存在于映像中，则必须创建另一个目录并将其复制到其中。  例如：

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    如果你使用的是 "/tmp" 目录，则可以使用以下代码执行该脚本。
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>构建映像后，生成项目会发生什么情况？

> [!NOTE]
> 图像生成器不会自动删除生成项目，因此强烈建议您始终具有删除生成项目的代码。
> 

* Windows 映像生成器会将文件部署到 `c:\buildArtifacts` 目录。 此目录是持久的，必须删除该目录。 您可以在执行的脚本中删除它。 例如：

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux-生成项目放置在 `/tmp` 目录中。 但是，在许多 Linux 操作系统上，重新启动时，将 `/tmp` 删除目录内容。 建议你具有删除内容的代码，而不是依赖 OS 删除内容。 例如：

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>映像生成的总长度

目前不能在 DevOps 管道任务中更改总长度。 它使用默认值240分钟。 如果要增加 [buildTimeoutInMinutes](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#properties-buildtimeoutinminutes)，可以在发布管道中使用 AZ CLI task。 配置任务以复制模板并提交模板。 有关示例，请参阅此 [解决方案](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)，或使用 Az PowerShell。


#### <a name="storage-account"></a>存储帐户

选择你在 "先决条件" 中创建的存储帐户。 如果在列表中看不到该功能，则图像生成器没有相应的权限。

生成开始时，图像生成器将创建一个名为的容器 `imagebuilder-vststask` 。 容器是存储库中的生成项目的位置。

> [!NOTE]
> 每次生成后，需要手动删除存储帐户或容器。
>

### <a name="distribute"></a>分发

支持3种分发类型。

#### <a name="managed-image"></a>托管映像

* ResourceID
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* 位置

#### <a name="azure-shared-image-gallery"></a>Azure 共享映像库

共享映像库 **必须** 已存在。

* ResourceID 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* 区域：区域列表，以逗号分隔。 例如，westus、eastus、centralus

#### <a name="vhd"></a>VHD

不能将任何值传递到此，因此，映像生成器会将 VHD 发送到 `IT_<DestinationResourceGroup>_<TemplateName>` *vhd* 容器中的临时映像生成器资源组。 当你启动发布版本时，映像生成器将发出日志。 完成后，它将发出 VHD URL。

### <a name="optional-settings"></a>可选设置

* [Vm 大小](image-builder-json.md#vmprofile) -可以从 *Standard_D1_v2*的默认值覆盖 vm 大小。 你可以重写以减少总自定义时间，或者，因为你要创建依赖于某些 VM 大小（如 GPU/HPC 等）的映像。

## <a name="how-it-works"></a>工作原理

当你创建发布时，该任务将在存储帐户中创建一个名为 *imagebuilder-vststask*的容器。 它 zips 并上传生成项目，并为 zip 文件创建 SAS 令牌。

任务使用传递给任务的属性来创建图像生成器模板项目。 该任务将执行以下操作：
* 下载生成项目 zip 文件和任何其他关联的脚本。 文件保存在临时映像生成器资源组中的存储帐户中 `IT_<DestinationResourceGroup>_<TemplateName>` 。
* 创建一个名为前缀 *t_* 和10位单调整数的模板。 模板保存到所选资源组。 在资源组中生成的持续时间内存在模板。 

示例输出：

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

开始生成映像时，将在版本日志中报告运行状态：

```text
starting run template...
```

映像生成完成后，会看到类似于以下文本的输出：

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

图像模板和 `IT_<DestinationResourceGroup>_<TemplateName>` 将被删除。

您可以采用 "$ (imageUri) " VSTS 变量并在下一任务中使用它，或者只是使用值并生成一个 VM。

## <a name="output-devops-variables"></a>输出 DevOps 变量

源 marketplace 映像的发布/产品/SKU/版本：
* $ (pirPublisher) 
* $ (pirOffer) 
* $ (pirSku) 
* $ (pirVersion) 

映像 URI-分布式映像的 ResourceID：
* $ (imageUri) 

## <a name="faq"></a>常见问题解答

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>我是否可以使用在 DevOps 之外创建的现有映像模板？

目前没有。

### <a name="can-i-specify-the-image-template-name"></a>能否指定映像模板名称？

不是。 使用唯一的模板名称，然后将其删除。

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>图像生成器失败。 如何进行故障排除？

如果生成失败，DevOps 任务不会删除暂存资源组。 可以访问包含生成自定义日志的暂存资源组。

VM 映像生成器任务的 DevOps 日志中会出现错误，请参阅自定义日志位置。 例如：

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="选择 &quot;在发布管道中添加项目&quot;。&quot;:::

选择 &quot; **生成路径** " 以查看项目。


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure 映像生成器概述](image-builder-overview.md)。
