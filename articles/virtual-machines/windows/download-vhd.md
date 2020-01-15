---
title: 从 Azure 下载 Windows VHD
description: 使用 Azure 门户下载 Windows VHD。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940457"
---
# <a name="download-a-windows-vhd-from-azure"></a>从 Azure 下载 Windows VHD

本文介绍如何使用 Azure 门户从 Azure 下载 Windows 虚拟硬盘 (VHD) 文件。

## <a name="optional-generalize-the-vm"></a>可选：通用化 VM

如果要使用 VHD 作为[映像](tutorial-custom-images.md)创建其他 vm，则应使用[Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)来通用化操作系统。 

若要使用 VHD 作为映像创建其他 Vm，请通用化虚拟机。

1. 如果尚未登录 [Azure 门户](https://portal.azure.com/)，请先登录。
2. [连接到 VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
3. 在 VM 上，以管理员身份打开“命令提示符”窗口。
4. 将目录切换到 *%windir%\system32\sysprep*，然后运行 sysprep.exe。
5. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”。
6. 在“关闭选项”中选择“关闭”，然后单击“确定”。 


## <a name="stop-the-vm"></a>停止 VM

如果 VHD 附加到正在运行的 VM，则不能从 Azure 下载。 需要停止 VM，才能下载 VHD。 

1. 在 Azure 门户的“中心”菜单上，单击“虚拟机”。
1. 从列表中选择 VM。
1. 在 VM 的边栏选项卡上，单击“停止”。


## <a name="generate-download-url"></a>生成下载 URL

若要下载 VHD 文件，需要生成[共享访问签名 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL。 生成 URL 时，将为 URL 分配到期时间。

1. 在 VM 的页面上，单击左侧菜单中的 "**磁盘**"。
1. 为 VM 选择操作系统磁盘。
1. 在磁盘的页上，从左侧菜单中选择 "**磁盘导出**"。
1. URL 的默认过期时间为*3600*秒。 将此磁盘的时间增加到**36000** 。
1. 单击“生成 URL”。

> [!NOTE]
> 从默认值增加到期时间，以便提供足够时间来下载 Windows Server 操作系统的大型 VHD 文件。 可能需要包含 Windows Server 操作系统的 VHD 文件，以便根据连接花几个小时下载。 如果下载的是数据磁盘的 VHD，则默认时间就足够了。 
> 
> 

## <a name="download-vhd"></a>下载 VHD

1. 在生成的 URL 下，单击“下载 VHD 文件”。
1. 你可能需要单击浏览器中的 "**保存**" 以开始下载。 VHD 文件的默认名称为 *abcd*。

## <a name="next-steps"></a>后续步骤

- 了解如何[将 VHD 文件上传到 Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
- [从存储帐户中的非托管磁盘创建托管磁盘](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- [使用 PowerShell 管理 Azure 磁盘](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

