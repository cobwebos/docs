---
title: Azure 开发测试实验室基础设施治理
description: 本文主要介绍如何监管 Azure 开发测试实验室基础结构。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 75ce5d6a88b5398bd010cc363b4241bc90068f55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60192993"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Azure 开发测试实验室基础结构的监管 - 应用程序迁移和集成
建立开发/测试实验室环境之后，你需要考虑以下问题：

- 如何在项目团队中利用此环境？
- 如何在确保遵循任何必需组织策略的同时又能够保持灵活性，为应用程序增加价值？

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure 市场映像与自定义映像

### <a name="question"></a>问题
何时应使用 Azure 市场映像以及自己的自定义组织映像？

### <a name="answer"></a>Answer
除非有特定考虑或组织要求，否则默认情况下应使用 Azure 市场映像。 一些常见示例包括：

- 复杂的软件设置，需要将应用程序作为基本映像的一部分包含在内。
- 安装和设置应用程序可能需要花费数小时，这没有有效利用 Azure 市场映像上添加的计算时间。
- 开发人员和测试人员需要快速访问虚拟机，并希望最大限度地缩短新虚拟机的设置时间。
- 所有计算机必须具备的合规性或法规条件（例如安全策略）。

不应轻易使用自定义映像。 它们会引入额外的复杂性，因为你现在必须管理那些底层基础映像的 VHD 文件。 此外，还需要使用软件更新定期修补这些基础映像。 这些更新包括新的操作系统 (OS) 更新，以及软件包本身所需的任何更新或配置更改。

## <a name="formula-vs-custom-image"></a>公式与自定义映像

### <a name="question"></a>问题
何时应使用公式以及自定义映像？

### <a name="answer"></a>Answer
通常，此方案中的决定因素是成本和重用。

如果你的方案中有许多用户/实验室需要在基础映像之上使用具有大量软件的映像，则可以通过创建自定义映像来降低成本。 这意味着该映像创建一次。 它减少了虚拟机设置时间以及由于在设置时运行虚拟机而产生的成本。

但是，需要注意的另一个因素是软件包的更改频率。 如果运行每日内部版本，并要求该软件位于用户的虚拟机上，请考虑使用公式而不是自定义映像。

## <a name="use-custom-organizational-images"></a>使用自定义组织映像

### <a name="question"></a>问题
如何设置一个易于重复的过程以便将我的自定义组织映像导入开发测试实验室环境？

### <a name="answer"></a>Answer
请观看[这一有关映像工厂模式的视频](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)。 此方案是一个高级方案，提供的脚本都只是示例脚本。 如果需要进行任何更改，则需要管理和维护在环境中使用的脚本。

使用开发测试实验室在 Azure Pipelines 中创建自定义映像管道：

- [Introduction:Get VMs ready in minutes by setting up an image factory in Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)（简介：通过在 Azure 开发测试实验室中设置映像工厂来在几分钟内准备好 VM）
- [Image Factory – Part 2!Setup Azure Pipelines and Factory Lab to Create VMs](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)（映像工厂 - 第 2 部分！设置 Azure Pipelines 和工厂实验室以创建 VM）
- [Image Factory – Part 3:Save Custom Images and Distribute to Multiple Labs](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)（映像工厂 - 第 3 部分：保存自定义映像并分发到多个实验室）
- [视频：Custom Image Factory with Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)（视频：使用 Azure 开发测试实验室自定义映像工厂）

## <a name="patterns-to-set-up-network-configuration"></a>用于设置网络配置的模式

### <a name="question"></a>问题
如何确保开发和测试虚拟机无法访问公共 Internet？ 是否有任何建议的模式来设置网络配置？

### <a name="answer"></a>Answer
是的。 有两个方面需要考虑，即入站和出站流量。

入站流量 – 如果虚拟机没有公用 IP 地址，则无法通过 Internet 访问它。 一种常见方法是确保设置订阅级别的策略，以便任何用户都不能创建公用 IP 地址。

出站流量 – 如果要防止虚拟机直接访问公共 Internet 并强制流量通过企业防火墙，则可以使用强制路由通过快速路由或 VPN 在本地路由流量。

> [!NOTE]
> 如果你的代理服务器在没有代理设置的情况下阻止流量，则不要忘记向实验室的项目存储帐户添加例外。

此外，还可以将网络安全组用于虚拟机或子网。 此步骤增添了一层额外的保护以允许/阻止流量。

## <a name="new-vs-existing-virtual-network"></a>新虚拟网络与现有虚拟网络

### <a name="question"></a>问题
何时应该为开发测试实验室环境创建新的虚拟网络以及使用现有的虚拟网络？

### <a name="answer"></a>Answer
如果你的 VM 需要与现有基础结构进行交互，则应考虑使用开发测试实验室环境中的现有虚拟网络。 此外，如果使用 ExpressRoute，则可能需要最大限度减少 VNet/子网的数量，以便不会分割已分配供在订阅中使用的 IP 地址空间。 另外，还应该考虑在此处使用 VNet 对等互连模式（中心辐射型模型）。 此方法支持在给定区域内跨订阅进行 VNet/子网通信，但跨区域对等互连是 Azure 网络中的一项即将推出的新功能。

除此之外，每个开发测试实验室环境可以有其自己的虚拟网络。 但请注意，每个订阅的虚拟网络数量有[限制](../azure-subscription-service-limits.md)。 默认数量为 50，但此限制可以提高到 100。

## <a name="shared-public-or-private-ip"></a>共享 IP、公用 IP 或专用 IP

### <a name="question"></a>问题
何时应使用共享 IP 与公用 IP 与专用 IP？

### <a name="answer"></a>Answer
如果使用站点到站点 VPN 或快速路由，则考虑使用专用 IP，以便你的计算机可通过内部网络访问，并且无法通过公共 Internet 访问。

> [!NOTE]
> 实验室所有者可以更改此子网策略，以确保不会有人意外地为其 VM 创建公共 IP 地址。 订阅所有者应创建一个阻止创建公用 IP 的订阅策略。

当使用共享的公用 IP 时，实验室中的虚拟机会共享公用 IP 地址。 当需要避免违反给定订阅的公用 IP 地址限制时，此方法很有用。

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>每个用户或实验室的虚拟机数量限制

### <a name="question"></a>问题
在为每个用户或每个实验室应该设置多少个虚拟机方面是否有相应的规则？

### <a name="answer"></a>Answer
在考虑每个用户或每个实验室的虚拟机数量时，主要有三个考虑因素：

- 团队在实验室资源上花费的总成本 。 运行很多计算机很容易。 为了控制成本，一种机制是限制每个用户和/或每个实验室的 VM 数量
- 实验室中的虚拟机总数受可用[订阅级别配额](../azure-subscription-service-limits.md)的影响。 其中一个上限是每个订阅 800 个资源组。 开发测试实验室当前为每个 VM 创建一个新的资源组（除非使用共享的公用 IP）。 如果在订阅中有 10 个实验室，实验室可满足每个实验室 （800 上限 – 10 个实验室本身的 10 个资源组） 中的大约 79 虚拟机 = 79 每个实验室的虚拟机。
- 举例来说，如果实验室通过 Express Route 连接到本地，则可以为 VNet/子网定义可用的 IP 地址空间 。 为了确保能够在实验室中创建 VM（出现“无法获取 IP 地址”错误时无法创建），实验室所有者可以根据可用的 IP 地址空间指定每个实验室的最大 VM 数量。

## <a name="use-resource-manager-templates"></a>使用 Resource Manager 模板

### <a name="question"></a>问题
如何在开发测试实验室环境中使用资源管理器模板？

### <a name="answer"></a>Answer
可以使用[开发测试实验室中的环境功能](devtest-lab-test-env.md)一文中所述的步骤，将资源管理器模板部署到开发测试实验室环境中。 从根本上说，将资源管理器模板签入 Git 存储库（Azure Repos 或 GitHub），并将[模板的专用存储库](devtest-lab-test-env.md)添加到实验室。

如果使用开发测试实验室托管开发计算机，则此方案可能没有用；但如果要构建具有生产代表性的过渡环境，则此方案可能很有用。

还值得注意的是，每个实验室或每个用户的虚拟机数量选项仅限制在实验室自身中本机创建的计算机数量，而不是由任何环境（资源管理器模板）创建的计算机数量。

## <a name="next-steps"></a>后续步骤
请参阅[在开发测试实验室中使用环境](devtest-lab-test-env.md)。