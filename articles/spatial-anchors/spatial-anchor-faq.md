---
title: 有关 Azure 空间定位点的常见问题解答 | Microsoft Docs
description: Azure 空间定位点是一个托管云服务和开发人员平台，可跨 HoloLens、iOS 和 Android 设备实现跨设备、多用户的混合现实体验。 这些常见问题解答从技术角度解决有关服务的问题。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 06936a196813dacfc1fc9d02945bee4119b7eea8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927498"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>有关 Azure 空间定位点的常见问题解答

Azure 空间定位点是一个托管云服务和开发人员平台，可跨 HoloLens、iOS 和 Android 设备实现可感知空间的多用户混合现实体验。

有关详细信息，请参阅 [Azure 空间定位点概述](overview.md)。

## <a name="azure-spatial-anchors-product-faqs"></a>Azure 空间定位点产品常见问题解答

**问：Azure 空间定位点支持哪些设备？**

**答:** Azure 空间定位点使开发人员可以在 HoloLens 上、在具有 ARKit 支持的 iOS 设备上以及在具有 ARCore 支持的 Android 上构建应用；对于 iOS 和 Android，这包括手机和平板电脑。

**问：是否必须连接到云才能使用 Azure 空间定位点？**

**答:** Azure 空间定位点当前需要到 Internet 的网络连接。 欢迎在我们的[反馈网站](https://feedback.azure.com/forums/919252-azure-spatial-anchors)上提出意见。

**问：Azure 空间定位点有哪些连接要求？**

**答:** Azure 空间定位点使用 Wi-Fi 和移动宽带连接。

**问：Azure 空间定位点如何准确地查找定位点？**

**答:** 许多因素会影响查找定位点的准确性 - 光照条件、环境中的物体，甚至是定位点所处的表面。 若要确定准确性是否满足需求，请在可代表你打算使用它们的环境中尝试定位点。 如果遇到准确性不满足需求的环境，请参阅 [ Azure 空间定位点中的日志记录和诊断](./concepts/logging-diagnostics.md)。

**问：创建并查找定位点需要多长时间？**

**答:** 创建并查找定位点所需的时间取决于许多因素 - 网络连接、设备的处理和负载以及特定环境。 我们的客户在许多行业（包括制造、零售和游戏）中构建应用程序，这表明该服务可为其方案实现出色的用户体验。

## <a name="privacy-faq"></a>隐私常见问题解答

**问：当应用程序将空间定位点放置在某处时，是否所有应用都有权访问它？**

**答:** 定位点按 Azure 帐户进行隔离。 只有你向帐户授予了访问权限的应用才能访问帐户中的定位点。

**问：使用 Azure 空间定位点时，会在服务中传输和存储有关环境的哪些信息？是否会传输和存储环境的图片？**

**答**：创建或查找定位点时，环境的图片会在设备上处理为派生格式。 此派生格式会传输到服务并存储在其中。

为了提供透明度，下面是环境和派生稀疏点云的图像。 点云显示在服务中传输和存储的环境的几何表示形式。 对于稀疏点云中的每个点，我们传输并存储该点的视觉特征哈希。 哈希派生自（但不包含）任何像素数据。

Azure 空间定位点遵守 [Azure 服务协议条款](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)和 [Microsoft 隐私声明](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409)。

![环境及其派生稀疏点云](./media/sparse-point-cloud.png)
*图 1：环境及其派生稀疏点云*


**问：我是否有方法可以向 Microsoft 发送诊断信息？**

**答**：是的。 Azure 空间定位点有一种诊断模式，开发人员可以通过 Azure 空间定位点 API 选择加入该模式。 这很有用，例如在遇到无法以可预测方式创建并查找定位点的环境时。 我们可能会询问是否可以提交包含可帮助我们进行调试的诊断报告。 有关详细信息，请参阅 [Azure 空间定位点中的日志记录和诊断](./concepts/logging-diagnostics.md)。

## <a name="availability-and-pricing-faqs"></a>可用性和定价常见问题解答

**问：是否提供 SLA？**

**答:** 作为 Azure 服务的标准，我们的目标是高于 99.9% 的可用性。 请注意，Azure 空间定位点当前处于预览版状态，因此会应用[预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

**问：是否可以将使用 Azure 空间定位点的应用发布到应用商店？是否可以将 Azure 空间定位点用于任务关键型生产方案？**

**答:** Azure 空间定位点当前处于预览版状态，在此期间我们会邀请你开发应用、针对产品[提供反馈](https://feedback.azure.com/forums/919252-azure-spatial-anchors)以及规划生产部署。

公开上市 (GA) 日期即将宣布。

**问：是否实施了任何限制？**

**答**：是的，我们实施了限制。  我们预计你不会在典型应用程序开发和测试中达到这些限制。 对于生产部署，我们已准备好支持客户的大规模要求。 请[联系我们](mailto:azuremrs@microsoft.com)以进行讨论。 在此预览版阶段，我们尚未发布分层和定价结构，但我们预计会很快发布。

**问：Azure 空间定位点在哪些区域可用？**

**答:** 可以在 Azure 美国东部 2 区域中立即创建 Azure 空间定位点帐户。 这意味着为此服务提供支持的计算和存储处于此区域中。 也就是说，对于客户端所处的位置没有限制。 将来，我们会将服务的区域可用性扩展到所有主要 Azure 区域。

**问：是否对 Azure 空间定位点收费？是否会永远收费？**

**答:** 可以在我们的[定价页面](https://azure.microsoft.com/pricing/details/spatial-anchors/)上找到有关预览版期间的定价的详细信息。

## <a name="technical-faqs"></a>技术常见问题解答

**问：Azure 空间定位点如何工作？**

**答:** Azure 空间定位点依赖于混合现实/增强现实跟踪器。 这些跟踪器使用照相机感知环境，并随着设备在空间中移动，采用 6 自由度 (6DoF) 来跟踪设备。

Azure 空间定位点将 6DoF 跟踪器作为构造块，使你可以将真实环境中的特定兴趣点指定为“定位”点。 例如，可以使用定位点来呈现现实世界中特定位置处的内容。

创建定位点时，客户端 SDK 会捕获该点周围的环境信息并将其传输给服务。 如果另一台设备在该相同空间中查找定位点，则类似数据会传输给服务。 该数据会与以前存储的环境数据进行匹配。 定位点相对于设备的位置随后会发送回去以便在应用程序中使用。

**问：Azure 空间定位点如何在 iOS 和 Android 上与 ARKit 和 ARCore 集成？**

**答:** Azure 空间定位点利用 ARKit 和 ARCore 的本机跟踪功能。 此外，我们适用于 iOS 和 Android 的 SDK 可提供各种功能，如将定位点持久保存在托管云服务中，以及允许应用只需连接到服务便可再次查找这些定位点。

**问：Azure 空间定位点如何与 HoloLens 集成？**

**答:** Azure 空间定位点利用 HoloLens 的本机跟踪功能。 我们提供了 Azure 空间定位点 SDK 以用于在 HoloLens 上构建应用。 该 SDK 与本机 HoloLens 功能集成，并提供了附加功能。 这些功能包括允许应用开发人员将定位点持久保存在托管云服务中，以及允许应用通过连接到服务来再次查找这些定位点。

**问：Azure 空间定位点支持哪些平台和语言？**

**答:** 开发人员可以使用熟悉的工具和框架为其设备构建带 Azure 空间定位点的应用：

- 跨 HoloLens、iOS 和 Android 的 Unity
- iOS 上的 Swift 或 Objective-C
- Android 上的 Java 或 Android NDK
- C++/WinRT on HoloLens

[从此处开始进行开发](index.yml)。

**问：它是否适用于 Unreal？**

**答:** 将来会考虑支持 Unreal。

**问：Azure 空间定位点使用哪些端口和协议？**

**答:** Azure 空间定位点使用加密协议通过 TCP 端口 443 进行通信。 对于身份验证，它使用 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)，后者使用 HTTPS 通过端口 443 进行通信。
