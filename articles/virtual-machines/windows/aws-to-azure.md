---
title: "将 Windows AWS VM 移到 Azure | Microsoft Docs"
description: "使用 Azure PowerShell 将 Amazon Web Services (AWS) EC2 Windows 实例移到 Azure 虚拟机。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: cynthn
ms.openlocfilehash: 7d2b498d3f84c4fd6cccf97c6d7781f293f5b395
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-azure-using-powershell"></a>使用 PowerShell 将 Windows VM 从 Amazon Web Services (AWS) 移到 Azure

如果正在评估用于托管工作负荷的 Azure 虚拟机，可以导出现有 Amazon Web Services (AWS) EC2 Windows VM 实例，然后将虚拟硬盘 (VHD) 上传到 Azure。 上传 VHD 后，可以在 Azure 中从 VHD 创建新的 VM。 

本主题介绍如何将单个 VM 从 AWS 移至 Azure。 如果要大规模地将 VM 从 AWS 移到 Azure，请参阅[使用 Azure Site Recovery 将 Amazon Web Services (AWS) 中的虚拟机迁移到 Azure](../../site-recovery/site-recovery-migrate-aws-to-azure.md)。

## <a name="prepare-the-vm"></a>准备 VM 
 
可将通用化和专用化 VHD 上传到 Azure。 每种类型都需要在从 AWS 导出之前先准备 VM。 

- **通用化 VHD** - 通用化 VHD 包含使用 Sysprep 删除的所有个人帐户信息。 如果打算使用 VHD 作为映像来创建新 VM，应该： 
 
    * [准备 Windows VM](prepare-for-upload-vhd-image.md)。  
    * 使用 Sysprep 将虚拟机通用化。  

 
- **专用化 VHD** - 专用化 VHD 保留原始 VM 中的用户帐户、应用程序和其他状态数据。 如果打算按原样使用 VHD 来创建新 VM，请确保完成以下步骤。  
    * [准备好要上传到 Azure 的 Windows VHD](prepare-for-upload-vhd-image.md)。 **不要**使用 Sysprep 通用化 VM。 
    * 删除 VM 上安装的所有来宾虚拟化工具和代理（例如 VMware 工具）。 
    * 确保 VM 配置为通过 DHCP 提取 IP 地址和 DNS 设置。 这可以确保服务器在启动时获得 VNet 中的 IP 地址。  


## <a name="export-and-download-the-vhd"></a>导出和下载 VHD 

将 EC2 实例导出到 Amazon S3 存储桶中的 VHD。 执行 Amazon 文档主题[使用 VM导入/导出功能导出实例作为 VM](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) 中所述的步骤，然后运行 [create-instance-export-task](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) 命令将 EC2 实例导出到 VHD 文件。 

导出的 VHD 文件将保存在你指定的 Amazon S3 存储桶中。 下面是用于导出 VHD 的基本语法，只需将 <brackets> 中的占位符文本替换为你的信息即可。

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

导出 VHD 后，按照[如何从 S3 存储桶下载对象？](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html)中的说明从 S3 存储桶下载 VHD 文件。 

> [!IMPORTANT]
> AWS 将收取用于下载 VHD 的数据传输费用。 有关详细信息，请参阅 [Amazon S3 定价](https://aws.amazon.com/s3/pricing/)。


## <a name="next-steps"></a>后续步骤

现在可以将 VHD 上传到 Azure 并创建新的 VM 了。 

- 如果导出前在源上运行了 Sysprep 以**通用化**该 VHD，请参阅[上传通用化的 VHD 并使用它在 Azure 中创建新的 VM](upload-generalized-managed.md)
- 如果导出前未运行 Sysprep，VHD 将被视为**专用化**，请参阅[将专用化的 VHD 上传到 Azure 并创建新的 VM](create-vm-specialized.md)

 
