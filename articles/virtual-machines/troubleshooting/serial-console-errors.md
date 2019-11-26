---
title: Azure Serial Console errors | Microsoft Docs
description: Common errors within the Azure Serial Console
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 7bd9fe4044dace4061285c016cb08562b556b98e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483623"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Common errors within the Azure Serial Console
There are a set of known errors within the Azure Serial Console. This is a list of those errors and mitigation steps for them.

## <a name="common-errors"></a>常见错误

错误                             |   缓解措施
:---------------------------------|:--------------------------------------------|
"Azure Serial Console requires boot diagnostics to be enabled. Click here to configure boot diagnostics for your virtual machine." ![Boot diagnostics error](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Ensure that the VM or virtual machine scale set has [boot diagnostics](boot-diagnostics.md) enabled. If you are using serial console on a virtual machine scale set instance, ensure that your instance has the latest model.
"Azure Serial Console requires a virtual machine to be running. Use the Start button above to start your virtual machine." ![Deallocated error](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | The VM or virtual machine scale set instance must be in a started state to access the serial console (your VM must not be stopped or deallocated). Ensure your VM or virtual machine scale set instance is running and try again.
"Azure Serial Console is not enabled for this subscription, contact your subscription administrator to enable." ![Subscription disabled error](./media/virtual-machines-serial-console/virtual-machines-serial-console-subscription-disabled-error.png) | The Azure Serial Console can be disabled at a subscription level. If you are a subscription administrator, you may [enable and disable the Azure Serial Console](./serial-console-enable-disable.md). If you are not a subscription administrator, you should reach out to your subscription administrator for next steps.
访问此 VM 的启动诊断存储帐户时遇到“已禁止”响应。 ![Storage account firewall error](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| 请确保启动诊断没有帐户防火墙。 若要使串行控制台正常运行，需要一个可访问的启动诊断存储帐户。 根据设计串行控制台无法与启动诊断存储帐户上启用的存储帐户防火墙一起使用。
没有所需的权限，无法使用此 VM 来访问串行控制台。 请确保至少拥有“虚拟机参与者”角色权限。| The serial console access requires you to have contributor level access or above on your VM or virtual machine scale set. For more information, see the [overview page](serial-console-overview.md).
The storage account '' used for boot diagnostics on this VM could not be found. Verify that boot diagnostics is enabled for this VM, this storage account has not been deleted, and you have access to this storage account. | Double check that you have not deleted the boot diagnostics storage account for your VM or virtual machine scale set
Provisioning for this VM has not yet succeeded. Please ensure the VM is fully deployed and retry the serial console connection. | Your VM or virtual machine scale set may still be provisioning. Wait some time and try again.
You do not have the required permissions to write to the boot diagnostics storage account for this VM. Please ensure you have at least VM Contributor permissions on ''. | Serial console access requires contributor level access on the boot diagnostics storage account. For more information, see the [overview page](serial-console-overview.md).
无法确定启动诊断存储帐户 *&lt;STORAGEACCOUNTNAME&gt;* 的资源组。 确认是否为此 VM 启用了启动诊断，以及是否有权访问此存储帐户。 | Serial console access requires contributor level access on the boot diagnostics storage account. For more information, see the [overview page](serial-console-overview.md).
Web 套接字已关闭或无法打开。 | You may need to add firewall access to `*.console.azure.com`. A more detailed but longer approach is to allow firewall access to the [Microsoft Azure Datacenter IP ranges](https://www.microsoft.com/download/details.aspx?id=41653), which change fairly regularly.
Serial console does not work with a storage account using Azure Data Lake Storage Gen2 with hierarchical namespaces. | This is a known issue with hierarchical namespaces. To mitigate, ensure that your VM's boot diagnostics storage account is not created using Azure Data Lake Storage Gen2. This option can only be set upon storage account creation. You may have to create a separate boot diagnostics storage account without Azure Data Lake Storage Gen2 enabled to mitigate this issue.


## <a name="next-steps"></a>后续步骤
* Learn more about the [Azure Serial Console for Linux VMs](./serial-console-linux.md)
* Learn more about the [Azure Serial Console for Windows VMs](./serial-console-windows.md)