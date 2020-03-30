---
title: 使用 Azure 映像生成器创建 Linux VM（预览）
description: 使用 Azure 映像生成器创建 Linux VM。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066683"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>预览：使用 Azure 映像生成器创建 Linux VM

本文介绍如何使用 Azure 映像生成器和 Azure CLI 创建自定义 Linux 映像。 本文中的示例使用三个不同的[自定义器](image-builder-json.md#properties-customize)来自定义映像：

- 外壳（脚本） - 下载并运行[一个shell脚本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)。
- 命令程序（内联） - 运行特定命令。 在此示例中，内联命令包括创建目录和更新操作系统。
- 文件 -[将 GitHub 的文件](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)复制到 VM 上的目录中。

还可以指定 。 `buildTimeoutInMinutes` 默认值为 240 分钟，您可以增加生成时间以允许较长的运行生成。

我们将使用示例 .json 模板来配置映像。 我们使用的 .json 文件在这里： [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

> [!IMPORTANT]
> Azure 映像生成器当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>注册功能
要在预览期间使用 Azure 映像生成器，需要注册新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

检查功能注册的状态。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

检查您的注册。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

如果他们不说已注册，则运行以下内容：

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="setup-example-variables"></a>设置示例变量

我们将反复使用一些信息片段，因此我们将创建一些变量来存储该信息。


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

为订阅 ID 创建变量。 您可以使用 获取此`az account show | grep id`功能。

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>创建资源组。
这用于存储图像配置模板项目和映像。

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>设置资源组的权限
授予图像生成器"参与者"在资源组中创建映像的权限。 如果没有适当的权限，映像生成将失败。 

该`--assignee`值是映像生成器服务的应用注册 ID。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>下载模板示例

已创建参数化的示例映像配置模板供您使用。 下载示例 .json 文件，然后使用前面设置的变量对其进行配置。

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

您可以根据需要修改此示例 .json。 例如，可以增加 的值`buildTimeoutInMinutes`以允许较长的运行生成。 您可以使用 文本编辑器（如`vi`）在云壳中编辑文件。

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 对于源映像，必须始终[指定版本](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)，不能使用`latest`。
>
> 如果添加或更改正在分发映像的资源组，则需要确保[为资源组设置了权限](#set-permissions-on-the-resource-group)。


## <a name="submit-the-image-configuration"></a>提交映像配置
将映像配置提交到 VM 映像生成器服务

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

如果成功完成，它将返回一条成功消息，并在$imageResourceGroup中创建映像生成器配置模板项目。 如果启用"显示隐藏类型"，则可以在门户中查看资源组。

此外，在后台，图像生成器会在订阅中创建暂存资源组。 映像生成器使用暂存资源组进行映像生成。 资源组的名称将采用此格式： `IT_<DestinationResourceGroup>_<TemplateName>`。

> [!IMPORTANT]
> 不要直接删除暂存资源组。 如果删除图像模板项目，它将自动删除暂存资源组。 有关详细信息，请参阅本文末尾的["清理](#clean-up)"部分。

如果服务在映像配置模板提交期间报告失败，请参阅[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)步骤。 在重试提交生成之前，还需要删除模板。 要删除模板：

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>启动映像生成

启动映像生成。


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

等待生成完成，对于此示例，可能需要 10-15 分钟。

如果您遇到任何错误，请查看这些[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)步骤。


## <a name="create-the-vm"></a>创建 VM

使用生成的映像创建 VM。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

从创建 VM 的输出中获取 IP 地址，并将其用于 VM 的 SSH。

```bash
ssh azureuser@<pubIp>
```

一旦建立 SSH 连接，您应该会看到图像是使用当天消息自定义的！

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

完成`exit`关闭 SSH 连接时键入。

## <a name="check-the-source"></a>检查源

在图像生成器模板中，在"属性"中，您将看到源映像、运行的自定义脚本以及分发位置。

```bash
cat helloImageTemplateLinux.json
```

有关此 .json 文件的详细信息，请参阅[图像生成器模板引用](image-builder-json.md)

## <a name="clean-up"></a>清除

完成后，可以删除资源。

删除映像生成器模板。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

删除图像资源组。

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>后续步骤

要了解有关本文中使用的 .json 文件组件的更多内容，请参阅[图像生成器模板引用](image-builder-json.md)。
