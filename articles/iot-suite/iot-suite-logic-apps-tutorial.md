<properties
  pageTitle="Azure IoT 套件和 Logic Apps | Azure"
  description="有关如何在业务流程中将 Logic Apps 挂接到 Azure IoT 套件的教程。"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="08/16/2016"
  ms.author="araguila"
  wacn.date=""/>  

  
# 教程：将逻辑应用连接到 Azure IoT 套件远程监视预配置解决方案

[Azure IoT 套件][lnk-internetofthings]远程监视预配置解决方案以一套端到端功能集演示 IoT 解决方案，是快速入门的好工具。本教程引导你将逻辑应用连接到 Microsoft Azure IoT 套件远程监视预配置解决方案。下述步骤演示了如何将 IoT 解决方案连接到业务流程，更进一步地利用该方案。

_如果你要查找有关如何预配远程监视预配置解决方案的演练，请参阅 [Tutorial: Get started with the IoT preconfigured solutions][lnk-getstarted]（教程：IoT 预配置解决方案入门）。_

开始本教程之前，必须：

- 在 Azure 订阅中预配远程监视预配置解决方案。

- 创建 SendGrid 帐户，以便发送可触发业务流程的电子邮件。你可以在 [SendGrid](https://sendgrid.com/) 中单击“免费试用”来注册一个免费试用帐户。注册免费试用帐户后，需要在 SendGrid 中创建一个用于授权发送邮件的 [API 密钥](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html)。本教程稍后需要此 API 密钥。

假设预配了远程监视预配置解决方案，请在 [Azure 门户][lnk-azureportal]中导航到该解决方案的资源组。资源组的名称与你在预配远程监视解决方案时选择的解决方案名称相同。资源组中会显示解决方案的所有预配 Azure 资源，Azure 经典门户中提供的 Azure Active Directory 应用程序除外。以下屏幕截图显示了远程监视预配置解决方案的“资源组”边栏选项卡示例：

![](./media/iot-suite-logic-apps-tutorial/resourcegroup.png)  


若要开始，请将逻辑应用设置为使用预配置的解决方案。

## 设置逻辑应用

1. 在 Azure 门户中，单击资源组边栏选项卡顶部的“添加”。

2. 搜索“逻辑应用”，选择它，然后单击“创建”。

3. 填写“名称”，并使用预配远程监视解决方案时使用的相同“订阅”和“资源组”。单击“创建”。

    ![](./media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. 部署完成后，会看到逻辑应用列为资源组中的资源。

5. 单击逻辑应用以导航到该应用的边栏选项卡，然后选择“空白逻辑应用”模板来打开“逻辑应用设计器”。

    ![](./media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. 选择“请求”。此操作会将带特定 JSON 格式化有效负载的传入 HTTP 请求指定为触发器。

7. 将以下内容贴到请求正文 JSON 架构中：

    ```
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
    
    > [AZURE.NOTE] 可在保存逻辑应用后复制 HTTP post 的 URL，但必须先添加一个操作。

8. 单击手动触发器下的“+ 新建步骤”。然后单击“添加操作”。

    ![](./media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. 搜索“SendGrid -发送电子邮件”并单击它。

    ![](./media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. 输入连接名称（如 **SendGridConnection**），输入设置 SendGrid 帐户时创建的 **SendGrid API 密钥**，然后单击“创建”。

    ![](./media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. 将你拥有的电子邮件地址添加到“发件人”和“收件人”字段。将“远程监视警报 [DeviceId]”添加到“主题”字段。在“电子邮件正文”字段中添加“设备 [DeviceId] 已报告 [measurementName] 与 [measuredValue] 值”。 可以通过单击“你可以从前面的步骤插入数据”部分，来添加 **[DeviceId]**、**[measurementName]** 和 **[measuredValue]**。

    ![](./media/iot-suite-logic-apps-tutorial/sendgridaction.png)  


12. 单击顶部菜单中的“保存”。

13. 单击“请求”触发器，并复制“此 URL 的 Http Post”值。本教程稍后需要此 URL。

<!-- > [AZURE.NOTE] Logic Apps enable you to run [many different types of action][lnk-logic-apps-actions] including actions in Office 365. -->

## 设置 EventProcessor Web 作业

在本部分中，会将预配置解决方案连接到创建的逻辑应用上。若要完成此任务，需添加 URL 以触发逻辑应用在设备感应器值超过阙值时执行操作。

1. 使用 git 客户端复制最新版的 [azure-iot-remote-monitoring github 存储库][lnk-rmgithub]。例如：

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. 在 Visual Studio 中，从存储库的本地副本打开 __RemoteMonitoring.sln__。

3. 打开 **Infrastructure\\Repository** 文件夹中的 __ActionRepository.cs__ 文件。

4. 使用从逻辑应用中记下的“此 URL 的 Http Post”更新 **actionIds** 字典，如下所示：

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. 在解决方案中保存所做的更改并退出 Visual Studio。

## 从命令行部署

在本部分中，你将部署已更新的远程监视解决方案，以替换当前正在 Azure 中运行的版本。

1. 遵循[开发设置][lnk-devsetup]说明来设置环境，以便为部署做好准备。

2.  若要在本地部署，请遵循[本地部署][lnk-localdeploy]说明。

3.  若要部署到云并更新现有的云部署，请遵循[云部署][lnk-clouddeploy]说明。使用原始部署的名称作为部署名称。例如，如果原始部署名为 **demologicapp**，请使用以下命令：

    ``
    build.cmd cloud release demologicapp
    ``
    
    生成脚本运行时，请务必使用预配解决方案时所用的相同 Azure 帐户、订阅、区域和 Active Directory 实例。

## 了解逻辑应用的工作动态

预配解决方案时，远程监视预配置解决方案默认设置有两个规则。这两个规则位于 **SampleDevice001** 设备上：

* 温度 > 38.00
* 湿度 > 48.00

温度规则触发 **Raise Alarm** 操作，湿度规则触发 **SendMessage** 操作。假设 **ActionRepository** 类的两个操作使用了同一 URL，则任一规则都会触发逻辑应用。这两个规则均使用 SendGrid 向“收件人”地址发送带详细警报详细的电子邮件。

> [AZURE.NOTE] 逻辑应用继续在每次达到阙值时触发。为避免不必要的电子邮件，可在解决方案门户中禁用规则或在 [Azure 门户][lnk-azureportal]中禁用逻辑应用。

除了接收电子邮件以外，你还可以查看逻辑应用在门户中的运行情况：

![](./media/iot-suite-logic-apps-tutorial/logicapprun.png)

## 后续步骤

现在，你已使用逻辑应用将预配置解决方案连接到业务流程，接下来可以详细了解自定义预配置解决方案的选项：

- [配合使用动态遥测和远程监视预配置解决方案][lnk-dynamic]
- [远程监视预配置解决方案中的设备信息元数据][lnk-devinfo]

[lnk-dynamic]: /documentation/articles/iot-suite-dynamic-telemetry/
[lnk-devinfo]: /documentation/articles/iot-suite-remote-monitoring-device-info/

[lnk-internetofthings]: /documentation/services/iot-suite/
[lnk-getstarted]: /documentation/articles/iot-suite-getstarted-preconfigured-solutions/
[lnk-azureportal]: https://portal.azure.cn
[lnk-logic-apps-actions]: /documentation/articles/connectors/apis-list/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md

<!---HONumber=Mooncake_0926_2016-->