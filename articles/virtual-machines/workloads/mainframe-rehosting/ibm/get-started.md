---
title: Azure 上的 IBM 工作负荷 |Microsoft Docs
description: 使用 Microsoft 合作伙伴提供的大型机模拟器和其他服务使用 Microsoft Azure rehost IBM z/OS 工作负荷。
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834585"
---
# <a name="ibm-workloads-on-azure"></a>Azure 上的 IBM 工作负荷

许多基于 z/OS 的 IBM 大型机工作负荷可以在 Azure 中进行复制, 而不会丢失任何功能, 甚至不会用户发现其基础系统中的更改。 Azure 上的重新承载应用程序提供所需的大型机功能, 以及云的弹性、可用性和可能的成本节约。

Azure 支持与现有的 IBM 大型机环境集成, 使你可以迁移有意义的 applicates, 在需要时运行混合解决方案, 并随时间推移迁移。 尽管可以为 Azure 完全重写现有的基于大型机的程序, 但更常见的方法是 rehost 它们。 重写增加了迁移项目的成本、复杂性和时间。 借助重新承载, 你可以:

- 将应用程序移动到基于云的模拟器。

- 将数据库迁移到基于云的数据库。

- 使用代码转换引擎替换模块和代码。

此外, IBM 软件 (包括 WebSphere 和 MQ) 现已在 Azure Marketplace 中。 使用 IBM 软件许可证，你可以利用 Azure 提供的按需的基础结构扩展功能来快速启动虚拟机。

广泛的合作伙伴生态系统可帮助你将 IBM 大型机系统迁移到 Azure。 在着手重写或替换应用程序的分阶段部署之前, 最重要的是, 在任何可能的情况上都遵循一种可行的 在[Azure 大型机迁移中心](https://azure.microsoft.com/migration/mainframe/)获取更多有关合作伙伴的指导和帮助。

**后续步骤**

- [大型机迁移: 误解和事实](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [& Azure 上的开发/测试环境安装 IBM zD](./install-ibm-z-environment.md)
- [在 IBM zD & T v1 中设置应用程序开发人员受控分发 (ADCD)](./demo.md)
- [Azure 上的 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
