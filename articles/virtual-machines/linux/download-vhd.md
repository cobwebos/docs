---
title: 从 Azure 下载 Linux VHD
description: 使用 Azure CLI 和 Azure 门户下载 Linux VHD。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 897cae53e589f4058e5499c0e6e941d4f1d9bb2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87761038"
---
# <a name="download-a-linux-vhd-from-azure"></a>从 Azure 下载 Linux VHD

本文介绍如何使用 Azure 门户从 Azure 下载 Linux 虚拟硬盘 (VHD) 文件。 

## <a name="stop-the-vm"></a>停止 VM

如果 VHD 附加到正在运行的 VM，则不能从 Azure 下载。 需要停止 VM，才能下载 VHD。 

1.  登录 [Azure 门户](https://portal.azure.com/)。
2.  在左侧菜单中，选择“虚拟机”。
3.  从列表中选择 VM。
4.  在 VM 的页面上，选择“停止”。

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="显示用于停止 VM 的菜单按钮。":::

## <a name="generate-sas-url"></a>生成 SAS URL

若要下载 VHD 文件，需要生成[共享访问签名 (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL。 生成 URL 时，将为 URL 分配到期时间。

1. 在 VM 页的菜单上，选择“磁盘”****。
2. 为 VM 选择操作系统磁盘，然后选择“磁盘导出”。
1. 如果需要，请更新“URL 到期时间(秒)”的值，留出足够的时间来完成下载。 默认值为 3600 秒（1 小时）。
3. 选择“生成 URL”。
 
      
## <a name="download-vhd"></a>下载 VHD

1.  在生成的 URL 下，选择“下载 VHD 文件”。

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="显示用于停止 VM 的菜单按钮。":::

2.  可能需要选择浏览器中的“保存”**** 以开始下载。 VHD 文件的默认名称为 *abcd*。

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure CLI 从自定义磁盘上传并创建 Linux VM](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 
- [使用 Azure CLI 管理 Azure 磁盘](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
