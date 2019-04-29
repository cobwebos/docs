---
title: 开始使用 TmaxSoft OpenFrame Azure 虚拟机上
description: 重新托管使用 TmaxSoft OpenFrame 环境在 Azure 虚拟机 (Vm) 在 IBM z/OS 大型机工作负荷。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485328"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>开始使用 Azure 上的 TmaxSoft OpenFrame

需要你的现有大型机资产并将它们移动到 Microsoft Azure 中使用 TmaxSoft OpenFrame。 此受欢迎的重新承载解决方案在 Azure 上，使您能够快速将迁移应用程序创建仿真环境。 不重新格式化是必需的。

## <a name="openframe-rehosting-environment"></a>OpenFrame 重新承载环境

设置 OpenFrame 环境在 Azure 上进行开发、 演示、 测试或生产工作负荷。 下图显示了，OpenFrame 包括在 Azure 创建大型机仿真环境的多个组件。 例如，OpenFrame 联机服务替换如 IBM 客户信息控制系统 (CICS) 大型机中间件。 OpenFrame 批处理使用其 TJES 组件，将替换 IBM 大型机作业条目子系统 (JES)。 

![OpenFrame 重新承载进程](media/openframe-01.png)

> [!NOTE]
> 若要在 Azure 上运行 OpenFrame 环境，必须具有有效的产品许可证或试用版许可证的 TmaxSoft。

## <a name="openframe-components"></a>OpenFrame 组件

以下组件是在 Azure 上的 OpenFrame 环境的一部分：

- **迁移工具**包括 OFMiner，分析大型机资产，然后将其迁移到 Azure 的解决方案。
- **编译器**，包括 OFCOBOL，编译器将解释大型机的 COBOL 程序;OFPLI，其中解释了主机的 PL / 我程序;和 OFASM，编译器将解释大型机的组装器程序。
- **前端**组件，包括 Java 企业用户解决方案 (JEUS)、 用于 Java Enterprise Edition 6.OFGW 认证的 web 应用程序服务器和提供 3270 侦听器的 OpenFrame 网关组件。
- **应用程序**环境。 OpenFrame 基数是管理整个系统的中间件。 OpenFrame 服务器类型 C (OSC) 取代了大型机的中间件和 IBM CICS。
- **关系数据库**，如 Tibero （显示）、 Oracle 数据库、 Microsoft SQL Server、 IBM Db2 或 MySQL。 OpenFrame 应用程序使用开放式数据库连接 (ODBC) 协议来与数据库通信。
- **安全**通过 TACF，控制用户对系统和资源访问的服务模块。 
- **OFManager**是一个解决方案，可以在 web 环境中的 OpenFrame 的操作和管理功能。

![OpenFrame 体系结构](media/openframe-02.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure 上安装 TmaxSoft OpenFrame](./install-openframe-azure.md)
