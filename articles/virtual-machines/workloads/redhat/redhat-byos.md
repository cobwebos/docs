---
title: Red Hat Enterprise Linux 订阅的映像 |Microsoft Docs
description: 了解 Azure 上的 Red Hat Enterprise Linux 的自带订阅映像
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486503"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>在 Azure 中 Red Hat Enterprise Linux 自带订阅映像
Red Hat Enterprise Linux （RHEL）映像可通过即用即付（PAYG）或自带订阅（BYOS）模型在 Azure 中使用。 本文档概述了 Azure 中的 BYOS 映像。

## <a name="important-points-to-consider"></a>需要考虑的要点

- 此程序中提供的 RHEL BYOS 映像与 Azure 库/Marketplace 中的 RHEL PAYG 映像类似。 获取映像的注册过程处于预览阶段。

- 映像遵循在[Azure 上 Red Hat Enterprise Linux 映像](./redhat-images.md)中描述的当前策略

- 标准支持策略适用于通过这些映像创建的 Vm

- 通过 RHEL BYOS 映像预配的 Vm 不会携带 rhel PAYG 映像关联的 RHEL 费用

- 映像是未获授权的，因此你必须使用订阅管理器注册并订阅 Vm 才能直接从 Red Hat 获取更新

- 目前不能在 Linux 映像的 BYOS 和 PAYG 计费模型之间动态切换。 若要切换计费模型，需要从各自的映像重新部署 VM

- 在这些 RHEL BYOS 映像上支持 Azure 磁盘加密（ADE）。 ADE 支持目前以预览版提供。 在配置 ADE 之前，必须使用订阅管理器注册 Red Hat。 注册后，若要配置 ADE，请参阅：为[Linux IaaS Vm 启用 Azure 磁盘加密](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview)

- 尽管映像不会更改（超过标准的更新和修补程序），但注册过程处于预览阶段，将进一步改进流以简化流程

- 不管最终实现如何，都可以完全控制已从这些映像或其快照预配的 Vm

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>访问 RHEL BYOS 映像的要求和条件

1. 熟悉[Red Hat 云访问计划](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)条款，在[Red Hat 的云访问注册页](https://access.redhat.com/cloude/manager/image_imports/new)上注册。

1. 完成[RHEL BYOS 访问请求表单](https://aka.ms/rhel-byos)。 你需要拥有你的 Red Hat 帐号以及你用来通过来访问 RHEL BYOS 映像的 Azure 订阅。

1. 通过 Red Hat 和 Microsoft 进行评审和批准后，将为你的 Azure 订阅启用图像访问。

### <a name="expected-time-for-image-access"></a>图像访问的预计时间

完成 RHEL BYOS 表单并接受条款后，Red Hat 将在三个工作日内验证你对 BYOS 映像的资格，如果有效，你将在一个工作日内获得 BYOS 映像的访问权限。

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>使用 Azure 门户中的 RHEL BYOS 映像

1. 为 RHEL BYOS 映像启用订阅后，可以通过导航到 "**创建资源**" 并**查看全部**，在[Azure 门户](https://portal.azure.com)中找到该映像。

1. 在页面顶部，你会看到你有专用产品/服务。

    ![Marketplace 专用产品/服务](./media/rhel-byos-privateoffers.png)

1. 你可以单击紫色链接或向下滚动到页面底部，查看你的专用产品/服务。

1. UI 中的其余设置与任何其他现有的 Red Hat 映像都没有什么不同。 选择 RHEL 版本，并按照提示进行操作以预配 VM。 此过程还可让你在最后一步接受映像的条款。

>[!NOTE]
>到目前为止，这些步骤不会启用你的 RHEL BYOS 映像以进行编程部署–需要额外的步骤，如下面的 "其他信息" 一节中所述。

本文档的其余部分重点介绍用于预配和接受有关映像的术语的 CLI 方法。 由于最终的结果（预配的 RHEL BYOS VM），UI 和 CLI 是完全可互换的。

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>使用 Azure CLI 中的 RHEL BYOS 映像
以下一组说明将指导你完成使用 Azure CLI 的 RHEL VM 的初始部署过程。 这些说明假定已[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

>[!IMPORTANT]
>请确保在发布者、产品/服务、计划和映像引用中为以下所有命令使用所有小写字母

1. 检查你是否处于所需的订阅中：
    ```azurecli
    az account show -o=json
    ```

1. 为 RHEL BYOS VM 创建资源组：
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 接受映像术语：
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >每*个 Azure 订阅（每个映像 SKU）需要接受一次*这些条款。

1. 可有可无通过以下命令验证 VM 部署：
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. 在不带 `--validate` 参数的情况下运行同一命令来预配 VM：
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. 通过 SSH 连接到 VM，并验证是否有未获授权映像。 为此，请运行 `sudo yum repolist`。 此输出将要求你使用订阅管理器向 Red Hat 注册 VM。

## <a name="additional-information"></a>其他信息
- 如果尝试在未针对此产品/服务启用的订阅上预配 VM，你将收到以下错误，你应与 Microsoft 或 Red Hat 联系以启用你的订阅。
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- 如果从 RHEL BYOS 映像创建快照并在[共享映像库](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)中发布映像，则需要提供与快照原始源匹配的计划信息。 例如，命令可能如下所示（请注意最后一行中的计划参数）：
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- 如果使用自动化从 RHEL BYOS 映像预配 Vm，则需要提供类似于上面所示内容的计划参数。 例如，如果使用的是 Terraform，则应在[计划块](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)中提供计划信息。

## <a name="next-steps"></a>后续步骤
* 了解有关[Azure Red Hat 更新基础结构](./redhat-rhui.md)的详细信息。
* 若要详细了解 Azure 中的 Red Hat 映像，请转到此[文档页面](./redhat-images.md)。
* 可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。