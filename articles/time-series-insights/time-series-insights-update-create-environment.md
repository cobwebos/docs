---
title: Azure 时序见解（预览版）教程 | Microsoft Docs
description: 了解 Azure 时序见解（预览版）
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: ed25d03f7c592476b9284790ac12f9954661a42b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872299"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Azure 时序见解（预览版）教程

本教程逐步讲解如何创建 Azure 时序见解 (TSI) 预览版环境，并在其中填充模拟设备的数据。 本教程介绍如何执行下列操作：

* 创建 TSI（预览版）环境。
* 将 TSI（预览版）环境连接到事件中心。
* 运行风力发电厂模拟以将数据流式传输到 TSI 预览版环境。
* 对数据进行基本的分析。
* 定义时序模型类型和层次结构，并将其与实例相关联。

## <a name="create-a-time-series-insights-preview-environment"></a>创建时序见解（预览版）环境

此部分介绍如何使用 [Azure 门户](https://portal.azure.com/)创建 Azure TSI（预览版）环境。

1. 使用订阅帐户登录到 Azure 门户
1. 选择左上角的“+ 创建资源”。
1. 依次选择“物联网”类别、“时序见解”。

  ![tutorial-one][1]

1. 在“时序见解环境”页上，填写必需的参数，然后单击“下一步: 事件源”

  ![tutorial-two][2]

1. 在“事件源”页上，填写必需的参数，然后单击“查看 + 创建” ****。

  ![tutorial-three][3]

1. 查看所有详细信息，单击“创建”，开始预配环境。

  ![tutorial-four][4]

1. 部署成功完成后，会收到通知。

  ![tutorial-five][5]

## <a name="send-events-to-your-tsi-environment"></a>将事件发送到 TSI 环境

在此部分，需使用风车设备模拟器通过事件中心将事件发送到 TSI 环境。

  1. 在 Azure 门户中导航到事件中心资源，然后将其连接到 TSI 环境。 了解[如何将资源连接到现有的事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

  1. 在“事件中心资源”页中转到“共享访问策略”，然后转到“RootManageSharedAccessKey”。 复制显示在此处的“连接字符串 - 主键”。

      ![tutorial-six][6]

  1. 转到[https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html)。 此 Web 应用模拟风车设备。
  1. 粘贴从“事件中心连接字符串”中的步骤 3 复制的连接字符串

      ![tutorial-seven][7]

  1. 单击“通过单击来启动”，将事件推送到事件中心。 此阶段会将名为 `instances.json` 的文件下载到计算机。 保存此文件，因为稍后会需要它。

  1. 返回事件中心。 此时会看到 hub.d 接收新事件

     ![tutorial-eight][8]

## <a name="analyze-data-in-your-environment"></a>在环境中分析数据

在此部分，需使用时序见解更新资源管理器对时序数据进行基本的分析。

  1. 导航到时序见解更新资源管理器，方法是单击 Azure 门户中资源页中的 URL。

      ![tutorial-nine][9]

  1. 在资源管理器中，单击“无父级实例”节点，查看环境中的所有时序实例。

     ![tutorial-ten][10]

  1. 在本教程中，我们将分析在前一天发送的数据。 为此，请单击“快速时间”，选择“过去 24 小时”选项。

     ![tutorial-eleven][11]

  1. 选择“Sensor_0”，然后选择“显示平均值”，以便将此时序实例发送的数据可视化。

     ![tutorial-twelve][12]

  1. 同样可以使用其他时序实例的数据来绘图，以便进行基本的分析。

     ![tutorial-thirteen][13]

## <a name="define-a-type--hierarchy"></a>定义类型和层次结构 

在此部分，需创作一个类型和一个层次结构，然后将它们与时序实例相关联。 阅读有关[时序模型](./time-series-insights-update-tsm.md)的详细内容。

  1. 在资源管理器中，单击应用栏中的“模型”选项卡。

     ![tutorial-fourteen][14]

  1. 在“类型”部分，单击“+ 添加”。 这样就可以创建新的时序模型类型。

     ![tutorial-fifteen][15]

  1. 在类型编辑器中，输入**名称**和**说明**，并为**平均**、**最小**和**最大**值创建变量，如下所示。 单击“创建”，保存类型。

     ![tutorial-sixteen][16]

     ![tutorial-seventeen][17]

  1. 在“层次结构”部分，单击“+ 添加”。 这样就可以创建新的时序模型层次结构。

     ![tutorial-eighteen][18]

  1. 在层次结构编辑器中，输入**名称**并添加层次结构级别，如下所示。 单击“创建”，保存层次结构。

     ![tutorial-nineteen][19]

     ![tutorial-twenty][20]

  1. 在“实例”部分，选择一个实例，然后单击“编辑”。 这样就可以将类型和层次结构与此实例相关联。

     ![tutorial-twenty-one][21]

  1. 在实例编辑器中，选择在步骤 3 和 5 中定义的类型和层次结构，如上所示。

     ![tutorial-twenty-two][22]

  1. 另外，若要一次性为所有实例执行该操作，可以编辑此前已下载的 `instances.json` 文件。 在此文件中，将所有 **typeId** 和 **hierarchyId** 字段替换为从上面的步骤 3 和 5 中获取的 ID。

  1. 在“实例”部分，单击“上传 JSON”，上传编辑的 `instances.json` 文件，如下所示。

     ![tutorial-twenty-three][23]

  1. 导航到“Analytics”选项卡，刷新浏览器。 此时会看到与上面定义的类型和层次结构相关联的所有实例。

     ![tutorial-twenty-four][24]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：  

* 创建 TSI（预览版）环境。
* 将 TSI（预览版）环境连接到事件中心。
* 运行风力发电厂模拟以将数据流式传输到 TSI（预览版）环境。
* 对数据进行基本的分析。
* 定义时序模型类型和层次结构，并将其与实例相关联。

了解如何创建自己的 TSI 更新环境后，就可以详细了解 TSI 中的重要概念了。

了解 TSI 存储配置：

> [!div class="nextstepaction"]
> [Azure TSI（预览版）存储和入口](./time-series-insights-update-storage-ingress.md)

详细了解时序模型：

> [!div class="nextstepaction"]
> [Azure TSI（预览版）数据建模](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png