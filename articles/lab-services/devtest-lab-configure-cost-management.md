---
title: Azure DevTest Labs에서 월간 예상 랩 비용 추세 보기
description: 本文介绍了如何在 Azure 开发测试实验室中跟踪实验室（每月预估成本趋势图表）的成本。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721721"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>跟踪与 Azure 开发测试实验室中的实验室相关的成本
本文提供了有关如何跟踪实验室成本的信息。 其中介绍了如何查看实验室当前日历月的估计成本 trent。 本文还介绍了如何在实验室中查看每个资源的每月到目前的成本。

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>查看每月估计实验室成本趋势 
在本部分中，将了解如何使用 "**每月估计成本趋势**" 图表来查看当前日历月的估计成本和当前日历月的预计月结束成本。 你还将了解如何通过设置支出目标和阈值来管理实验室成本，达到该限制时，触发开发测试实验室向你报告结果。

월간 예상 비용 추세 차트를 보려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 랩을 선택합니다.  
4. 在左侧菜单中选择 "**配置和策略**"。  
4. 在左侧菜单的 "**成本跟踪**" 部分中选择 "**成本趋势**"。 以下屏幕截图显示了成本图表的示例。 
   
    ![비용 차트](./media/devtest-lab-configure-cost-management/graph.png)

    **예상 비용** 값은 현재 월의 현재까지의 예상 비용입니다. **예측 비용** 은 이번 달 전체의 예상 비용으로, 이전 5일 동안의 랩 비용을 사용해서 계산합니다.

    금액은 다음 정수로 올림됩니다. 예: 

   * 5.01은 6으로 올림됩니다. 
   * 5.50은 6으로 올림됩니다.
   * 5.99는 6으로 올림됩니다.

     위의 차트에 언급되어 있듯이, 기본적으로 차트에 보이는 비용은 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 제안 요율을 사용하여 *예상된* 비용입니다. [랩의 비용 목표를 관리](#managing-cost-targets-for-your-lab)하여 차트에 표시되는 지출 목표를 설정할 수도 있습니다.

     成本计算中*不*包括以下成本：

   * Azure DevTest Labs에서는 [Azure 청구 API](../cost-management-billing/manage/usage-rate-card-overview.md) 를 사용하여 랩 비용을 계산하며 CSP 또는 Dreamspark 구독을 지원하지 않으므로 이러한 구독 방식이 현재 지원되지 않습니다.
   * 사용자에 대한 제안 요율. 현재는 Microsoft 또는 Microsoft 파트너와 협상한 제안 요율(구독 아래에 표시됨)을 사용할 수 없습니다. 오직 종량제 요율만 사용됩니다.
   * 사용자 세금
   * 사용자 할인
   * 사용자의 청구 통화. 현재는, 랩 비용이 USD 통화로만 표시됩니다.

### <a name="managing-cost-targets-for-your-lab"></a>랩의 비용 목표 관리
DevTest Labs에서 지출 목표를 설정하고 이후에 월별 예상 비용 추세 차트에서 지출 목표를 살펴보면 랩의 비용을 보다 효율적으로 관리할 수 있습니다. 또한 지정된 지출 목표 또는 임계값에 도달하면 DevTest Labs에서 알림을 보냅니다. 

1. 在 "**成本趋势**" 页上，选择 "**管理目标**"。

    ![목표 관리 단추](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. 在 "**管理目标**" 页上，指定支出目标和阈值。 또한 선택한 각 임계값을 비용 추세 차트에 보고할 것인지 아니면 웹후크 알림을 통해 보고할 것인지 선택할 수 있습니다.

    ![목표 관리 창](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 비용 목표를 추적할 기간을 선택합니다.
      - **월별**: 비용 목표를 월 단위로 추적합니다.
      - 已**修复**：为在开始和结束日期中指定的日期范围跟踪成本目标。 通常，这些值表示项目计划运行的时间。
   - **목표 비용**을 지정합니다. 例如，你计划在定义的时间段内花费在此实验室中的量。
   - 지정된 **비용 목표**의 최대 125%까지 25% 단위로 보고서를 받을 수 있도록 임계값을 사용하거나 사용하지 않도록 선택합니다.
      - **알림**: 이 임계값에 도달하면 지정한 웹후크 URL을 통해 알림이 제공됩니다.
      - **在图表上绘制**：满足此阈值时，结果将绘制在你可以查看的 "成本趋势" 图上，如查看每月估计成本趋势图表中所述。
   - 임계값에 도달하면 **알림**을 받기로 선택하는 경우 웹후크 URL을 지정해야 합니다. 비용 통합 영역에서 **통합을 추가하려면 여기를 클릭하세요.** 를 선택합니다. 在 "配置通知" 窗格中输入**WEBHOOK URL** ，然后选择 **"确定"** 。

       ![알림 창 구성](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - **알림**을 지정하는 경우 웹후크 URL을 정의해야 합니다.
     - 마찬가지로, 웹후크 URL을 정의하는 경우 비용 임계값 창에서 **알림**을 **켜기**로 설정해야 합니다.
     - 여기에 입력하기 전에 웹후크를 만들어야 합니다.  

       웹후크에 대한 자세한 내용은 [웹후크 또는 API Azure Function 만들기](../azure-functions/functions-create-a-web-hook-or-api-function.md)를 참조하세요. 

## <a name="view-cost-by-resource"></a>按资源查看成本 
使用实验室中的每月成本趋势功能，你可以查看当前日历月所用的空间。 它还会根据您在过去7天内的支出，显示到月末结束之前的计划。 为了帮助你了解实验室中的支出在提前达到阈值的原因，你可以使用**按资源**划分的成本功能，该功能可显示表中**每个资源**的本月到目前的成本。

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 在左侧菜单中选择 "**配置和策略**"。
5. 在左侧菜单的 "**成本跟踪**" 部分中选择 "**按资源成本**"。 你将看到与实验室关联的每个资源相关的成本。 

    ![리소스별 비용](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

此功能可帮助您轻松地识别成本最高的资源，以便您可以采取措施来减少实验室支出。 例如，VM 的成本基于 VM 的大小。 VM 的大小越大，开销就越大。 你可以轻松地找到 VM 和所有者的大小，以便可以与 VM 所有者对话，了解为何需要此类 VM 大小，以及是否有可能降低大小。

通过[自动关闭策略](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)，可以在一天中的特定时间关闭实验室 vm，从而降低成本。 不过，实验室用户可以选择退出关闭策略，这会增加运行 VM 的成本。 可以在表中选择一个 VM，以查看其是否已选择退出自动关闭策略。 如果是这种情况，则可以与 VM 所有者联系，以找出 VM 选择超出策略的原因。
 
## <a name="next-steps"></a>다음 단계
다음으로 수행해 볼 작업은 아래와 같습니다.

* [랩 정책 정의](devtest-lab-set-lab-policy.md) – 랩 및 해당 VM의 사용 방식을 관리하는 데 사용되는 다양한 정책을 설정하는 방법을 알아봅니다. 
* [사용자 지정 이미지 만들기](devtest-lab-create-template.md) - VM을 만들 때 사용자 지정 이미지 또는 Marketplace 이미지 중에서 기본 이미지를 지정할 수 있습니다. 이 문서에는 VHD 파일에서 사용자 지정 이미지를 만드는 방법이 나와 있습니다.
* [마켓플레이스 이미지 구성](devtest-lab-configure-marketplace-images.md) - DevTest Labs에서 Azure Marketplace 이미지에 기반하여 VM을 만들 수 있습니다. 이 문서에서는 랩에서 VM을 만들 때 사용할 수 있는 Azure Marketplace 이미지(있는 경우)를 지정하는 방법을 보여 줍니다.
* [랩에서 VM 만들기](devtest-lab-add-vm.md) - 기본 이미지(사용자 지정 또는 Marketplace 이미지)에서 VM을 만드는 방법 및 VM에서 아티팩트 작업 방법에 대해 설명합니다.

