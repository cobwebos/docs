---
title: Azure Blockchain Workbench 故障排除
description: 如何排查 Azure Blockchain Workbench 应用程序问题
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8a2715666c4fff490f5184b7b8719b412952b9bf
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Azure Blockchain Workbench 故障排除

PowerShell 脚本用于协助开发人员进行调试或提供支持。 此脚本生成摘要并收集进行故障排除所需的详细日志。 收集的日志包括：

* Blockchain 网络，例如 Ethereum
* Blockchain Workbench 微服务
* Application Insights
* Azure 监视 (OMS)

可以根据此信息确定后续步骤和问题的根本原因。 

## <a name="troubleshooting-script"></a>故障排除脚本

PowerShell 故障排除脚本在 GitHub 上提供。 从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/blockchain/archive/master.zip)或克隆该示例。

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>运行脚本
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

运行 `collectBlockchainWorkbenchTroubleshooting.ps1` 脚本可收集日志并创建一个 ZIP 文件，该文件包含一个文件夹，其中有故障排除信息。 例如：

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
此脚本接受以下参数：

| 参数  | 说明 | 必选 |
|---------|---------|----|
| 订阅 ID | SubscriptionID，用于创建或定位所有资源。 | 是 |
| ResourceGroupName | Blockchain Workbench 部署时所在的 Azure 资源组的名称。 | 是 |
| OutputDirectory | 用于创建输出 .ZIP 文件的路径。 如果未指定，则默认为当前目录。 | 否
| OmsSubscriptionId | 在其中部署了 OMS 的订阅 ID。 传递此参数的前提是适用于区块链网络的 OMS 部署在 Blockchain Workbench 的资源组外面。| 否 |
| OmsResourceGroup |在其中部署了 OMS 的资源组。 传递此参数的前提是适用于区块链网络的 OMS 部署在 Blockchain Workbench 的资源组外面。| 否 |
| OmsWorkspaceName | OMS 工作区名称。 传递此参数的前提是适用于区块链网络的 OMS 部署在 Blockchain Workbench 的资源组外面 | 否 |

## <a name="what-is-collected"></a>收集什么内容？

输出 ZIP 文件包含以下文件夹结构：

| 文件夹\文件 | 说明  |
|---------|---------|
| \Summary.txt | 系统摘要 |
| \metrics\blockchain | 有关区块链的指标 |
| \metrics\workbench | 有关 Workbench 的指标 |
| \details\blockchain | 有关区块链的详细日志 |
| \details\workbench | 有关 Workbench 的详细日志 |

摘要文件提供了应用程序总体状态的快照以及应用程序的运行状况。 此摘要提供建议的操作，突出显示了顶级错误以及正在运行的服务的相关元数据。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 体系结构](blockchain-workbench-architecture.md)