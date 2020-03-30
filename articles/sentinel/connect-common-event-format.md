---
title: 将 CEF 数据连接到 Azure 哨兵预览*微软文档
description: 了解如何将 CEF 数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588342"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>使用通用事件格式连接外部解决方案


连接发送 CEF 消息的外部解决方案时，有三个步骤用于连接 Azure Sentinel：

第 1 步：通过部署代理步骤 2[连接 CEF：](connect-cef-agent.md)[执行特定于解决方案的步骤](connect-cef-solution-config.md)步骤 3：[验证连接性](connect-cef-verify.md)

本文介绍了连接的工作原理，提供了先决条件，并为您提供了在 Syslog 上发送公共事件格式 （CEF） 消息的安全解决方案上部署代理的步骤。 

> [!NOTE] 
> 数据存储在运行 Azure Sentinel 的工作区的地理位置中。

为了建立此连接，您需要在专用 Linux 计算机（VM 或本地）上部署代理，以支持设备与 Azure Sentinel 之间的通信。 下图描述了在 Azure 中发生 Linux VM 时设置。

 ![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，如果您在另一个云或本地计算机中使用 VM，则此设置将存在。 

 ![本地 CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>安全注意事项

确保根据组织的安全策略配置计算机的安全性。 例如，您可以将网络配置为与公司网络安全策略保持一致，并更改守护进程中的端口和协议以符合您的要求。 您可以使用以下说明来改进计算机安全配置  [：Azure 中的安全 VM，](../virtual-machines/linux/security-policy.md)[网络安全的最佳做法](../security/fundamentals/network-best-practices.md)。

要使用安全解决方案和 Syslog 计算机之间的 TLS 通信，您需要配置 Syslog 守护进程（rsyslog 或 syslog-ng）以 TLS 进行通信：[使用 TLS-rsyslog 加密 Syslog 流量](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)，[使用 TLS _syslog-ng 加密日志消息](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)。

 
## <a name="prerequisites"></a>先决条件
确保用作代理的 Linux 计算机运行以下操作系统之一：

- 64 位
  - CentOS 6 和 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 和 7
  - Red Hat Enterprise Linux Server 6 和 7
  - Debian GNU/Linux 8 和 9
  - Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 位
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 和 9
   - Ubuntu Linux 14.04 LTS 和 16.04 LTS
 
 - 守护进程版本
   - Syslog-ng： 2.1 - 3.22.1
   - Rsyslog： v8
  
 - 支持系统 RFC
   - 系统 RFC 3164
   - 系统 RFC 5424
 
确保机器也满足以下要求： 
- 权限
    - 您的计算机上必须具有提升的权限 （sudo）。 
- 软件要求
    - 确保 Python 在计算机上运行



## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。

