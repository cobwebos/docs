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
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 02/19/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f5b884ddda292b1c523a5364d34753ccb3a5bbdf
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194429"
---
# <a name="validate-oem-packages"></a>验证 OEM 程序包

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

当对已完成的解决方案验证的固件或驱动程序进行了更改时，可以测试新的 OEM 程序包。 在您的软件包经过测试，它是由 Microsoft 签名。 你的测试必须包含更新后的 OEM 扩展程序包，其中包含已通过 Windows Server 徽标和 PCS 测试的驱动程序和固件。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> 在上载或提交包之前, 查看[创建一个 OEM 包](azure-stack-vaas-create-oem-package.md)预期的包格式和内容。

## <a name="managing-packages-for-validation"></a>管理包进行验证

使用时**包验证**工作流，以验证包中，将需要提供的 URL **Azure 存储 blob**。 此 blob 是测试签名将作为更新过程的一部分安装的 OEM 包。 创建使用在安装期间创建的 Azure 存储帐户的 blob (请参阅[设置为服务资源将验证](azure-stack-vaas-set-up-resources.md))。

### <a name="prerequisite-provision-a-storage-container"></a>先决条件：预配存储容器

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

#### <a name="option-1-generating-a-blob-sas-url"></a>选项 1：生成 blob SAS URL

如果您不想要启用到你的存储容器或 blob 的公共读取访问权限，请使用此选项。

1. 在中[Azure 门户](https://portal.azure.com/)，请转到你的存储帐户，然后导航到包含您的包.zip

2. 选择**生成 SAS**从上下文菜单

3. 选择**读**从**权限**

4. 设置**开始时间**为当前时间，并**结束时间**到 48 小时至少从**开始时间**。 如果你将在其他测试运行使用同一个包，请考虑增大**结束时间**测试的长度。 通过 VaaS 后计划的任何测试**结束时间**将失败，而且新的 SAS 将需要生成。

5. 选择“生成 Blob SAS 令牌和 URL”。

使用**Blob SAS URL**时提供包 blob 到门户的 Url。

#### <a name="option-2-grant-public-read-access"></a>选项 2：授予公共读取访问权限

> [!CAUTION]
> 此选项将打开你个 blob 的匿名的只读访问权限。

1. 授予**的公共读取访问仅限对 blob**到按照部分中的说明包容器[授予对容器和 blob 的匿名用户权限](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)。

> [!NOTE]
> 如果你要提供的包 URL*交互式测试*（例如，每月 AzureStack 更新验证或 OEM 扩展包验证），必须授予**完全公共读取访问**到继续进行测试。

2. 在包容器中，选择要打开属性窗格中的包 blob。

3. 复制**URL**。 提供包 blob Url 复制到门户时，请使用此值。

## <a name="apply-monthly-update"></a>应用每月更新

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

> [!NOTE]
> 在应用每月的更新后，我们建议你运行 Test-azurestack 以验证更新已正确应用并处于正常状态。 如果 Test-azurestack 失败，请向 Microsoft 报告此问题。 不要继续执行测试轮次直到问题得到解决。 有关如何运行测试 Azure Stack 命令的信息可在此[一文](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)。

## <a name="create-a-package-validation-workflow"></a>创建包验证工作流

1. 登录到[VaaS 门户](https://azurestackvalidation.com)。

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 选择**启动**上**包验证**磁贴。

    ![包验证工作流磁贴](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. 输入 Azure 存储 blob URL 测试签名 OEM 包需要来自 Microsoft 的签名。 有关说明，请参阅[VaaS 生成包 blob URL](#generate-package-blob-url-for-vaas)。

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 创建工作流后，无法修改环境的参数。

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    你将重定向到测试摘要页。

## <a name="required-tests"></a>所需的测试

以下测试所需的 OEM 包验证：

- OEM 扩展包验证
- 云模拟引擎

## <a name="run-package-validation-tests"></a>运行包验证测试

1. 在中**包验证测试摘要**页上，将运行的适用于你的方案列出测试的子集。

    验证工作流中**安排**测试使用工作流创建期间指定的工作流级别公用参数 (请参阅[工作流通用参数作为服务AzureStack验证](azure-stack-vaas-parameters.md)). 如果任何测试参数值变得无效，您必须 resupply 它们中所述[修改工作流参数](azure-stack-vaas-monitor-test.md#change-workflow-parameters)。

    > [!NOTE]
    > 计划高于现有实例的一个验证测试，将在门户中创建的新实例来代替旧实例。 旧实例的日志将保留，但不能从门户访问。  
    测试成功，完成后**计划**操作将被禁用。

2. 选择将运行测试的代理。 有关添加信息本地测试执行代理，请参阅[部署本地代理](azure-stack-vaas-local-agent.md)。

3. 进行完整的 OEM 扩展包验证选**计划**从上下文菜单打开的提示的计划的测试实例。

4. 查看测试参数，然后选择**提交**要安排执行 OEM 扩展包验证。

5. 查看 OEM 扩展包验证结果。 成功测试之后，计划云模拟引擎执行。

已成功完成所有测试，请发送 VaaS 解决方案和对包验证的名称[ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)请求包签名。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)
