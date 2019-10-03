---
title: 使用 Azure 映像生成器 (预览版) 创建 Linux VM
description: 使用 Azure 映像生成器创建 Linux VM。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 1bac04bbb67c7472de92c6da322121bafc20a560
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695434"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>预览版：使用 Azure 映像生成器创建 Linux VM

本文介绍如何使用 Azure 映像生成器和 Azure CLI 创建自定义 Linux 映像。 本文中的示例使用三[个不同的](image-builder-json.md#properties-customize)自定义程序来自定义映像:

- Shell (ScriptUri)-下载并运行[shell 脚本](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)。
- Shell (内联)-运行特定命令。 在此示例中, 内联命令包括创建目录和更新操作系统。
- 文件-将[文件从 GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)复制到 VM 上的某个目录中。

你还可以指定`buildTimeoutInMinutes`。 默认值为240分钟, 可以增加生成时间以允许较长的运行生成。

我们将使用示例 json 模板来配置映像。 我们使用的 json 文件是: [helloImageTemplateLinux](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json)。 

> [!IMPORTANT]
> Azure 映像生成器目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>注册功能
若要在预览期间使用 Azure 映像生成器, 需要注册新功能。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

检查功能注册的状态。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

检查你的注册。

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

如果未注册, 请运行以下内容:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="setup-example-variables"></a>安装程序示例变量

我们将重复使用某些信息, 因此我们将创建一些变量来存储该信息。


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

为订阅 ID 创建一个变量。 你可以使用`az account show | grep id`获取此。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>创建资源组。
这用于存储映像配置模板项目和映像。

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>设置资源组的权限
在资源组中, 为映像生成器 "参与者" 创建映像的权限。 如果没有适当的权限, 映像生成将会失败。 

`--assignee`该值是映像生成器服务的应用注册 ID。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>下载模板示例

已创建一个参数化示例图像配置模板供你使用。 下载示例 json 文件, 并将其配置为先前设置的变量。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

您可以根据需要修改此示例。 例如, 你可以增加的值`buildTimeoutInMinutes`以允许较长时间运行的生成。 您可以使用文本编辑器 (如`vi`) 在 Cloud Shell 中编辑该文件。

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> 对于源映像, 必须始终[指定版本](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), 不能使用`latest`。
>
> 如果添加或更改要在其中分发映像的资源组, 则需要确保为[资源组设置权限](#set-permissions-on-the-resource-group)。


## <a name="submit-the-image-configuration"></a>提交映像配置
将映像配置提交给 VM 映像生成器服务

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

如果成功完成, 它将返回一条成功消息, 并在 $imageResourceGroup 中创建一个图像生成器配置模板项目。 如果启用 "显示隐藏的类型", 则可在门户中看到资源组。

此外, 在后台, 映像生成器会在你的订阅中创建一个暂存资源组。 映像生成器使用暂存资源组来生成映像。 资源组的名称将采用以下格式: `IT_<DestinationResourceGroup>_<TemplateName>`。

> [!IMPORTANT]
> 不要直接删除暂存资源组。 如果删除映像模板项目, 该项目会自动删除暂存资源组。 有关详细信息, 请参阅本文末尾的 "[清理](#clean-up)" 部分。

如果服务在映像配置模板提交期间报告故障, 请参阅[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)步骤。 你还需要在重试生成之前删除该模板。 删除模板:

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

等到生成完成后, 在本示例中, 可能需要10-15 分钟。

如果遇到任何错误, 请查看这些[故障排除](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)步骤。


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

获取用于创建 VM 的输出中的 IP 地址, 并使用它通过 SSH 连接到 VM。

```azurecli-interactive
ssh azureuser@<pubIp>
```

一旦建立 SSH 连接, 就会看到该映像已自定义一天的消息!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

完成`exit`后, 请键入结束 SSH 连接。

## <a name="check-the-source"></a>检查源

在 "映像生成器" 模板的 "属性" 中, 你将看到源映像、其运行的自定义脚本以及它的分发位置。

```azurecli-interactive
cat helloImageTemplateLinux.json
```

有关此 json 文件的更多详细信息, 请参阅[图像生成器模板参考](image-builder-json.md)

## <a name="clean-up"></a>清理

完成后, 可以删除资源。

删除图像生成器模板。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

删除映像资源组。

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>后续步骤

若要详细了解本文中使用的 json 文件的组件, 请参阅[图像生成器模板参考](image-builder-json.md)。
