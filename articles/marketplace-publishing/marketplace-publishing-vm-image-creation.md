---
title: "为 Azure 应用商店创建虚拟机映像 | Microsoft Docs"
description: "详细说明了如何为 Azure 应用商店创建虚拟机映像，以供其他人购买。"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: 27f49a7c0c2736423d73930a1865f7b542f54da4
ms.openlocfilehash: 63c1591db91a2ffeaced8ac775525291f0fd033f
ms.lasthandoff: 01/06/2017


---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>为 Azure 应用商店创建虚拟机映像指南
本文的**步骤 2** 将引导用户完成虚拟硬盘 (VHD) 的准备工作，然后将其部署到 Azure 应用商店。 VHD 是 SKU 的基础。 此过程各有不同，具体取决于提供的是基于 Linux 还是基于 Windows 的 SKU。 本文对这两种方案都做了介绍。 此过程可与[帐户创建和注册][link-acct-creation] 并行执行。

## <a name="1-define-offers-and-skus"></a>1.定义产品/服务和 SKU
在本部分中，你将了解如何定义产品/服务及其关联的 SKU。

产品/服务是其所有 SKU 的“父级”。 用户可以拥有多个产品/服务。 用户决定如何构造产品/服务。 将产品/服务推送到过渡环境时，它会随其所有的 SKU 一起推送。 请仔细考虑 SKU 标识符，因为它们会显示在 URL 中：

* Azure.com：http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Azure 预览门户：https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

SKU 是 VM 映像的商业名称。 一个 VM 映像包含一个操作系统磁盘以及零个或更多数据磁盘。 它本质上是虚拟机的完整存储配置文件。 每个磁盘都需要一个 VHD。 即使空白数据磁盘也需要创建 VHD。

无论使用何种操作系统，都仅添加 SKU 所需的最少数据磁盘数。 客户无法在部署时删除作为映像一部分的磁盘，但是始终可以在部署过程中或之后添加磁盘（如果需要）。

> [!IMPORTANT]
> **请勿在新映像版本中更改磁盘计数。** 如果必须在映像中重新配置数据磁盘，请定义新 SKU。 发布不同磁盘计数的新映像版本可能会在通过 ARM 模板和其他方案自动扩展和部署时，中断基于新映像版本的新部署。
>
>

### <a name="11-add-an-offer"></a>1.1 添加产品/服务
1. 使用卖方帐户登录[发布门户][link-pubportal]。
2. 在发布门户上选择“虚拟机”选项卡。 在提示的输入字段中，输入产品/服务名称。 产品/服务名称通常是计划在 Azure 应用商店中销售的产品或服务的名称。
3. 选择“创建” 。

### <a name="12-define-a-sku"></a>1.2 定义 SKU
添加了产品/服务后，需要定义并标识 SKU。 客户可拥有多个产品/服务，并且每个产品/服务下面均可有多个 SKU。 将产品/服务推送到过渡环境时，它会随其所有的 SKU 一起推送。

1. **添加 SKU。** SKU 需要在 URL 中使用的标识符。 该标识符在发布配置文件中必须独一无二，不过不会有标识符与其他发布者发生冲突的风险。

   > [!NOTE]
   > 产品/服务和 SKU 标识符显示在应用商店的产品/服务 URL 中。
   >
   >
2. **为 SKU 添加摘要说明。** 摘要说明对客户可见，因此要使它们便于阅读。 在“推送到过渡环境”阶段之前，无需锁定此信息。 到时候可随意编辑。
3. 如果使用基于 Windows 的 SKU，请遵循建议链接来获取已批准的 Windows Server 版本。

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2.创建与 Azure 兼容的 VHD（基于 Linux）
本部分重点介绍有关为 Azure 应用商店创建基于 Linux 的 VM 映像的最佳做法。 有关分步演练，请参阅以下文档：[创建和上载包含 Linux 操作系统的虚拟硬盘](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3.创建与 Azure 兼容的 VHD（基于 Windows）
本部分重点介绍用于为 Azure 应用商店创建基于 Windows Server 的 SKU 的步骤。

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 请确保使用正确的基本 VHD
用于 VM 映像的操作系统 VHD 必须基于 Azure 批准的基本映像（包含 Windows Server 或 SQL Server）。

开始时，请从位于 [Microsoft Azure 门户][link-azure-portal] 的以下映像中创建 VM：

* Windows Server（[2012 R2 Datacenter][link-datactr-2012-r2]、[2012 Datacenter][link-datactr-2012]、[2008 R2 SP1][link-datactr-2008-r2]）
* SQL Server 2014（[Enterprise][link-sql-2014-ent]、[Standard][link-sql-2014-std]、[Web][link-sql-2014-web]）
* SQL Server 2012 SP2（[Enterprise][link-sql-2012-ent]、[Standard][link-sql-2012-std]、[Web][link-sql-2012-web]）

这些链接也可以在发布门户中 SKU 页面下找到。

> [!TIP]
> 如果使用最新 Azure 门户或 PowerShell，则在 2014 年 9 月 8 日及以后发布的 Windows Server 映像已得到批准。
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 创建基于 Windows 的 VM
在 Microsoft Azure 门户中，只需几个简单步骤便可以基于批准的基本映像创建 VM。 下面是过程概述：

1. 在基本映像页面中，选择“创建虚拟机”可定向到新的 [Microsoft Azure 门户][link-azure-portal]。

    ![绘制][img-acom-1]
2. 通过要使用的 Azure 订阅的 Microsoft 帐户和密码登录该门户。
3. 按照提示使用选择的基本映像创建 VM。 需要为 VM 提供主机名（计算机名称）、用户名（以管理员身份注册的用户）和密码。

    ![绘制][img-portal-vm-create]
4. 选择要部署的 VM 大小：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。    如果计划在本地开发 VHD，大小则无关紧要。 请考虑使用一个较小的 VM。

    b.保留“数据库类型”设置，即设置为“共享”。    如果计划在 Azure 中开发映像，请考虑对所选映像使用一个建议的 VM 大小。

    c.    有关定价信息，请参阅门户上显示的“建议定价层”选择器。 它将提供由发布者提供的三个建议大小。 （在此例中，发布者是 Microsoft。）

    ![绘制][img-portal-vm-size]
5. 设置属性：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。    为进行快速部署，可以为“可选配置”和“资源组”下的属性保留默认值。

    b.保留“数据库类型”设置，即设置为“共享”。    在“存储帐户”下，可以选择存储操作系统 VHD 的存储帐户。

    c.    在“资源组”下，可以选择放置 VM 的逻辑组。
6. 选择部署的“位置”：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。    如果计划在本地开发 VHD，位置则无关紧要，因为映像将稍后上传到 Azure。

    b.保留“数据库类型”设置，即设置为“共享”。    如果计划在 Azure 中开发映像，请考虑从开始便使用一个位于美国的 Microsoft Azure 区域。 这将加快 VHD 复制过程，Microsoft 会在你提交映像进行认证时代表你执行该过程。

    ![绘制][img-portal-vm-location]
7. 单击“创建” 。 开始部署 VM。 在几分钟内便会部署成功，可以开始为 SKU 创建映像。

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 在云中开发 VHD
我们强烈建议使用远程桌面协议 (RDP) 在云中开发 VHD。 使用在预配过程中指定的用户名和密码连接到 RDP。

> [!IMPORTANT]
> 如果本地开始 VHD（不推荐），请参阅[本地创建虚拟机映像](marketplace-publishing-vm-image-creation-on-premise.md)。 如果在云中开发，则无需下载 VHD。
>
>

“使用 [Microsoft Azure 门户][link-azure-portal]通过 RDP 进行连接”

1. 选择“浏览 VM” > 。
2. 虚拟机边栏选项卡将打开。 请确保要连接的 VM 正在运行，然后从部署的 VM 列表中选择它。
3. 一个介绍所选 VM 的边栏选项卡将打开。 在顶部单击“连接”。
4. 将提示输入在预配时指定的用户名和密码。

**使用 PowerShell 通过 RDP 进行连接**

要将远程桌面文件下载到本地计算机，请使用 [Get-AzureRemoteDesktopFile cmdlet][link-technet-2]。 为了使用此 cmdlet，需要知道服务名称和 VM 名称。 如果从 [Microsoft Azure 门户][link-azure-portal]创建了 VM，则可以在“VM 属性”下找到此信息：

1. 在 Microsoft Azure 门户中，选择“浏览 VM” > 。
2. 虚拟机边栏选项卡将打开。 选择部署的 VM。
3. 一个介绍所选 VM 的边栏选项卡将打开。
4. 单击“属性”。
5. 域名的第一个部分是服务名。 主机名是 VM 名。

    ![绘制][img-portal-vm-rdp]
6. 用于将创建的 VM 的 RDP 文件下载到管理员本地桌面的 cmdlet 如下所示。

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

有关 RDP 的详细信息，可在 MSDN 的[通过 RDP 或 SSH 连接 Azure VM](http://msdn.microsoft.com/library/azure/dn535788.aspx) 一文中找到。

**配置 VM 并创建 SKU**

下载操作系统 VHD 之后，可使用 Hyper­V 并配置 VM 以开始创建 SKU。 可以在以下 TechNet 链接处找到详细步骤：[安装 Hyper-V 和配置 VM](http://technet.microsoft.com/library/hh846766.aspx)。

### <a name="34-choose-the-correct-vhd-size"></a>3.4 选择正确的 VHD 大小
VM 映像中的 Windows 操作系统 VHD 应创建为 128 GB 的固定格式的 VHD。  

如果物理大小小于 128GB，则 VHD 应是稀疏的磁盘。 提供的基本 Windows 和 SQL Server 映像已满足这些要求；请不要更改获取的 VHD 的格式或大小。  

数据磁盘可以大至 1TB。 决定磁盘大小时，请记住，客户无法在部署时对映像中的 VHD 调整大小。 数据磁盘 VHD 应创建为固定格式 VHD。 它们还应是稀疏的。 数据磁盘可以为空或包含数据。

### <a name="35-install-the-latest-windows-patches"></a>3.5 安装最新 Windows 修补程序
基本映像包含截至发布日期的最新修补程序。 发布创建的操作系统 VHD 之前，请确保 Windows 更新已运行，并且所有最新的“关键”和“重要”安全更新都已安装。

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 根据需要执行其他配置和计划任务
如果需要其他配置，请考虑使用在启动时运行的计划任务，以便在部署 VM 之后对它进行任何最终更改：

* 让任务在成功执行之后删除自己是最佳做法。
* 不应有任何配置依赖于驱动器 C 或 D 之外的驱动器，因为这两个驱动器是仅有的始终保证存在的驱动器。 驱动器 C 是操作系统磁盘，而驱动器 D 是临时的本地磁盘。

### <a name="37-generalize-the-image"></a>3.7 一般化映像
Azure 应用商店中的所有映像必须可采用一般形式重复使用。 换句话说，必须对操作系统 VHD 进行一般化：

* 对于 Windows，映像应“执行 sysprep 操作”，不应进行任何不支持 **sysprep** 命令的配置。
* 可从 %windir%\System32\Sysprep 目录中运行以下命令。

        sysprep.exe /generalize /oobe /shutdown

  以下 MSDN 文章的步骤提供了有关如何对操作系统执行 sysprep 操作的指南：[创建 Windows Server VHD 并上传到 Azure](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="4-deploy-a-vm-from-your-vhds"></a>4.从 VHD 部署 VM
将 VHD（一般化操作系统 VHD 以及零个或更多数据磁盘 VHD）上传到 Azure 存储器帐户之后，可将它们注册为用户 VM 映像。 然后可以测试该映像。 请注意，因为操作系统 VHD 已经一般化，所以无法通过提供 VHD URL 来直接部署 VM。

若要了解关于 VM 映像的详细信息，请查看以下博客文章：

* [VM 映像](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [VM 映像 PowerShell 方法](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [关于 Azure 中的 VM 映像](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>设置所需工具、PowerShell 和 Azure CLI
* [如何设置 PowerShell](/powershell/azureps-cmdlets-docs)
* [如何设置 Azure CLI](../xplat-cli-install.md)

### <a name="41-create-a-user-vm-image"></a>4.1 创建用户 VM 映像
#### <a name="capture-vm"></a>捕获 VM
请阅读下面给出的链接，获取有关使用 API/PowerShell/Azure CLI 捕获 VM 的指南。

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>一般化映像
请阅读下面给出的链接，获取有关使用 API/PowerShell/Azure CLI 捕获 VM 的指南。

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 从用户 VM 映像部署 VM
要从用户 VM 映像部署 VM，可以使用最新 [Azure 门户](https://manage.windowsazure.com)或 PowerShell。

**从最新 Azure 门户部署 VM**

1. 依次转到“新建” > “计算” > “虚拟机” > “从库”。

    ![绘制][img-manage-vm-new]
2. 转到“我的映像”，然后选择要用于部署 VM 的 VM 映像：

   1. 请密切注意选择的映像，因为“我的映像”视图会同时列出操作系统映像和 VM 映像。
   2. 查看磁盘数可以帮助确定所部署的映像类型，因为大多数 VM 映像都具有多个磁盘。 但是，仍可能有 VM 映像仅包含单个操作系统磁盘，从而会将“磁盘数”设置为 1。

      ![绘制][img-manage-vm-select]
3. 按照 VM 创建向导操作，指定 VM 名、VM 大小、位置、用户名和密码。

**从 PowerShell 部署 VM**

要从刚创建的一般化 VM 映像部署大型 VM，可以使用以下 cmdlet。

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> 有关其他帮助，请参考 [创建 VHD 时遇到的常见问题疑难解答]。
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5.获取 VM 映像的认证
为 Azure 应用商店准备 VM 映像的下一步是进行认证。

此过程包括运行一个专门认证工具、将认证结果上传到 VHD 所处的 Azure 容器、添加产品/服务、定义 SKU 以及提交 VM 映像进行认证。

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 下载并运行 Azure 认证的认证测试工具
认证工具在正在运行的 VM（从用户 VM 映像中预配）上运行，确保 VM 映像与 Microsoft Azure 兼容。 它会验证指南和是否满足有关准备 VHD 的要求。 该工具的输出时兼容性报告，此报告在请求认证时应上传到发布门户。

认证工具可以与 Windows 和 Linux VM 一起使用。 它通过 PowerShell 连接到基于 Windows 的 VM，通过 SSH.Net 连接到 Linux VM：

1. 首先，在 [Microsoft 下载站点][link-msft-download]下载认证工具。
2. 打开认证工具，然后单击“启动新测试”按钮。
3. 在“测试信息”屏幕中，为测试运行输入名称。
4. 选择 VM 是处于 Linux 还是 Windows 上。 根据所选内容，选择后续选项。

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**将认证工具连接到 Linux VM 映像**
1. 选择 SSH 身份验证模式：密码或密钥文件。
2. 如果使用基于密码的身份验证，请输入域名系统 (DNS) 名称、用户名和密码。
3. 如果使用密钥文件身份验证，请输入 DNS 名称、用户名和私钥位置。

   ![Linux VM 映像的密码身份验证][img-cert-vm-pswd-lnx]

   ![Linux VM 映像的密钥文件身份验证][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**将认证工具连接到基于 Windows 的 VM 映像**
1. 输入完全限定的VM DNS 名称（例如 MyVMName.Cloudapp.net）。
2. 输入用户名和密码。

   ![Windows VM 映像的密码身份验证][img-cert-vm-pswd-win]

为基于 Linux 或 Windows 的 VM 映像选择了正确的选项之后，选择“测试连接”以确保 SSH.Net 或 PowerShell 具有可用于测试的有效连接。 建立连接之后，按“下一步”以启动测试。

测试完成时，将收到每个测试元素的结果（通过/失败/警告）。

![Linux VM 映像的测试用例][img-cert-vm-test-lnx]

![Windows VM 映像的测试用例][img-cert-vm-test-win]

如果任何测试失败，将不会认证映像。 如果发生这种情况，请查看要求并作出任何必要更改。

自动测试后，将通过调查表屏幕要求用户提供有关 VM 映像的其他输入。  填写这些问题，然后选择“下一步”。

![认证工具调查表][img-cert-vm-questionnaire]

![认证工具调查表][img-cert-vm-questionnaire-2]

填写调查表后，可为 Linux VM 映像提供诸如 SSH 访问信息的额外信息，并解释评估失败的原因。 除下载调查表答案外，还可下载已执行的测试用例的测试结果和日志文件。 将结果保存在与 VHD 相同的容器中。

![保存认证测试结果][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 为 VM 映像获取共享访问签名 URI
在发布过程中，指定导向为 SKU 创建的所有 VHD 的统一资源标识符 (URI)。 Microsoft 需要在认证过程中访问这些 VHD。 因此需要为每个 VHD 创建共享访问签名 URI。 这是应在发布门户的“映像”选项卡中输入的 URI。

创建的共享访问签名 URI 应符合以下要求：

* 为 VHD 生成共享访问签名 URI 时，“列出”和“读取”权限已足够使用。 请不要提供“写入”或“删除”访问权限。
* 访问的持续时间应至少是三 (3) 周，从创建共享访问签名 URI 时算起。
* 为了保证 UTC 时间，请选择当前日期的前一天。 例如，如果当前时间是 2014 年 10 月 6 日，则选择 10/5/2014。

可以通过多种方式生成 SAS URL，以便向 Azure 应用商店共享你的 VHD。
以下是 3 个建议的工具：

1.  Azure 存储资源管理器
2.  Microsoft 存储资源管理器
3.  Azure CLI

**Azure 存储资源管理器（向 Windows 用户推荐）**

下面是使用 Azure 存储资源管理器生成 SAS URL 的步骤

1. 从 CodePlex 下载 [Azure 存储资源管理器 6 预览版 3](https://azurestorageexplorer.codeplex.com/)。 转到 [Azure 存储资源管理器 6 预览版](https://azurestorageexplorer.codeplex.com/)，单击“下载”。

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. 下载 [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) 并在解压缩后安装。

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. 安装之后，打开该应用程序。
4. 单击“添加帐户”。

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. 指定存储帐户名称、存储帐户密钥和存储终结点域。 这是 Azure 订阅中的存储帐户，你通过其将 VHD 保存在 Azure 门户中。

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Azure 存储资源管理器连接到特定存储帐户后，它将开始显示该存储帐户中的所有容器。 选择已复制操作系统磁盘 VHD 文件的容器以及数据磁盘（如果这些磁盘适用于方案）。

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. 选择 blob 容器后，Azure 存储资源管理器开始显示容器中的文件。 选择需要提交的映像文件 (.vhd)。

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  选择容器中的 .vhd 文件后，请单击“安全”选项卡。

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  在“Blob 容器安全”对话框中，保留“访问级别”选项卡上的默认值，然后单击“共享访问签名”选项卡。

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. 请遵循以下步骤，为 .vhd 映像生成共享访问签名 URI：

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **允许访问开始日期**：为了保证 UTC 时间，请选择当前日期的前一天。 例如，如果当前时间是 2014 年 10 月 6 日，则选择 10/5/2014。

    b. **允许访问结束日期**：选择“允许访问开始日期”后至少 3 周的日期。

    c. **允许的操作**：选择“列出”和“读取”权限。

    d.单击“下一步”。 如果已正确选择 .vhd 文件，则文件显示在“要访问的 Blob 名称”中，并带有扩展名 .vhd。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“生成签名”。

    f. 在“此容器中生成的共享访问签名 URI”中，查看以下在上文中亮显的内容：

        - 请确保映像文件名称和“.vhd”均在 URI 中。
        - 在签名末尾，确保显示“=rl”。 这显示“读取”和“列出”访问权限已成功提供。
        - 在签名中间，确保显示“sr=c”。 这表示你具有容器级访问权限

11. 若要确保生成的共享访问签名 URI 可运行，请单击“在浏览器中测试”。 这应开始下载过程。

12. 复制共享访问签名 URI。 这是要粘贴到发布门户中的 URI。

13. 对 SKU 中的每个 VHD 重复步骤 6-10。

**Microsoft Azure 存储资源管理器 (Windows/MAC/Linux)**

下面是使用 Microsoft Azure 存储资源管理器生成 SAS URL 的步骤

1.  从 [http://storageexplorer.com/](http://storageexplorer.com/) 网站下载 Microsoft Azure 存储资源管理器。 转到 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/releasenotes.html)，单击“下载 Windows 版”。

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  安装之后，打开该应用程序。

3.  单击“添加帐户”。

4.  登录到你的帐户，在订阅中配置 Microsoft Azure 存储资源管理器

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  转到存储帐户并选择“容器”

6.  选择“获取共享访问签名...” （通过右键单击“容器”）

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  按照以下所示更新“开始时间”、“到期时间”和“权限”

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  **开始时间**：为了保证 UTC 时间，请选择当前日期的前一天。 例如，如果当前时间是 2014 年 10 月 6 日，则选择 10/5/2014。

    b.保留“数据库类型”设置，即设置为“共享”。  **到期时间**：选择至少为“开始时间”后 3 周的日期。

    c.  **权限**：选择“列出”和“读取”权限

8.  复制容器共享访问签名 URI

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    生成的 SAS URL 用于容器级别，现在我们需要在其中添加 VHD 名称。

    容器级别 SAS URL 的格式：`https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    在 SAS URL 中的容器名称后插入 VHD 名称，如下所示：`https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    示例：

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd 为 VHD 名称，则 VHD SAS URL 将为 `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - 请确保映像文件名称和“.vhd”均在 URI 中。
    - 在签名中间，确保显示“sp=rl”。 这显示“读取”和“列出”访问权限已成功提供。
    - 在签名中间，确保显示“sr=c”。 这表示你具有容器级访问权限

9.  若要确保生成的共享访问签名 URI 可正常运行，请在浏览器中测试它。 这应开始下载过程

10. 复制共享访问签名 URI。 这是要粘贴到发布门户中的 URI。

11. 对 SKU 中的每个 VHD 重复这些步骤。

**Azure CLI（推荐用于非 Windows 和持续集成）**

下面是使用 Azure CLI 生成 SAS URL 的步骤

1.  从[此处](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/)下载 Microsoft Azure CLI。 还可以找到用于 **[Windows](http://aka.ms/webpi-azure-cli)** 和 **[MAC OS](http://aka.ms/mac-azure-cli)** 的不同链接。

2.  下载后，请安装

3.  使用以下代码创建一个 PowerShell 文件并将它保存在本地

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    更新上述代码中的以下参数

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **`<StorageAccountName>`**：指定存储帐户名称

    b.保留“数据库类型”设置，即设置为“共享”。 **`<Storage Account Key>`**：指定存储帐户密钥

    c. **`<Permission Start Date>`**：为了保证 UTC 时间，请选择当前日期的前一天。 例如，如果当前日期是 2016 年 10 月 26 日，则值应为 10/25/2016

    d.单击“下一步”。 **`<Permission End Date>`**：选择至少为“开始日期”后 3 周的日期。 则值应为“11/02/2016”。

    以下是更新适当参数后的代码示例

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  在“以管理员身份运行”模式下打开 Powershell 编辑器并打开步骤 #3 中的文件。

5.  运行脚本，脚本将为你提供容器级别访问权限所对应的 SAS URL

    以下是 SAS 签名的输出，请将突出显示部分复制到记事本中

    ![绘制](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  现在，你将获得容器级别 SAS URL，并需要在其中添加 VHD 名称。

    容器级别 SAS URL #

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  在 SAS URL 中的容器名称后插入 VHD 名称，如下所示：`https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    示例：

    TestRGVM201631920152.vhd 为 VHD 名称，则 VHD SAS URL 将为

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - 请确保映像文件名称和“.vhd”均在 URI 中。
    -   在签名中间，确保显示“sp=rl”。 这显示“读取”和“列出”访问权限已成功提供。
    -   在签名中间，确保显示“sr=c”。 这表示你具有容器级访问权限

8.  若要确保生成的共享访问签名 URI 可正常运行，请在浏览器中测试它。 这应开始下载过程

9.  复制共享访问签名 URI。 这是要粘贴到发布门户中的 URI。

10. 对 SKU 中的每个 VHD 重复这些步骤。


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 在发布门户中提供有关 VM 映像的信息并请求认证
创建了产品/服务和 SKU 之后，应输入与该 SKU 关联的映像详细信息：

1. 转到[发布门户][link-pubportal]，然后使用卖方帐户登录。
2. 选择“VM 映像”选项卡。
3. 页面顶部列出的标识符实际上是产品/服务标识符，而不是 SKU 标识符。
4. 在“SKU”部分下填写属性。
5. 在“操作系统系列”下，选择与操作系统 VHD 关联的操作系统类型。
6. 在“操作系统”框下，描述操作系统。 请考虑诸如操作系统系列、类型、版本和更新这样的格式。 一个示例是“Windows Server Datacenter 2014 R2”。
7. 选择最多六个建议的虚拟机大小。 在客户决定购买和部署你的映像时，这些是在 Azure 门户的“定价层”边栏选项卡中向他们显示的建议。 **这些仅仅是建议。客户能够选择可容纳在映像中指定的磁盘的任何 VM 大小。**
8. 输入版本。 版本字段封装标识产品及其更新的语义版本：
   * 版本格式应为 X.Y.Z，X、Y 和 Z 是整数。
   * 不同 SKU 中的映像拥有不同的主版本和次版本。
   * SKU 中的版本应仅为增量更改，即提升修补程序版本（X.Y.Z 中的 Z）。
9. 在“OS VHD URL”框中，输入为操作系统 VHD 创建的共享访问签名 URI。
10. 如果存在与此 SKU 关联的数据磁盘，请选择要在部署时用于装载此数据磁盘的逻辑单元号 (LUN)。
11. 在“LUN X VHD URL”框中，输入为第一个数据 VHD 创建的共享访问签名 URI。

    ![绘制](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>常见的 SAS URL 问题和修补程序

|问题|失败消息|解决方法|文档链接|
|---|---|---|---|
|复制映像时失败 - 在 SAS url 中找不到 "?"|失败：复制映像。 无法使用所提供的 SAS Uri 下载 blob。|使用建议的工具更新 SAS Url|[https://azure.microsoft.com/zh-cn/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|复制映像时失败 -“st”和“se”参数不在 SAS url 中|失败：复制映像。 无法使用所提供的 SAS Uri 下载 blob。|使用其开始和结束日期更新 SAS Url|[https://azure.microsoft.com/zh-cn/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|复制映像时失败 - "sp = rl" 不在 SAS url 中|失败：复制映像。 无法使用所提供的 SAS Uri 下载 blob|使用设置为“读取”和“列出”的权限更新 SAS Url|[https://azure.microsoft.com/zh-cn/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|复制映像时失败 - SAS url 的 vhd 名称中包含空格|失败：复制映像。 无法使用所提供的 SAS Uri 下载 blob。|将 SAS Url 更新为不包含空格|[https://azure.microsoft.com/zh-cn/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|复制映像时失败 - SAS Url 授权错误|失败：复制映像。 由于授权错误，无法下载 blob|重新生成 SAS Url|[https://azure.microsoft.com/zh-cn/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|


## <a name="next-step"></a>后续步骤
填写完 SKU 详细信息后，可前进到 [Azure 应用商店市场营销内容指南][link-pushstaging]。 在发布过程的该步骤中，提供市场营销内容、定价和其他必要信息，然后执行**步骤 3：在过渡环境中测试 VM 产品/服务**，具体是先测试各种用例方案，然后将产品/服务部署到 Azure 应用商店，使公众都可以看到和购买。  

## <a name="see-also"></a>另请参阅
* [入门：如何将产品/服务发布到 Azure 应用商店](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/

