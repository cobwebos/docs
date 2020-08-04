---
author: cynthn
ms.author: cynthn
ms.date: 08/03/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 8ad191ca0d31abf317bab521dfbbc7c2567c3450
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87545170"
---
借助标准化虚拟机 (VM) 映像，组织可迁移到云，并确保在部署中保持一致。 映像通常包括预定义的安全性和配置设置，以及必需的软件。 设置自己的映像管道需要时间、基础结构和设置，但使用 Azure VM 映像生成器，只需提供一个描述映像的简单配置，将其提交给该服务，即可构建映像并进行分发。
 
通过 Azure VM 映像生成器（Azure 映像生成器），可从基于 Windows 或 Linux 的 Azure 市场映像、现有自定义映像或 Red Hat Enterprise Linux (RHEL) ISO 开始，并开始添加自己的自定义项。 因为映像生成器基于 [HashiCorp Packer](https://packer.io/) 构建，因此你还可以导入现有 Packer shell 配置程序脚本。 在 [Azure 共享映像库](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)中，还可以指定要在其中托管映像的位置（作为托管映像或 VHD）。

> [!IMPORTANT]
> Azure 映像生成器目前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="preview-features"></a>预览功能

对于预览版，支持以下功能：

- 创建黄金基线映像，该映像包括最低安全性和企业配置，使部门可以根据需要进一步对其进行自定义。
- 修补现有映像，使用映像生成器可以持续修补现有的自定义映像。
- 将映像生成器连接到现有的虚拟网络，以便连接到现有的配置服务器（DSC、Chef、Puppet 等）、文件共享或任何其他可路由的服务器/服务。
- 与 Azure 共享映像库集成，以在全球范围内分发、更改和缩放映像，并提供映像管理系统。
- 与现有映像生成管道集成，只需从管道调用映像生成器，或者使用简单的预览映像生成器 Azure DevOps 任务即可。
- 将现有的映像自定义管道迁移到 Azure。 使用现有的脚本、命令和进程来自定义映像。
- 创建 VHD 格式的映像以支持 Azure Stack。
 

## <a name="regions"></a>区域
Azure 映像生成器服务将在这些区域提供预览版。 可以在这些区域外分发映像。
- 美国东部
- 美国东部 2
- 美国中西部
- 美国西部
- 美国西部 2
- 北欧
- 西欧

## <a name="os-support"></a>OS 支持
AIB 将支持 Azure 市场基础 OS 映像：
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6、7.7
- CentOS 7.6、7.7
- SLES 12 SP4
- SLES 15、SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise 多会话/Professional
- Windows 2016
- Windows 2019

不再提供 RHEL ISO 支持。
## <a name="how-it-works"></a>工作原理


![Azure 映像生成器的概念图](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure 映像生成器是一种完全托管的 Azure 服务，可通过 Azure 资源提供程序进行访问。 Azure 映像生成器进程有三个主要部分：源、自定义和分发，它们显示在模板中。 下图显示了这些组件及其部分属性。 
 


**映像生成器进程** 

![Azure 映像生成器进程的概念图](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 将映像模板创建为 .json 文件。 此 .json 文件包含有关映像源、自定义和分发的信息。 [Azure 映像生成器 GitHub 存储库](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)中提供了多个示例。
1. 将其提交给服务，这将在指定的资源组中创建一个映像模板项目。 在后台，映像生成器将根据需要下载源映像或 ISO 以及脚本。 它们存储在在订阅中自动创建的单独资源组中，格式为： IT_ \<DestinationResourceGroup> _ \<TemplateName> 。 
1. 创建映像模板后，就可以生成映像。 在背景图像生成器中，使用模板和源文件在 IT_ \<DestinationResourceGroup> _ 资源组中创建 VM （默认大小： Standard_D1_v2）、网络、公共 IP、NSG 和存储 \<TemplateName> 。
1. 在创建映像的过程中，映像生成器会根据模板分发映像，然后删除 \<DestinationResourceGroup> 为该进程创建的 IT_ _ 资源组中的其他资源 \<TemplateName> 。


## <a name="permissions"></a>权限
注册 (AIB) 时，这会授予 AIB 服务创建、管理和删除暂存资源组 (IT_*) 以及向其添加资源的权限，映像生成需要这些权限。 这是通过在成功注册期间在订阅中提供的 AIB 服务主体名称 (SPN) 来实现的。

若要允许 Azure VM 映像生成器将映像分发到托管映像或共享映像库，则需要创建一个拥有读取和写入映像权限的 Azure 用户分配的标识。 如果要访问 Azure 存储，则需要读取专用容器的权限。

首先，你必须遵循[创建 Azure 用户分配的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli)文档，了解如何创建标识。

具有向其授予权限的标识后，若要执行此操作，则可以使用 Azure 自定义角色定义，然后分配用户分配的托管标识以使用自定义角色定义。

[此处](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)对权限进行了详细说明，并且示例演示了如何实现权限。

> [!Note]
> 以前使用 AIB 时，可以使用 AIB SPN，并向映像资源组授予 SPN 权限。 我们将不再使用此模型，以允许使用将来的功能。 从 2020 年 5 月 26 日开始，映像生成器将不接受没有用户分配的标识的模板，需要使用[用户标识](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#identity)将现有模板重新提交给服务。 此处的示例已经说明了如何创建用户分配的标识并将其添加到模板。 有关详细信息，请查看此[文档](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information)了解此更改和发布更新。

## <a name="costs"></a>成本
通过 Azure 映像生成器创建、构建和存储映像时，会产生一些计算、网络和存储成本。 这些成本与手动创建自定义映像产生的成本类似。 对于资源，将按 Azure 费率向你收费。 

在映像创建过程中，会下载文件并将其存储在 `IT_<DestinationResourceGroup>_<TemplateName>` 资源组中，这会产生较低的存储成本。 如果不想保留这些文件，请在映像生成后删除“映像模板”。
 
映像生成器创建 D1v2 VM 大小的 VM、存储和 VM 所需的网络。 这些资源将在生成过程中持续存在，并将在映像生成器创建完映像后删除。 
 
Azure 映像生成器会将映像分发到所选区域，这可能会产生网络流出量费用。

## <a name="hyper-v-generation"></a>Hyper-v 生成
映像生成器当前支持 Hyper-v 第1代映像和 Vm。
 
## <a name="next-steps"></a>后续步骤 
 
若要试用 Azure 映像生成器，请参阅构建 [Linux](../articles/virtual-machines/linux/image-builder.md) 或 [Windows](../articles/virtual-machines/windows/image-builder.md) 映像的文章。
 
