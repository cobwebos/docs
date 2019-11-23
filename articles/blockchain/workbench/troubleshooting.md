---
title: Azure Blockchain Workbench 故障排除
description: How to troubleshoot an Azure Blockchain Workbench Preview application.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324304"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Azure Blockchain Workbench Preview troubleshooting

PowerShell 脚本用于协助开发人员进行调试或提供支持。 此脚本生成摘要并收集进行故障排除所需的详细日志。 收集的日志包括：

* Blockchain 网络，例如 Ethereum
* Blockchain Workbench 微服务
* Application Insights
* Azure Monitoring (Azure Monitor logs)

可以根据此信息确定后续步骤和问题的根本原因。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>故障排除脚本

PowerShell 故障排除脚本在 GitHub 上提供。 从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/blockchain/archive/master.zip)或克隆该示例。

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>运行脚本
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

运行 `collectBlockchainWorkbenchTroubleshooting.ps1` 脚本可收集日志并创建一个 ZIP 文件，该文件包含一个文件夹，其中有故障排除信息。 例如：

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
此脚本接受以下参数：

| 参数  | 描述 | 需要 |
|---------|---------|----|
| 订阅 ID | SubscriptionID，用于创建或定位所有资源。 | 是 |
| ResourceGroupName | Blockchain Workbench 部署时所在的 Azure 资源组的名称。 | 是 |
| OutputDirectory | 用于创建输出 .ZIP 文件的路径。 如果未指定，则默认为当前目录。 | No |
| LookbackHours | 拉取遥测数据时要使用的小时数。 默认值为 24 小时。 最大值为 90 小时 | No |
| OmsSubscriptionId | The subscription ID where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| No |
| OmsResourceGroup |The resource group where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| No |
| OmsWorkspaceName | Log Analytics 工作区名称。 Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group | No |

## <a name="what-is-collected"></a>收集什么内容？

输出 ZIP 文件包含以下文件夹结构：

| 文件夹或文件 | 描述  |
|---------|---------|
| \Summary.txt | 系统摘要 |
| \Metrics\blockchain | 有关区块链的指标 |
| \Metrics\Workbench | 有关 Workbench 的指标 |
| \Details\Blockchain | 有关区块链的详细日志 |
| \Details\Workbench | 有关 Workbench 的详细日志 |

摘要文件提供了应用程序总体状态的快照以及应用程序的运行状况。 此摘要提供建议的操作，突出显示了顶级错误以及正在运行的服务的相关元数据。

**Metrics** 文件夹包含各种系统组件随时间变化的指标。 例如，输出文件 `\Details\Workbench\apiMetrics.txt` 包含不同响应代码的摘要，以及整个收集期间的响应时间。 **Details** 文件夹包含用于解决 Workbench 或底层区块链网络特定问题的详细日志。 例如，`\Details\Workbench\Exceptions.csv` 包含系统中最近发生的异常列表，这对于解决智能合同或与区块链交互的错误很有用。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench Application Insights 故障排除指南](https://aka.ms/workbenchtroubleshooting)
