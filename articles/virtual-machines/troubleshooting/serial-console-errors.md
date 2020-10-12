---
title: Azure 串行控制台错误 |Microsoft Docs
description: Azure 串行控制台中的常见错误
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
ms.openlocfilehash: cad12a55332a6c7898f9709776c58d7dba8dd81a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526429"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure 串行控制台中的常见错误
Azure 串行控制台中有一组已知错误。 这是这些错误的列表以及这些错误的缓解步骤。

## <a name="common-errors"></a>常见错误

错误                             |   缓解措施
:---------------------------------|:--------------------------------------------|
"Azure 串行控制台要求启用启动诊断。 单击此处配置虚拟机的启动诊断。 | 请确保 VM 或虚拟机规模集已启用 [启动诊断](boot-diagnostics.md) 。 如果在虚拟机规模集实例上使用串行控制台，请确保实例具有最新的模型。
"Azure 串行控制台要求虚拟机运行。 使用上面的 "启动" 按钮启动您的虚拟机。  | VM 或虚拟机规模集实例必须处于已启动状态才能访问串行控制台 (不得停止或释放 VM) 。 请确保 VM 或虚拟机规模集实例正在运行，然后重试。
"没有为此订阅启用 Azure 串行控制台，请与你的订阅管理员联系以启用。" | 可以在订阅级别禁用 Azure 串行控制台。 如果你是订阅管理员，你可以 [启用和禁用 Azure 串行控制台](./serial-console-enable-disable.md)。 如果你不是订阅管理员，则应该联系订阅管理员以了解后续步骤。
访问此 VM 的启动诊断存储帐户时遇到“已禁止”响应。 | 请确保启动诊断没有帐户防火墙。 若要使串行控制台正常运行，需要一个可访问的启动诊断存储帐户。 根据设计串行控制台无法与启动诊断存储帐户上启用的存储帐户防火墙一起使用。
没有所需的权限，无法使用此 VM 来访问串行控制台。 请确保至少拥有“虚拟机参与者”角色权限。| 通过串行控制台访问，你可以在 VM 或虚拟机规模集上访问参与者级别或更高级别。 有关详细信息，请参阅 [概述页](serial-console-overview.md)。
在此 VM 上找不到用于启动诊断的存储帐户 ""。 验证是否为此 VM 启用了启动诊断，此存储帐户未被删除，并且你有权访问此存储帐户。 | 仔细检查你是否未删除 VM 或虚拟机规模集的启动诊断存储帐户
与 VM 的串行控制台连接遇到错误： "错误的请求" (400)  | 如果启动诊断 URI 不正确，可能会发生这种情况。 例如，使用了 "http://" 而不是 "https://"。 可以通过以下命令修复启动诊断 URI： `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
你没有写入此 VM 的启动诊断存储帐户所需的权限。 请确保至少具有 VM 参与者权限 | 串行控制台访问需要启动诊断存储帐户的参与者级别访问权限。 有关详细信息，请参阅 [概述页](serial-console-overview.md)。
无法确定启动诊断存储帐户* &lt; STORAGEACCOUNTNAME &gt; *的资源组。 确认是否为此 VM 启用了启动诊断，以及是否有权访问此存储帐户。 | 串行控制台访问需要启动诊断存储帐户的参与者级别访问权限。 有关详细信息，请参阅 [概述页](serial-console-overview.md)。
尚未成功预配此 VM。 请确保已完全部署 VM，并重试串行控制台连接。 | 你的 VM 或虚拟机规模集可能仍在进行设置。 请等待一段时间，然后重试。
Web 套接字已关闭或无法打开。 | 可能需要将防火墙访问权限添加到 `*.console.azure.com` 。 更详细但更好的方法是允许防火墙访问 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)，这种范围会定期发生变化。
串行控制台不能使用具有分层命名空间的 Azure Data Lake Storage Gen2 的存储帐户。 | 这是分层命名空间的已知问题。 若要缓解此情况，请确保不使用 Azure Data Lake Storage Gen2 创建 VM 的启动诊断存储帐户。 仅可在创建存储帐户时设置此选项。 你可能需要创建单独的启动诊断存储帐户，而不启用 Azure Data Lake Storage Gen2 来缓解此问题。
与 VM 的串行控制台连接遇到错误： "禁止" (SubscriptionNotEnabled) -订阅名称未定义，id 处于未定义的未 \<subscription id> 启用状态 | 如果用户已在中创建了其 Cloud Shell 的存储帐户的订阅已禁用，则可能出现此问题。 若要缓解这种情况，请启动 Cloud Shell 并 [执行必要的步骤](../../cloud-shell/persisting-shell-storage.md#unmount-clouddrive-1) ，为当前订阅中的 Cloud Shell 重新设置后备存储帐户。

## <a name="next-steps"></a>后续步骤
* 详细了解适用于 [Linux vm 的 Azure 串行控制台](./serial-console-linux.md)
* 详细了解适用于 [Windows vm 的 Azure 串行控制台](./serial-console-windows.md)
