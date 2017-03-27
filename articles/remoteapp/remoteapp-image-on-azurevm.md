---
title: "基于 Azure VM 创建 Azure RemoteApp 映像 | Microsoft Docs"
description: "了解如何从 Azure 虚拟机开始创建 Azure RemoteApp 的映像。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d41583ef-6cd8-4115-8dcb-b2cd5b3d301a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 310cb63756806c8fab208c47bce6c7292ea5e5d7
ms.openlocfilehash: 61e8a49475129486505111bd595f98af955ed809


---
# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>基于 Azure 虚拟机创建 Azure RemoteApp 映像
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

你可以从 Azure 虚拟机创建 Azure RemoteApp 映像（其中承载你在集合中共享的应用）。 你还可以选择使用我们添加到满足所有 Azure RemoteApp 映像要求的 Azure VM 映像库中的某个虚拟机映像，这样就可以根据需要将该虚拟机映像作为你自己 VM 的一个起始点。 只需在库中查找“Windows Server 远程桌面会话主机”映像即可。

基于 Azure 虚拟机创建你自己的映像可分为两步：创建映像，然后将其从 Azure VM 库上载到 Azure RemoteApp。

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>创建一个基于 Azure VM 的自定义映像
使用以下步骤创建一个基于 Azure VM 的映像。

1. 创建一个 Azure 虚拟机。 你可以使用 Azure 虚拟机映像库中的“Windows Server 远程桌面会话主机”或“带有 Microsoft Office 365 ProPlus 的 Windows Server 远程桌面会话主机”映像。 此映像满足所有 Azure RemoteApp 模板映像要求。
   
    有关详细信息，请参阅[创建运行 Windows 的 VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
2. 连接到 VM 并安装和配置你希望通过 RemoteApp 共享的应用。 请确保要执行你的应用所要求的任何附加 Windows 配置。
   
    有关详细信息，请参阅[如何登录到运行 Windows Server 的虚拟机](../virtual-machines/virtual-machines-windows-classic-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
3. 如果你使用其中一个 Windows Server 远程桌面会话主机映像，则会存在一个包含的验证脚本，它将确保你的 VM 满足 RemoteApp 必备组件。 若要运行脚本，请双击桌面上的“**ValidateRemoteAppImage**”。 确保脚本报告的所有错误都得到修复之后，再继续执行下一步。
4. SYSPREP 对映像进行通用化，并捕获映像。 有关说明，请参阅[如何捕获一台用作模板的 Windows 虚拟机](../virtual-machines/virtual-machines-windows-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>将映像导入 Azure RemoteApp 映像库
使用以下步骤将新映像导入到 Azure RemoteApp 中：

1. 在“**模板映像**”选项卡中：
   
   * 如果没有现有映像，请单击“**上载或导入模板映像**。
   * 如果已经有至少一个映像，请单击 **+** 以添加新映像。
2. 选择“**从虚拟机库导入映像**”，然后单击“**下一步**”。
3. 在下一页中，从列表选择自定义映像，并确认你遵循在创建映像时列出的步骤。 单击“资源组名称” 的 Azure 数据工厂。
4. 为新的 RemoteApp 映像输入一个名称，并选取位置，然后单击复选标记以开始导入过程。

> [!NOTE]
> 你可以将映像从 Azure 虚拟机支持的任何 Azure 位置导入到 Azure RemoteApp 支持的任何 Azure 位置。 根据位置不同，导入时间可能会长达 25 分钟。
> 
> 

现在，你已准备好创建新的集合，取决于你的需求，新集合可能是[云](remoteapp-create-cloud-deployment.md)集合，也可能是[混合](remoteapp-create-hybrid-deployment.md)集合。




<!--HONumber=Dec16_HO1-->


