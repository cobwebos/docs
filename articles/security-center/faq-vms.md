---
title: Azure 安全中心常见问题解答-关于虚拟机的问题
description: 有关 Azure 安全中心中的虚拟机的常见问题，一种可帮助防止、检测和响应威胁的产品
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: d84085115816a8fe1cba65e191ea391dd91a4aed
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599361"
---
# <a name="faq---questions-about-virtual-machines"></a>常见问题解答-关于虚拟机的问题


## <a name="what-types-of-virtual-machines-are-supported"></a>支持哪些类型的虚拟机？

使用[经典部署模式和 Resource Manager 部署模式](../azure-classic-rm.md)创建的虚拟机 (VM) 支持监视和建议。

有关支持平台的列表，请参阅[ Azure 安全中心支持的平台](security-center-os-coverage.md)。


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure 安全中心为何不能识别我的 Azure VM 上运行的反恶意软件解决方案？

Azure 安全中心可识别出通过 Azure 扩展安装的反恶意软件。 例如，安全中心无法检测出预装在所提供的映像上的反恶意软件或使用自己的进程（例如配置管理系统）在虚拟机上安装的反恶意软件。


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>为什么会显示消息称 VM“缺少扫描数据”？

没有可供 VM 扫描的数据时会出现此消息。 在 Azure 安全中心启用数据收集之后，扫描数据导入可能需要一段时间（低于一小时）。 首次导入扫描数据后可能会收到此消息，因为此时根本没有扫描数据或没有最近扫描数据。 扫描不会导入处于停止状态的 VM。 如果最近（根据 Windows 代理的保留策略，此值默认为 30 天）没有导入扫描数据，也可能会显示此消息。


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>安全中心多长时间扫描一次操作系统漏洞、系统更新和终结点保护问题？

下面是安全中心扫描漏洞、更新和问题的延迟时间：

- 操作系统安全配置 - 数据在 48 小时内更新
- 系统更新 – 数据在 24 小时内更新
- 终结点保护问题 – 数据在 8 小时内更新

安全中心通常每小时扫描一次新数据，并相应地刷新建议。 

> [!NOTE]
> 安全中心使用 Microsoft Monitoring Agent 来收集和存储数据。 若要了解详细信息，请参阅 [Azure 安全中心平台迁移](security-center-platform-migration.md)。


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>为什么会显示消息称“VM 代理缺失”？

VM 代理必须安装在 VM 上才能启用数据收集。 对于从 Azure 市场部署的 VM，默认安装 VM 代理。 有关如何在其他 VM 上安装 VM 代理的详细信息，请参阅博客文章 [VM 代理和扩展](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)。