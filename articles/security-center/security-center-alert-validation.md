---
title: Azure 安全中心的警报验证（EICAR 测试文件） |Microsoft Docs
description: 本文档介绍了如何在 Azure 安全中心验证安全警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 10ea15168d61d5e73aff976ef641e07b6327dbca
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604566"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure 安全中心的警报验证（EICAR 测试文件）
本文档介绍如何验证系统是否已针对 Azure 安全中心警报进行了适当的配置。

## <a name="what-are-security-alerts"></a>什么是安全警报？
警报是安全中心检测到对资源的威胁时生成的通知。 它划分优先级并列出警报，以及快速调查问题所需的信息。 安全中心还提供了有关如何修正攻击的建议。
有关详细信息，请参阅[安全中心中的安全警报](security-center-alerts-overview.md)和[管理和响应安全警报](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>警报验证

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## 验证 Windows Vm 上的警报<a name="validate-windows"></a>

在计算机上安装安全中心代理后，请在想要成为警报的受攻击资源的计算机上执行以下步骤：

1. 将可执行文件（例如**calc**）复制到计算机的桌面或其他便利目录，并将其重命名为**ASC_AlertTest_662jfi039N。**
1. 打开命令提示符并使用参数（只是伪参数名）执行此文件，例如： ```ASC_AlertTest_662jfi039N.exe -foo```
1. 等待 5 到 10 分钟，然后打开安全中心警报。 应显示类似于以下[示例](#alert-validate)的警报：

> [!NOTE]
> 查看 Windows 的此测试警报时，请确保**已启用字段参数审核**为**true**。 如果为**false**，则需要启用命令行参数审核。 若要启用它，请使用以下命令：
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## 验证 Linux Vm 上的警报<a name="validate-linux"></a>

在计算机上安装安全中心代理后，请在想要成为警报的受攻击资源的计算机上执行以下步骤：
1. 将可执行文件复制到一个方便的位置，并将其重命名为 **./asc_alerttest_662jfi039n**，例如：

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. 打开命令提示符并执行以下文件：

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 等待 5 到 10 分钟，然后打开安全中心警报。 应显示类似于以下[示例](#alert-validate)的警报：

### 警报示例<a name="alert-validate"></a>

![警报验证示例](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## 验证 Kubernetes 上的警报<a name="validate-kubernetes"></a>

如果使用集成 Azure Kubernetes 服务的安全中心预览功能，请运行以下 kubectl 命令来测试你的警报是否正常工作：

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

有关 Azure Kubernetes 服务和 Azure 安全中心集成的详细信息，请参阅[此文](azure-kubernetes-service-integration.md)。

## <a name="next-steps"></a>后续步骤
本文介绍了警报验证过程。 熟悉该验证以后，请尝试阅读以下文章：

* [管理和响应 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)-了解如何管理警报并响应安全中心的安全事件。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)-了解不同类型的安全警报。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)-了解如何对安全中心的常见问题进行故障排除。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/)-查找有关 Azure 安全性和符合性的博客文章。
