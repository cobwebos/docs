---
title: 开始在 Azure 虚拟机上使用 TmaxSoft 开放帧
description: 使用 Azure 虚拟机 （VM） 上的 TmaxSoft OpenFrame 环境重新托管 IBM z/OS 主机工作负载。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485328"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>在 Azure 上使用 TmaxSoft 开放帧开始

获取现有大型机资源，并使用 TmaxSoft OpenFrame 将其移动到 Microsoft Azure。 此流行的重新托管解决方案在 Azure 上创建仿真环境，使您能够快速迁移应用程序。 无需重新格式化。

## <a name="openframe-rehosting-environment"></a>打开帧重新托管环境

在 Azure 上设置 OpenFrame 环境，用于开发、演示、测试或生产工作负荷。 如下图所示，OpenFrame 包括多个组件，这些组件在 Azure 上创建大型机仿真环境。 例如，OpenFrame 在线服务取代了大型机中间件，如 IBM 客户信息控制系统 （CICS）。 OpenFrame Batch 及其 TJES 组件取代了 IBM 主机的作业输入子系统 （JES）。 

![打开帧重新托管过程](media/openframe-01.png)

> [!NOTE]
> 要在 Azure 上运行 OpenFrame 环境，必须拥有 TmaxSoft 的有效产品许可证或试用许可证。

## <a name="openframe-components"></a>开放框架组件

以下组件是 Azure 上的 OpenFrame 环境的一部分：

- **迁移工具**，包括 OFMiner，一种分析大型机资产然后将其迁移到 Azure 的解决方案。
- **编译器**，包括OFCOBOL，一个解释大型机COBOL程序的编译器;OFPLI，它解释大型机的PL/I程序;和 OFASM，一个解释大型机的汇编程序的编译器。
- **前端**组件，包括 Java 企业用户解决方案 （JEUS），一个经过 Java 企业版 6.OFGW 认证的 Web 应用程序服务器，以及提供 3270 侦听器的 OpenFrame 网关组件。
- **应用程序**环境。 OpenFrame Base 是管理整个系统的中间件。 OpenFrame 服务器类型 C （OSC） 取代了大型机的中间件和 IBM CICS。
- **关系数据库**，如 Tibero（如图所示）、Oracle 数据库、微软 SQL 服务器、IBM Db2 或 MySQL。 OpenFrame 应用程序使用开放数据库连接 （ODBC） 协议与数据库通信。
- **通过**TACF 的安全性，TACF 是一个服务模块，用于控制用户对系统和资源的访问。 
- **OFManager**是一种在 Web 环境中提供 OpenFrame 的操作和管理功能的解决方案。

![开放框架架构](media/openframe-02.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure 上安装 TmaxSoft 开放帧](./install-openframe-azure.md)
