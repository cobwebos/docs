---
title: 部署远程监视解决方案 - Azure | Microsoft Docs
description: 本教程演示如何从 azureiotsuite.com 预配远程监视解决方案加速器。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: eb78ce91755c43f1c6fedf62a70238df911b940f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33773626"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>部署远程监视解决方案加速器

本教程演示如何预配远程监视解决方案加速器。 从 azureiotsuite.com 部署解决方案。还可以使用 CLI 部署解决方案，若要了解此选项，请参阅[从命令行部署解决方案加速器](iot-suite-remote-monitoring-deploy-cli.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 配置解决方案加速器
> * 部署解决方案加速器
> * 登录到解决方案加速器

## <a name="prerequisites"></a>先决条件

需要有效的 Azure 订阅才能完成此教程。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="deploy-the-solution-accelerator"></a>部署解决方案加速器

将解决方案加速器部署到 Azure 订阅之前，必须选择一些配置选项：

1. 使用 Azure 帐户凭据登录到 [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators)。

1. 单击“远程监视”磁贴上的“立即试用”。

    ![选择远程监视](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. 在“创建远程监视解决方案”页上，为远程监视解决方案加速器输入**解决方案名称**。

1. 选择“基本”或“标准”部署。 如果部署解决方案的目的是要了解其工作原理或运行演示，请选择“基本”选项，使成本降到最低。

1. 选择 **Java** 或 **.NET** 作为语言。 所有微服务都可用作 Java 或 .NET 实现。

1. 有关配置选择的详细信息，请查看“解决方案详细信息”面板。

1. 选择要用于预配解决方案的“订阅”和“区域”。

1. 单击“创建解决方案”  开始预配过程。 此过程通常需要数分钟的运行时间：

    ![远程监视解决方案详细信息](media/iot-suite-remote-monitoring-deploy/createform.png)

有关故障排除的信息，请参阅 GitHub 存储库中的[部署失败时该如何操作](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails)。

## <a name="sign-in-to-the-solution-accelerator"></a>登录到解决方案加速器

预配过程完成后，可以登录到远程监视解决方案加速器。

1. 在“预配解决方案”页上，选择新的远程监视解决方案：

    ![选择新的解决方案](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. 可以在显示的面板中查看有关远程监视解决方案的信息。 选择“解决方案仪表板”连接到远程监视解决方案。

    > [!NOTE]
    > 使用完远程监视解决方案后，可以将其从此面板中删除。

    ![解决方案面板](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. 远程监视解决方案仪表板将显示在浏览器中。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 配置解决方案加速器
> * 部署解决方案加速器
> * 登录到解决方案加速器

现在已部署远程监视解决方案，下一步是[浏览解决方案仪表板的功能](./iot-suite-remote-monitoring-explore.md)。

<!-- Next tutorials in the sequence -->