---
title: 使用 Node.js 将 X.509 设备注册到 Azure 设备预配服务 | Microsoft Docs
description: Azure 快速入门 - 使用 Node.js 服务 SDK 将 X.509 设备注册到 Azure IoT 中心设备预配服务
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 12/21/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0d255cd3a076b892f0732766e0084f78a7859aa4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>使用 Node.js 服务 SDK 将 X.509 设备注册到 IoT 中心设备预配服务

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]


以下步骤演示了如何使用 [Node.js 服务 SDK](https://github.com/Azure/azure-iot-sdk-node) 和 Node.js 示例，以编程方式为中间的或根的 CA X.509 证书创建注册组。 虽然这些步骤在 Windows 和 Linux 计算机上均适用，但本文使用 Windows 开发计算机。
 

## <a name="prerequisites"></a>先决条件

- 请确保完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。 

 
- 请确保已在计算机上安装 [Node.js v4.0 或更高版本](https://nodejs.org)。


- 需要一个 .pem 文件，其中包含的中间的或根的 CA X.509 证书已上传到预配服务并通过该服务进行验证。 **Azure IoT c SDK** 包含的工具有助于创建 X.509 证书链、从该链上传根证书或中间证书，以及通过服务执行所有权证明，对证书进行验证。 若要使用该工具，请在计算机上克隆 [Azure IoT c SDK](https://github.com/Azure/azure-iot-sdk-c) 并按 [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) 中的步骤操作。

## <a name="create-the-enrollment-group-sample"></a>创建注册组示例 

 
1. 在工作文件夹的命令窗口中，运行以下命令：
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. 使用文本编辑器，在工作文件夹中创建 **create_enrollment_group.js** 文件。 将以下代码添加到文件并进行保存：

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ````

## <a name="run-the-enrollment-group-sample"></a>运行注册组示例
 
1. 若要运行示例，需要适用于预配服务的连接字符串。 
    1. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开设备预配服务。 
    2. 单击“共享访问策略”，然后单击需要用来打开其属性的访问策略。 在“访问策略”窗口中，复制并记下主密钥连接字符串。 

    ![从门户获取预配服务连接字符串](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. 如[先决条件](#prerequisites)中所述，还需要一个 .pem 文件，其中包含的 X.509 中间或根 CA 证书此前已上传并通过预配服务进行验证。 若要查看证书是否已上传并验证，请在 Azure 门户的设备预配服务摘要页中单击“证书”。 找到要用于组注册的证书，确保其状态值为“已验证”。

    ![门户中的已验证证书](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. 若要为证书创建注册组，请运行以下命令（包括命令参数的引号）：
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. 成功创建以后，命令窗口会显示新的注册组的属性。

    ![命令输出中的注册属性](./media/quick-enroll-device-x509-node/sample-output.png) 

4. 验证注册组是否已创建。 在 Azure 门户的设备预配服务摘要边栏选项卡上，选择“管理注册”。 选择“注册组”选项卡，验证新注册项 (*first*) 是否存在。

    ![门户中的注册属性](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>清理资源
如果打算学习 Node.js 服务示例，请勿清除本快速入门中创建的资源。 如果不打算继续学习，请通过以下步骤删除通过本快速入门创建的所有 Azure 资源。
 
1. 关闭计算机上的 Node.js 示例输出窗口。
2. 在 Azure 门户中导航到设备预配服务，单击“管理注册”，然后选择“注册组”选项卡。选择通过本快速入门创建的注册项的“注册 ID”，然后单击边栏选项卡顶部的“删除”按钮。  
3. 在 Azure 门户的设备预配服务中单击“证书”，然后单击为本快速入门上传的证书，再单击“证书详细信息”窗口顶部的“删除”按钮。  
 
## <a name="next-steps"></a>后续步骤
本快速入门介绍了如何使用 Azure IoT 中心设备预配服务为 X.509 中间或根 CA 证书创建组注册。 若要深入了解设备预配，请继续学习本教程有关如何在 Azure 门户中进行设备预配服务设置的内容。 
 
> [!div class="nextstepaction"]
> [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)