---
title: Azure 虚拟机上的 TmaxSoft OpenFrame 入门
description: 使用 Azure 虚拟机（Vm）上的 TmaxSoft OpenFrame 环境 Rehost IBM z/OS 大型机工作负荷。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "61485328"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Azure 上的 TmaxSoft OpenFrame 入门

获取现有的大型机资产，并使用 TmaxSoft OpenFrame 将它们移动到 Microsoft Azure。 此常见的重新承载解决方案在 Azure 上创建一个仿真环境，使你能够快速迁移应用程序。 不需要重新格式化。

## <a name="openframe-rehosting-environment"></a>OpenFrame 重新承载环境

在 Azure 上设置 OpenFrame 环境，用于开发、演示、测试或生产工作负荷。 如下图所示，OpenFrame 包括在 Azure 上创建大型机模拟环境的多个组件。 例如，OpenFrame 联机服务将大型机中间件（如 IBM 客户信息控制系统（CICS））替换为。 OpenFrame Batch 及其 TJES 组件替换 IBM 主机的作业入口子系统（JES）。 

![OpenFrame 重新承载进程](media/openframe-01.png)

> [!NOTE]
> 若要在 Azure 上运行 OpenFrame 环境，必须具有有效的产品许可证或 TmaxSoft 中的试用版许可证。

## <a name="openframe-components"></a>OpenFrame 组件

以下组件是 Azure 上的 OpenFrame 环境的一部分：

- **迁移工具**（包括 OFMiner），它是一种解决方案，用于分析大型机资产，然后将其迁移到 Azure。
- **编译器（包括**OFCOBOL），用于解释大型机的 COBOL 程序;OFPLI，用于解释大型机的 PL/I 程序;和 OFASM，它是解释大型机汇编程序程序的编译器。
- **前端**组件，包括 Java 企业用户解决方案（JEUS），是一种针对 Java enterprise Edition 6. OFGW 认证的 web 应用程序服务器，以及提供3270侦听器的 OpenFrame 网关组件。
- **应用程序**环境。 OpenFrame 是管理整个系统的中间件。 OpenFrame 服务器类型 C （.OSC）替换了大型机的中间件和 IBM CICS。
- **关系数据库**，例如 Tibero （已显示）、Oracle Database、MICROSOFT SQL SERVER、IBM Db2 或 MySQL。 OpenFrame 应用程序使用开放式数据库连接（ODBC）协议与数据库进行通信。
- 通过 TACF 的**安全**，该服务模块控制用户对系统和资源的访问。 
- **OFManager**是一种解决方案，用于在 web 环境中提供 OpenFrame 的操作和管理功能。

![OpenFrame 体系结构](media/openframe-02.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure 上安装 TmaxSoft OpenFrame](./install-openframe-azure.md)
