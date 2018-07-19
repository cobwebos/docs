---
title: Azure Batch 渲染 - 云规模的渲染 | Microsoft Docs
description: 直接从 Maya 渲染 Azure 虚拟机上的作业，按使用付费。
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: danlep
ms.openlocfilehash: f07b3ce85641f34462c97d16bbed8cf9e2e50652
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114535"
---
# <a name="get-started-with-batch-rendering"></a>Batch 渲染入门 

Azure Batch 渲染提供云规模的渲染功能，按使用付费。 Batch 渲染处理作业计划和队列、管理失败和重试，以及针对渲染作业自动缩放。 Batch 渲染支持 [Autodesk Maya](https://www.autodesk.com/products/maya/overview)、[3ds Max](https://www.autodesk.com/products/3ds-max/overview)、[Arnold](https://www.autodesk.com/products/arnold/overview) 和 [V-Ray](https://www.chaosgroup.com/vray/maya) 等渲染应用。 使用用于 Maya 2017 的 Batch 插件，可以直接从桌面轻松启动 Azure 上的渲染作业。

有了 Maya 和 3ds Max，即可使用 [BatchLabs](https://github.com/Azure/BatchLabs) 桌面应用程序或 [Batch 模板 CLI](batch-cli-templates.md) 运行作业。 使用 Azure Batch CLI，可在不编写代码的情况下运行 Batch 作业。 可以改用模板文件来创建 Batch 池、作业和任务。 有关详细信息，请参阅[使用 Azure Batch CLI 模板和文件传输](batch-cli-templates.md)。


## <a name="supported-applications"></a>支持的应用程序

Batch 渲染目前支持以下应用程序：

在 CentOS 7 渲染节点上：
- Autodesk Maya I/O 2017 更新 5 (cut 201708032230)
- Autodesk Maya I/O 2018 更新 2 (cut 201711281015)
- Autodesk Arnold for Maya 2017（Arnold 版本 5.0.1.1）MtoA-2.0.1.1-2017
- Autodesk Arnold for Maya 2018（Arnold 版本 5.0.1.4）MtoA-2.1.0.3-2018
- Chaos Group V-Ray for Maya 2017（版本 3.60.04） 
- Chaos Group V-Ray for Maya 2018（版本 3.60.04） 
- Blender (2.68)

在 Windows Server 2016 渲染节点上：
- Autodesk Maya I/O 2017 更新 5（版本 17.4.5459） 
- Autodesk Maya I/O 2018 更新 2（版本 18.2.0.6476） 
- Autodesk 3ds Max I/O 2018 更新 4（版本 20.4.0.4254） 
- Autodesk Arnold for Maya（Arnold 版本 5.0.1.1）MtoA-2.0.1.1-2017
- Autodesk Arnold for Maya（Arnold 版本 5.0.1.4）MtoA-2.0.2.3-2018
- Autodesk Arnold for 3ds Max（Arnold 版本 5.0.2.4）（版本 1.2.926） 
- Chaos Group V-Ray for Maya（版本 3.52.03） 
- Chaos Group V-Ray for 3ds Max（版本 3.60.02）
- Blender (2.79)


## <a name="prerequisites"></a>先决条件

若要使用 Batch 渲染，需要：

- [Azure 帐户](https://azure.microsoft.com/free/)。
- **Azure Batch 帐户**。 有关在 Azure 门户中创建 Batch 帐户的指南，请参阅[使用 Azure 门户创建 Batch 帐户](batch-account-create-portal.md)。
- **Azure 存储帐户**。 用于渲染作业的资产通常存储在 Azure 存储中。 可以在设置 Batch 帐户时自动创建存储帐户。 也可以使用现有的存储帐户。 有关 Batch 中的存储帐户选项，请参阅 [Batch 功能概述](batch-api-basics.md#azure-storage-account)。
- **环境变量**。 如果你的解决方案修改了环境变量，请确保在调用上述任何经许可的应用程序时 `AZ_BATCH_ACCOUNT_URL` 和 `AZ_BATCH_SOFTWARE_ENTITLEMENT_TOKEN` 的值保持不变并存在。 否则，可能会遇到软件激活问题。
- **BatchLabs**（可选）。 [BatchLabs](https://azure.github.io/BatchLabs) 是一个功能丰富的免费独立客户端工具，可帮助创建、调试和监视 Azure Batch 应用程序。 虽然不是使用渲染服务所必需的，但它是一个有用的选项，可以开发和调试 Batch 解决方案。

若要使用用于 Maya 的 Batch 插件，你需要：

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview)。
- 支持的渲染器，例如 Arnold for Maya 或 V-Ray for Maya。

## <a name="basic-batch-concepts"></a>基本 Batch 概念

开始使用 Batch 渲染之前，可以熟悉一下 Batch 概念，包括计算节点、池和作业。 若要详细了解 Azure Batch 的一般性知识，请参阅[使用 Batch 运行固有并行的工作负荷](batch-technical-overview.md)。

### <a name="pools"></a>池

Batch 是一项平台服务，用于在计算节点池中运行计算密集型作业，例如渲染。 池中的每个计算节点都是一台运行 Linux 或 Windows 的 Azure 虚拟机 (VM)。 

有关 Batch 池和计算节点的详细信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)中的[池](batch-api-basics.md#pool)和[计算节点](batch-api-basics.md#compute-node)部分。

### <a name="jobs"></a>作业

Batch 作业是在池中计算节点上运行的任务的集合。 当提交渲染作业时，Batch 会将该作业分解为多个任务，然后分发这些任务以在池中的计算节点上运行。

可以使用 [Azure 门户](https://ms.portal.azure.com/)来监视作业和诊断失败的任务，只需下载应用程序日志以及使用 RDP 或 SSH 以远程方式连接到各个 VM 即可。 也可使用 [BatchLabs 工具](https://azure.github.io/BatchLabs)进行管理、监视和调试。

有关 Batch 作业的详细信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)中的[作业](batch-api-basics.md#job)部分。

## <a name="options-for-provisioning-required-applications"></a>用于预配所需应用程序的选项

可能需要使用多个应用程序来渲染一个作业，例如，根据需要组合使用 Maya 和 Arnold 或者 3ds Max 和 V-Ray，以及其他第三方插件。 另外，某些客户可能需要这些应用程序的特定版本。 因此，可以使用多种方法来预配所需的应用程序和软件：

### <a name="pre-configured-vm-images"></a>预配置的 VM 映像

Azure 提供 Windows 和 Linux 映像，每个都预先安装了单一版本的 Maya、3ds Max、Arnold 和 V-Ray，可以马上使用。 创建池时，可以在 [Azure 门户](https://portal.azure.com)、Maya 插件或 [BatchLabs](https://azure.github.io/BatchLabs) 中选择这些映像。

可以在 Azure 门户和 BatchLabs 中安装某个已预装了应用程序的 VM 映像，如下所示：在 Batch 帐户的“池”部分选择“新建”，然后在“添加池”中从“映像类型”下拉列表选择“图形和渲染(Linux/Windows)”：

![选择 Batch 帐户的映像类型](./media/batch-rendering-service/add-pool.png)

向下滚动，在“图形和渲染许可”下，单击“选择软件和定价”。 选择一个或多个软件许可证：

![为池选择图形和渲染许可证](./media/batch-rendering-service/graphics-licensing.png)

提供的特定许可证版本与上面的“支持的应用程序”部分中的版本匹配。

### <a name="custom-images"></a>自定义映像

可以使用 Azure Batch 提供自己的自定义映像。 可以使用此选项为 VM 配置所需的具体应用程序和版本。 有关详细信息，请参阅 [Use a custom image to create a pool of virtual machines](https://docs.microsoft.com/azure/batch/batch-custom-images)（使用自定义映像创建虚拟机池）。 请注意，Autodesk 和 Chaos Group 已分别对 Arnold 和 V-Ray 进行了修改，允许向我们自己的许可服务进行验证。 需确保所拥有的这些应用程序的版本提供此支持，否则，按使用付费许可将不起作用。 此许可证验证对于 Maya 或 3ds Max 来说不是必需的，因为当前的已发布版本在无外设（批处理/命令行模式）运行时不需要许可证服务器。 如果不确定如何使用此选项，请联系 Azure 支持部门。

## <a name="options-for-submitting-a-render-job"></a>用于提交渲染作业的选项

可以通过多个选项提交渲染作业，具体取决于所使用的 3D 应用程序：

### <a name="maya"></a>Maya

可以通过 Maya 来使用：

- [用于 Maya 的 Batch 插件](https://docs.microsoft.com/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- [BatchLabs](https://azure.github.io/BatchLabs) 桌面应用程序
- [Batch 模板 CLI](batch-cli-templates.md)

### <a name="3ds-max"></a>3ds Max

有了 3ds Max，即可使用：

- [BatchLabs](https://azure.github.io/BatchLabs) 桌面应用程序（请参阅 [BatchLabs-data](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax)，了解如何使用 3ds Max BatchLabs 模板）
- [Batch 模板 CLI](batch-cli-templates.md)

有了 3ds Max Batch 实验室模板，即可使用 Batch 渲染来渲染 VRay 和 Arnold 场景。 适用于 VRay 和 Arnold 的模板有两个变体，一个用于标准场景，另一个用于较复杂的场景，需要资产和纹理的 3ds Max 路径文件（.mxp 文件）。 有关 3ds Max Batch 实验室模板的详细信息，请参阅 GitHub 上的 [BatchLabs-data](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) 存储库。

另外，可以使用 [Batch Python SDK](/python/api/overview/azure/batch) 将渲染与现有管道集成。


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>使用用于 Maya 的 Batch 插件提交渲染作业

使用用于 Maya 的 Batch 插件时，可以直接从 Maya 将作业提交到 Batch 渲染。 下述各部分介绍如何从插件配置作业，然后进行提交。 

### <a name="load-the-batch-plug-in-for-maya"></a>加载用于 Maya 的 Batch 插件

[GitHub](https://github.com/Azure/azure-batch-maya/releases) 上提供 Batch 插件。 将存档解压缩到所选目录： 可以直接从 *azure_batch_maya* 目录加载插件。

若要在 Maya 中加载插件，请执行以下操作：

1. 运行 Maya。
2. 打开“Windows” > “设置/首选项” > “插件管理器”。
3. 单击“浏览”。
4. 导航到 *azure_batch_maya/plug-in/AzureBatch.py* 并将其选中。

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>对 Batch 和存储帐户访问进行身份验证

若要使用该插件，需通过 Azure Batch 和 Azure 存储帐户密钥进行身份验证。 若要检索帐户密钥，请执行以下操作：

1. 在 Maya 中显示该插件，然后选择“配置”选项卡。
2. 导航到 [Azure 门户](https://portal.azure.com)。
3. 从左侧菜单中选择“Batch 帐户”。 必要时单击“更多服务”，然后在“Batch”上进行筛选。
4. 在列表中找到所需 Batch 帐户。
5. 选择“密钥”菜单项，以便显示帐户名称、帐户 URL 和访问密钥：
    - 将 Batch 帐户 URL 粘贴到 Batch 插件的“服务”字段中。
    - 将帐户名称粘贴到“Batch 帐户”字段中。
    - 将主帐户密钥粘贴到“Batch 密钥”字段中。
7. 从左侧菜单中选择“存储帐户”。 必要时单击“更多服务”，然后在“存储”上进行筛选。
8. 在列表中找到所需存储帐户。
9. 选择“访问密钥”菜单项，以便显示存储帐户名称和密钥。
    - 将存储帐户名称粘贴到 Batch 插件的“存储帐户”字段中。
    - 将主帐户密钥粘贴到“存储密钥”字段中。
10. 单击“身份验证”，确保插件可以访问这两个帐户。

成功进行身份验证后，插件会将状态字段设置为“已验证”： 

![对 Batch 和存储帐户进行身份验证](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>为渲染作业配置一个池

验证 Batch 和存储帐户以后，请为渲染作业设置一个池。 有了插件，就不需要在不同的会话之间进行选择。 设置首选配置以后，就不需要对其进行修改，除非其已变化。

以下部分演示在“提交”选项卡上提供的可用选项：

#### <a name="specify-a-new-or-existing-pool"></a>指定新池或现有池

若要指定可在其中运行渲染作业的池，请选择“提交”选项卡。该选项卡提供用于创建池或选择现有池的选项：

- 可以“自动为此作业预配池”（默认选项）。 选择此选项时，Batch 会创建专用于当前作业的池，在渲染作业完成后再自动删除该池。 如果需要完成单个渲染作业，则此选项最适合。
- 可以“重用现有的永久池”。 如果有空闲的现有池，则可指定该池来运行渲染作业，只需从下拉列表中将其选中即可。 重用现有的永久池可以节省预配该池所需的时间。  
- 可以“创建新的永久池”。 选择此选项可以创建用于运行此作业的新池。 此选项不会在作业完成后删除池，因此可以将该池重用于将来的作业。 如果不断地需要运行渲染作业，请选择此选项。 在后续作业中，可以选择“重用现有的永久池”，以便使用为第一个作业创建的永久池。

![指定池、OS 映像、VM 大小和许可证](./media/batch-rendering-service/submit.png)

若要详细了解 Azure VM 的收费情况，请参阅 [Linux 定价常见问题解答](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq)和 [Windows 定价常见问题解答](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq)。

#### <a name="specify-the-os-image-to-provision"></a>指定用于预配的 OS 映像

可以在“Env”（环境）选项卡上指定用于预配池中计算节点的 OS 映像的类型。Batch 目前支持下述用于渲染作业的映像选项：

|操作系统  |映像  |
|---------|---------|
|Linux     |Batch CentOS |
|Windows     |Batch Windows |

#### <a name="choose-a-vm-size"></a>选择 VM 大小

可以在“Env”选项卡上指定 VM 大小。有关可用 VM 大小的详细信息，请参阅 [Azure 中 Linux VM 的大小](../virtual-machines/linux/sizes.md)和 [Azure 中 Windows VM 的大小](../virtual-machines/windows/sizes.md)。 

![在“Env”选项卡上指定 VM OS 映像和大小](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>指定许可选项

可以在“Env”选项卡上指定想要使用的许可证。选项包括：

- **Maya**：默认启用。
- **Arnold**：启用的前提是在 Maya 中检测到 Arnold 为活动的渲染引擎。

 若要使用自己的许可证来渲染，则可向表添加适当的环境变量，以便配置许可证终结点。 如果这样做，请确保取消选中默认的许可渲染。

> [!IMPORTANT]
> 当 VM 在池中运行时，会收取许可证使用费用，即使 VM 目前并未用于渲染。 若要避免额外收费，请导航到“池”选项卡，将池大小重设为 0 个节点，等到准备运行下一个渲染作业时再重新调整。 
>
>

#### <a name="manage-persistent-pools"></a>管理永久池

可以在“池”选项卡上管理现有的永久池。从列表中选择一个池即可显示该池的当前状态。

在“池”选项卡上，还可以删除池以及重设池中的 VM 数。 可以在不同工作负荷之间将池大小重设为 0 个节点，避免产生额外的费用。

![对池进行查看、重设大小和删除操作](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>配置要提交的渲染作业

指定要运行渲染作业的池的参数以后，请配置作业本身。 

#### <a name="specify-scene-parameters"></a>指定场景参数

Batch 插件会检测当前正在 Maya 中使用的渲染引擎，并在“提交”选项卡上显示相应的渲染设置。这些设置包括开始帧、结束帧、输出前缀和帧步骤。 可以在插件中指定不同的设置，以便替代场景文件渲染设置。 对插件设置所做的更改无法持久回退成场景文件渲染设置，因此，可以按作业逐个进行更改，这样就不需要重新上传场景文件。

如果在 Maya 中选择的渲染引擎不受支持，插件会发出警告。

如果在插件处于打开状态时加载新的场景，请单击“刷新”按钮，确保设置已更新。

#### <a name="resolve-asset-paths"></a>解析资产路径

加载插件时，插件会扫描场景文件，看其中是否有任何外部文件引用。 这些引用显示在“资产”选项卡中。如果引用的路径无法解析，插件会尝试在多个默认位置查找该文件，其中包括：

- 场景文件的位置 
- 当前项目的 _sourceimages_ 目录
- 当前工作目录。 

如果该资产仍找不到，则会在列出时带有警告图标：

![缺失的资产在显示时带有警告图标](./media/batch-rendering-service/missing_assets.png)

如果知道未解析的文件引用的位置，则可单击警告图标，系统就会提示你添加搜索路径。 然后，插件就会使用该搜索路径尝试解析任何缺失的资产。 可以添加任意数目的其他搜索路径。

引用得到解析以后，就会在列出时带有绿灯图标：

![解析的资产显示绿灯图标](./media/batch-rendering-service/found_assets.png)

如果场景需要插件尚未检测到的其他文件，则可添加其他文件或目录。 使用“添加文件”和“添加目录”按钮。 如果在插件处于打开状态时加载新的场景，请确保单击“刷新”以更新场景的引用。

#### <a name="upload-assets-to-an-asset-project"></a>将资产上传到资产项目

提交渲染作业时，在“资产”选项卡中显示的已引用文件会自动作为资产项目上传到 Azure 存储。 也可使用“资产”选项卡上的“上传”按钮，在独立于渲染作业的情况下上传资产文件。资产项目在“项目”字段中指定名称，默认情况下是根据当前的 Maya 项目命名的。 上传资产文件时，会保留本地文件结构。 

上传以后，资产可供任意数目的渲染作业引用。 所有上传的资产均可供引用资产项目的任何作业使用，不管这些资产是否包括在场景中。 若要更改下一作业所引用的资产项目，请更改“资产”选项卡的“项目”字段中的名称。如果不想上传某些引用的文件，请使用列表旁边的绿色按钮将其取消选中。

#### <a name="submit-and-monitor-the-render-job"></a>提交和监视渲染作业

在插件中配置渲染作业以后，单击“提交”选项卡上的“提交作业”按钮即可将作业提交到 Batch：

![提交渲染作业](./media/batch-rendering-service/submit_job.png)

可以从插件上的“作业”选项卡监视正在进行的作业。 从列表中选择一个作业即可显示该作业的当前状态。 也可使用该选项卡来取消和删除作业，以及下载输出和渲染日志。 

若要下载输出，请修改“输出”字段，以便设置所需的目标目录。 单击齿轮图标即可启动一个后台进程，用于监视作业并在作业进行过程中下载输出： 

![查看作业状态和下载输出](./media/batch-rendering-service/jobs.png)

可以关闭 Maya 而不中断下载过程。

## <a name="next-steps"></a>后续步骤

若要详细了解 Batch，请参阅[使用 Batch 运行固有并行的工作负荷](batch-technical-overview.md)。
