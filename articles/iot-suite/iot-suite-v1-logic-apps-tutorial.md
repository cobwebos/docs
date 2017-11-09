---
title: "Azure IoT 套件和逻辑应用 | Microsoft Docs"
description: "有关如何在业务流程中将逻辑应用挂接到 Azure IoT 套件的教程。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: f4457b44c97fadc58406430fc0f31b3e0bac6682
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>教程：将逻辑应用连接到 Azure IoT 套件远程监视预配置解决方案
[Microsoft Azure IoT 套件][lnk-internetofthings]远程监视预配置解决方案以一套端到端功能集演示 IoT 解决方案，是快速入门的好工具。 本教程引导将逻辑应用连接到 Microsoft Azure IoT 套件远程监视预配置解决方案。 下述步骤演示了如何将 IoT 解决方案连接到业务流程，更进一步地利用该方案。

*如果要查找有关如何设置远程监视预配置解决方案的演练，请参阅[教程：IoT 预配置解决方案入门][lnk-getstarted]。*

开始本教程之前，必须：

* 在 Azure 订阅中预配远程监视预配置解决方案。
* 创建 SendGrid 帐户，以便发送可触发业务流程的电子邮件。 可以在 [SendGrid](https://sendgrid.com/) 中单击“**免费试用**”来注册一个免费试用帐户。 注册免费试用帐户后，需要在 SendGrid 中创建一个用于授权发送邮件的 [API 密钥](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html)。 本教程稍后需要此 API 密钥。

若要完成本教程，需要使用 Visual Studio 2015 或 Visual Studio 2017 修改预配置解决方案后端中的操作。

假设已经设置了远程监视预配置解决方案，请在 [Azure 门户][lnk-azureportal]中导航到该解决方案的资源组。 资源组的名称与在预配远程监视解决方案时选择的解决方案名称相同。 资源组中会显示解决方案的所有预配 Azure 资源，Azure 经典门户中提供的 Azure Active Directory 应用程序除外。 以下屏幕截图显示了远程监视预配置解决方案的“**资源组**”边栏选项卡示例：

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

要开始，请将逻辑应用设置为使用预配置的解决方案。

## <a name="set-up-the-logic-app"></a>设置逻辑应用
1. 在 Azure 门户中，单击资源组边栏选项卡顶部的“**添加**”。
2. 搜索“**逻辑应用**”，选择它，并单击“**创建**”。
3. 填写“**名称**”，并使用预配远程监视解决方案时使用的相同“**订阅**”和“**资源组**”。 单击“**创建**”。
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. 部署完成后，会看到逻辑应用列为资源组中的资源。
5. 单击逻辑应用以导航到“逻辑应用”边栏选项卡，选择“**空白逻辑应用**”模板以打开 **Logic Apps 设计器**。
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. 选择“**请求**”。 此操作会将带特定 JSON 格式化有效负载的传入 HTTP 请求指定为触发器。
7. 将以下代码贴到请求正文 JSON 架构中：
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > 可在保存逻辑应用后复制 HTTP post 的 URL，但必须先添加一个操作。
   > 
   > 
8. 单击手动触发器下的“**+ 新建步骤**”。 然后单击“添加操作”。
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. 搜索“**SendGrid - 发送电子邮件**”并单击它。
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. 输入连接名称，例如 **SendGridConnection**，输入设置 SendGrid 帐户时创建的 **SendGrid API 密钥**，并单击“**创建**”。
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. 在“**发件人**”和“**收件人**”字段中添加自己的电子邮件地址。 将“**远程监视警报 [DeviceId]**”添加到“**主题**”字段。 在“**电子邮件正文**”字段中，添加“**设备 [DeviceId] 已报告具有值 [measuredValue] 的 [measurementName]**”。 可以通过在**可以从前面的步骤插入数据**部分中单击，添加 **[DeviceId]**、**[measurementName]** 和 **[measuredValue]**。
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. 单击顶部菜单中的“**保存**”。
13. 单击“**请求**”触发器和“**指向此 URL 的 Http Post**”值的副本。 本教程稍后需要此 URL。

> [!NOTE]
> 通过逻辑应用，可以运行[多个不同类型的操作][lnk-logic-apps-actions]，包括 Office 365 中的操作。 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>设置 EventProcessor Web 作业
在本部分中，会将预配置解决方案连接到创建的逻辑应用上。 若要完成此任务，需添加 URL 以触发逻辑应用在设备感应器值超过阙值时执行操作。

1. 使用 git 客户端克隆最新版的 [azure-iot-remote-monitoring github 存储库][lnk-rmgithub]。 例如：
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. 在 Visual Studio 中，从存储库的本地副本打开 **RemoteMonitoring.sln**。
3. 打开 **Infrastructure\\Repository** 文件夹中的 **ActionRepository.cs** 文件。
4. 使用逻辑应用中记下的**指向此 URL 的 Http Post** 更新 **actionIds** 字典，如下所示：
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. 在解决方案中保存所做的更改并退出 Visual Studio。

## <a name="deploy-from-the-command-line"></a>从命令行部署
在本部分中，会部署已更新的远程监视解决方案，以替换当前正在 Azure 中运行的版本。

1. 遵循[开发设置][lnk-devsetup]说明来设置环境，以便为部署做好准备。
2. 若要在本地部署，请遵循[本地部署][lnk-localdeploy]说明。
3. 若要部署到云并更新现有的云部署，请遵循[云部署][lnk-clouddeploy]说明。 使用原始部署的名称作为部署名称。 例如，如果原始部署称为 **demologicapp**，请使用以下命令：
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   生成脚本运行时，请务必使用预配解决方案时所用的相同 Azure 帐户、订阅、区域和 Active Directory 实例。

## <a name="see-your-logic-app-in-action"></a>了解逻辑应用的工作动态
预配解决方案时，远程监视预配置解决方案默认设置有两个规则。 这两个规则位于 **SampleDevice001** 设备上：

* 温度 > 38.00
* 湿度 > 48.00

温度规则触发**引发警报**操作，湿度规则触发 **SendMessage** 操作。 假设为这两个操作的 **ActionRepository** 类使用了相同的 URL，则针对任一规则触发逻辑应用。 这两个规则使用 SendGrid 将电子邮件发送至“**收件人**”地址，其中包含警报的详细信息。

> [!NOTE]
> 逻辑应用继续在每次达到阙值时触发。 为避免不必要的电子邮件，可在解决方案门户中禁用规则或在 [Azure 门户][lnk-azureportal]中禁用逻辑应用。
> 
> 

除了接收电子邮件以外，还可以查看逻辑应用在门户中的运行情况：

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>后续步骤
现在，已使用逻辑应用将预配置解决方案连接到业务流程，接下来可以详细了解自定义预配置解决方案的选项：

* [配合使用动态遥测和远程监视预配置解决方案][lnk-dynamic]
* [远程监视预配置解决方案中的设备信息元数据][lnk-devinfo]

[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
