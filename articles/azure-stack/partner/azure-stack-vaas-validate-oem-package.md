---
title: 验证 Azure Stack 验证作为一项服务中的原始设备制造商 (OEM) 包 |Microsoft Docs
description: 了解如何进行验证的验证作为一项服务的原始设备制造商 (OEM) 包。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 8268a6b04d7ddbb35821999142d3a33bdd2bedcc
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261796"
---
# <a name="validate-oem-packages"></a>验证 OEM 程序包

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

当对已完成的解决方案验证的固件或驱动程序进行了更改时，可以测试新的 OEM 程序包。 在您的软件包经过测试，它是由 Microsoft 签名。 你的测试必须包含更新后的 OEM 扩展程序包，其中包含已通过 Windows Server 徽标和 PCS 测试的驱动程序和固件。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> 在上载或提交包之前, 查看[创建一个 OEM 包](azure-stack-vaas-create-oem-package.md)预期的包格式和内容。

## <a name="managing-packages-for-validation"></a>管理包进行验证

使用时**包验证**工作流，以验证包中，将需要提供的 URL **Azure 存储 blob**。 此 blob 是在部署时，解决方案已安装的 OEM 包。 创建使用在安装期间创建的 Azure 存储帐户的 blob (请参阅[设置为服务资源将验证](azure-stack-vaas-set-up-resources.md))。

### <a name="prerequisite-provision-a-storage-container"></a>先决条件： 预配存储容器

包的 blob 在存储帐户中创建容器。 此容器可以用于所有包验证运行。

1. 在中[Azure 门户](https://portal.azure.com)，请转到在中创建的存储帐户[设置为服务资源将验证](azure-stack-vaas-set-up-resources.md)。
2. 在左侧边栏选项卡上**Blob 服务**，选择上**容器**。
3. 选择 **+ 容器**在菜单栏，为容器提供名称，例如， `vaaspackages`。

### <a name="upload-package-to-storage-account"></a>将包上载到存储帐户

1. 准备要验证的包。 如果您的包具有多个文件，压缩到`.zip`文件。
2. 在中[Azure 门户](https://portal.azure.com)，选择包容器并上传上选择包**上传**菜单栏中。
3. 选择的包`.zip`要上传文件。 保留默认值为**Blob 类型**(即**块 Blob**) 和**块大小**。

> [!NOTE]
> 请确保`.zip`内容的根目录处放置`.zip`文件。 包应为任何子文件夹。

### <a name="generate-package-blob-url-for-vaas"></a>为 VaaS 生成包 blob URL

创建时**包验证**VaaS 门户中的工作流，你将需要提供包含包的 Azure 存储 blob 的 URL。

#### <a name="option-1-generating-an-account-sas-url"></a>选项 1： 生成帐户 SAS URL

1. 在中[Azure 门户](https://portal.azure.com/)，请转到你的存储帐户，然后导航到包含您的包.zip

2. 选择**生成 SAS**从上下文菜单

3. 选择**读**从**权限**

4. 设置**开始时间**为当前时间，并**结束时间**到 48 小时至少从**开始时间**。 如果你将在其他测试运行使用同一个包，请考虑增大**结束时间**测试的长度。 通过 VaaS 后计划的任何测试**结束时间**将失败，而且新的 SAS 将需要生成。

5. 选择**生成 blob SAS 令牌和 URL**

使用**Blob SAS URL**启动时的新**包验证**VaaS 门户中的工作流。

#### <a name="option-2-using-public-read-container"></a>选项 2： 使用公共只读的容器

> [!CAUTION]
> 此选项将打开你的容器用于匿名的只读访问。

1. 授予**的公共读取访问仅限对 blob**到按照部分中的说明包容器[授予对容器和 blob 的匿名用户权限](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)。

2. 在包容器中选择在容器中的包 blob 以打开属性窗格。

3. 复制**URL**。 使用此值时开始一个新**包验证**VaaS 门户中的工作流。

## <a name="apply-monthly-update"></a>应用每月更新

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>创建包验证工作流

1. 登录到[VaaS 门户](https://azurestackvalidation.com)。

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 选择**启动**上**包验证**磁贴。

    ![包验证工作流磁贴](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. 给 OEM 包的解决方案安装在部署时输入 Azure 存储 blob URL。 有关说明，请参阅[VaaS 生成包 blob URL](#generate-package-blob-url-for-vaas)。

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 创建工作流后，无法修改环境的参数。

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    你将重定向到测试摘要页。

## <a name="run-package-validation-tests"></a>运行包验证测试

在中**包验证测试摘要**页中，将看到的完成验证所需的测试的列表。 此工作流中的测试运行大约 24 小时。

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

已成功完成所有测试，请发送 VaaS 解决方案和对包验证的名称[ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)请求包签名。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)