---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419143"
---
标准化虚拟机 （VM） 映像允许组织迁移到云并确保部署的一致性。 映像通常包括预定义的安全和配置设置以及必要的软件。 设置自己的映像管道需要时间、基础结构和设置，但使用 Azure VM 映像生成器，只需提供描述映像的简单配置，将其提交到服务，并生成映像并分发映像即可。
 
Azure VM 映像生成器（Azure 映像生成器）允许您从基于 Windows 或 Linux 的 Azure 应用商店映像、现有自定义映像或红帽企业 Linux （RHEL） ISO 开始，并开始添加自己的自定义项。 由于映像生成器是建立在[HashiCorp 打包器](https://packer.io/)之上的，因此还可以导入现有的打包器外壳预配器脚本。 您还可以在[Azure 共享映像库中](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)指定希望将映像托管的位置作为托管映像或 VHD。

> [!IMPORTANT]
> Azure 映像生成器当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="preview-features"></a>预览功能

对于预览，支持以下功能：

- 创建黄金基线映像，包括您的最低安全性和企业配置，并允许各部门进一步自定义它以满足其需求。
- 修补现有图像，图像生成器将允许您持续修补现有的自定义图像。
- 将映像生成器连接到现有虚拟网络，以便连接到现有配置服务器（DSC、Chef、Puppet 等）、文件共享或任何其他可路由的服务器/服务。
- 与 Azure 共享映像库集成，允许您全局分发、版本和缩放映像，并为您提供映像管理系统。
- 与现有映像生成管道集成，只需从管道调用映像生成器，或使用简单的预览映像生成器 Azure DevOps 任务。
- 将现有映像自定义管道迁移到 Azure。 使用现有脚本、命令和进程自定义映像。
- 以 VHD 格式创建图像。
 

## <a name="regions"></a>区域
Azure 映像生成器服务将在这些区域中预览。 图像可以分布在这些区域之外。
- 美国东部
- 美国东部 2
- 美国中西部
- 美国西部
- 美国西部 2
- 北欧
- 西欧

## <a name="os-support"></a>操作系统支持
AIB 将支持 Azure 应用商店基础操作系统映像：
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6， 7.7
- CentOS 7.6， 7.7
- SLES 12 SP4
- SLES 15， SLES 15 SP1
- Windows 10 RS5 企业/企业多会话/专业
- Windows 2016
- 视窗 2019

RHEL ISO 支持正在被弃用，请查看模板文档以了解更多详细信息。

## <a name="how-it-works"></a>工作原理


![Azure 映像生成器的概念绘图](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure 映像生成器是一个完全托管的 Azure 服务，由 Azure 资源提供程序访问。 Azure 映像生成器过程有三个主要部分：源、自定义和分发，这些部分在模板中表示。 下图显示了组件及其某些属性。 
 


**图像生成器过程** 

![Azure 映像生成器过程的概念绘图](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 将图像模板创建为 .json 文件。 此 .json 文件包含有关映像源、自定义项和分发的信息。 [Azure 映像生成器 GitHub 存储库](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)中有多个示例。
1. 将其提交到服务，这将在指定的资源组中创建图像模板项目。 在后台，图像生成器将根据需要下载源映像或 ISO 和脚本。 这些资源存储在订阅中自动创建的单独资源组中，其格式为：IT_\<目标资源组>_\<模板名称>。 
1. 创建图像模板后，可以生成映像。 在后台，映像生成器使用模板和源文件在IT_\<目标资源组>_\<模板名称>资源组中创建 VM（默认大小：Standard_D1_v2）、网络、公共 IP、NSG 和存储。
1. 作为映像创建的一部分，图像生成器会根据模板分发映像，然后删除为进程创建的IT_\<目标资源组>_\<模板名称>资源组中的其他资源。


## <a name="permissions"></a>权限

要允许 Azure VM 映像生成器将映像分发到托管映像或共享映像库，您需要为资源组中的服务"Azure 虚拟机映像生成器"（应用 ID：cf32a0cc-373c-47c9-9156-0db11f6a6dfc）提供"参与者"权限。 

如果使用现有的自定义托管映像或映像版本，则 Azure 映像生成器将需要对这些资源组的最小"读取器"访问权限。

可以使用 Azure CLI 分配访问权限：

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

您可以使用 PowerShell 分配访问权限：

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


如果未找到服务帐户，则可能意味着要添加角色分配的订阅尚未为资源提供程序注册。


## <a name="costs"></a>成本
使用 Azure 映像生成器创建、构建和存储映像时，将会产生一些计算、网络和存储成本。 这些成本与手动创建自定义映像的成本类似。 对于资源，您将按 Azure 费率收费。 

在映像创建过程中，文件被下载并存储在`IT_<DestinationResourceGroup>_<TemplateName>`资源组中，这将产生较小的存储成本。 如果不想保留这些，请在映像生成后删除**映像模板**。
 
映像生成器使用 D1v2 VM 大小以及 VM 所需的存储和网络创建 VM。 这些资源将持续在生成过程的持续时间内，并在映像生成器完成创建映像后删除。 
 
Azure 映像生成器将映像分发到所选区域，这可能会产生网络出口费用。
 
## <a name="next-steps"></a>后续步骤 
 
要尝试 Azure 映像生成器，请参阅有关构建[Linux](../articles/virtual-machines/linux/image-builder.md)或[Windows](../articles/virtual-machines/windows/image-builder.md)映像的文章。
 
 
