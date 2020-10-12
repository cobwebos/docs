---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 9ac7966538102273b91d6b7f15b90e18ceedd421
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83779937"
---
还可以查看指标以监视设备的性能，并在某些情况下排除设备故障。

在 Azure 门户中执行以下步骤，为所选择的设备指标创建图表。

1. 在 Azure 门户中的资源处，转到“监视”>“指标”，然后选择“添加指标” 。

    ![添加指标](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. 将自动填充资源。  

    ![当前资源](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    若要指定其他资源，请选择该资源。 在“选择资源”边栏选项卡上，选择想查看其指标的订阅、资源组、资源类型和特定的资源，然后选择“应用” 。

    ![选择其他资源](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. 在下拉列表中选择用于监视设备的指标。 有关这些指标的完整列表，请参阅[设备上的指标](#metrics-on-your-device)。

4. 当在下拉列表中选择指标后，即可定义聚合。 聚合指的是在指定时间范围内聚合的实际值。 聚合值可以为平均值、最小值或最大值。 从平均值、最大值或最小值中选择聚合。

    ![查看图表](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. 如果所选指标有多个实例，则可以使用拆分选项。 选择“应用拆分”，然后选择要按其显示明细图表的值。

    ![应用拆分](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. 如果现在只想查看少数实例的明细图表，可以对数据进行筛选。 例如，在这种情况下，如果想查看两个连接到设备的网络接口的网络吞吐量，可以筛选这些接口。 选择“添加筛选器”，并指定要筛选的网络接口名称。

    ![添加筛选器](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. 还可以将图表固定至仪表板，以便访问。

    ![固定到仪表板](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. 若要将图表数据导出为 Excel 电子表格或者获取用于共享的图表链接，请在命令栏中选择共享选项。

    ![导出数据](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)
