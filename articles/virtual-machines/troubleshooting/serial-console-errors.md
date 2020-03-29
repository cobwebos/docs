---
title: Azure 串行控制台错误 |微软文档
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
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060688"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure 串行控制台中的常见错误
Azure 串行控制台中有一组已知错误。 这是这些错误的列表，并针对这些错误执行缓解步骤。

## <a name="common-errors"></a>常见错误

错误                             |   缓解操作
:---------------------------------|:--------------------------------------------|
Azure 串行控制台需要启用引导诊断。 单击此处为虚拟机配置启动诊断。 | 确保 VM 或虚拟机规模集已启用[启动诊断](boot-diagnostics.md)。 如果在虚拟机缩放集实例上使用串行控制台，请确保实例具有最新的模型。
Azure 串行控制台需要虚拟机运行。 使用上面的"开始"按钮启动虚拟机。  | VM 或虚拟机规模集实例必须处于启动状态才能访问串行控制台（不得停止或处理 VM）。 确保 VM 或虚拟机规模集实例正在运行，然后重试。
未为此订阅启用 Azure 串行控制台，请与订阅管理员联系以启用。 | 可以在订阅级别禁用 Azure 串行控制台。 如果您是订阅管理员，则可以[启用和禁用 Azure 串行控制台](./serial-console-enable-disable.md)。 如果您不是订阅管理员，则应联系订阅管理员了解后续步骤。
访问此 VM 的启动诊断存储帐户时遇到“已禁止”响应。 | 请确保启动诊断没有帐户防火墙。 若要使串行控制台正常运行，需要一个可访问的启动诊断存储帐户。 根据设计串行控制台无法与启动诊断存储帐户上启用的存储帐户防火墙一起使用。
没有所需的权限，无法使用此 VM 来访问串行控制台。 请确保至少拥有“虚拟机参与者”角色权限。| 串行控制台访问要求您在 VM 或虚拟机规模集中具有参与者级别或以上访问权限。 有关详细信息，请参阅[概述页](serial-console-overview.md)。
找不到用于此 VM 上启动诊断的存储帐户''。 验证是否为此 VM 启用了启动诊断，此存储帐户尚未删除，并且您有权访问此存储帐户。 | 仔细检查您尚未删除 VM 或虚拟机规模集的启动诊断存储帐户
与 VM 的串行控制台连接遇到错误："错误请求" （400） | 如果您的引导诊断 URI 不正确，则可能发生这种情况。 例如，使用"http://"而不是"https://"。 启动诊断 URI 可以通过以下命令进行修复：`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
您没有写入此 VM 的启动诊断存储帐户所需的权限。 请确保您至少具有 VM 参与者权限 | 串行控制台访问需要在引导诊断存储帐户上访问参与者级别。 有关详细信息，请参阅[概述页](serial-console-overview.md)。
无法确定引导诊断存储帐户*&lt;存储帐户的"&gt;存储帐户名称*"的资源组。 确认是否为此 VM 启用了启动诊断，以及是否有权访问此存储帐户。 | 串行控制台访问需要在引导诊断存储帐户上访问参与者级别。 有关详细信息，请参阅[概述页](serial-console-overview.md)。
此 VM 的预配尚未成功。 请确保已完全部署 VM 并重试串行控制台连接。 | VM 或虚拟机规模集可能仍在预配中。 请稍等片刻，然后重试。
Web 套接字已关闭或无法打开。 | 您可能需要向`*.console.azure.com`添加防火墙访问权限。 一个更详细的但更长的方法是允许防火墙访问[微软 Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)，该范围会定期更改。
串行控制台不适用于使用 Azure 数据湖存储 Gen2 具有分层命名空间的存储帐户。 | 这是分层命名空间的已知问题。 要缓解，请确保不会使用 Azure 数据湖存储 Gen2 创建 VM 的启动诊断存储帐户。 此选项只能在创建存储帐户时设置。 您可能需要创建单独的引导诊断存储帐户，而不启用 Azure 数据湖存储 Gen2 来缓解此问题。
与 VM 的串行控制台连接遇到错误："禁止"（订阅未启用） - 订阅名称未定义，id\<订阅 id>处于未启用状态未定义 | 如果用户已在其中创建其云外壳存储帐户的订阅已被禁用，则可能会出现此问题。 为了缓解，启动云外壳并[执行在当前](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1)订阅中为云外壳重新预配备份存储帐户所需的步骤。

## <a name="next-steps"></a>后续步骤
* 了解有关 Linux VM 的[Azure 串行控制台](./serial-console-linux.md)的信息
* 了解有关 Windows VM 的[Azure 串行控制台](./serial-console-windows.md)的信息