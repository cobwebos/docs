---
title: 将模型从 Azure 机器学习服务部署到 IoT Edge 设备 | Microsoft Docs
description: 了解如何将模型从 Azure 机器学习服务部署到 Azure IoT Edge 设备。 将模型部署到 Edge 设备后，可以在设备上对数据进行评分，无需将其发送到云并等待响应。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 7d706cf71761496fd740c729224ee4331eeb2911
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091617"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>准备好在 IoT Edge 上部署模型

本文档介绍如何使用 Azure 机器学习服务准备训练模型以部署到 Azure IoT Edge 设备。

Azure IoT Edge 设备是运行 Azure IoT Edge 运行时的基于 Linux 或 Windows 的设备。 机器学习模型可以作为 IoT Edge 模块部署到这些设备。 将模型部署到 IoT Edge 设备允许设备直接使用模型，而不必将数据发送到云进行处理。 你可以获得更快的响应时间，执行更少的数据传输。

在将模型部署到 Edge 设备之前，请使用本文档中的步骤准备模型和设备。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* Azure 机器学习服务工作区。 要创建一个工作区，请使用[开始使用 Azure 机器学习服务](quickstart-get-started.md)文档中的步骤。

* 开发环境。 有关详细信息，请参阅[如何配置开发环境](how-to-configure-environment.md)文档。

* Azure 订阅中的 [Azure IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)。 

* 定型的模型。 有关如何训练模型的示例，请参阅[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)文档。 [在适用于 Azure IoT Edge GitHub 存储库的 AI 工具包](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)上提供了预训练模型。

## <a name="prepare-the-iot-device"></a>准备 IoT 设备

若要了解如何注册设备并安装 IoT 运行时，请按照[快速入门：将第一个 IoT Edge 模块部署到 Linux x64 设备](../../iot-edge/quickstart-linux.md)文档中的步骤执行操作。

## <a name="register-the-model"></a>注册模型

Azure IoT Edge 模块基于容器映像。 要将模型部署到 IoT Edge 设备，请使用以下步骤在 Azure 机器学习服务工作区上注册模型并创建 Docker 映像。 

1. 初始化工作区并加载 config.json 文件：

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the config file info.
    ws  = Workspace.from_config()
    ```    

1. 将模型注册到工作区。 将默认文本替换为你的模型路径、名称、标记和说明：

    > [!IMPORTANT]
    > 如果使用了 Azure 机器学习来训练模型，它可能已在工作区中注册。 如果是这样，请跳过此步骤。 若要查看注册了此工作区的模型列表，请使用 `Model.list(ws)`。

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. 检索已注册的模型： 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>创建 Docker 映像

1. 创建名为 `score.py` 的评分脚本。 此文件用于运行映像内的模型。 它必须包括以下函数：

    * `init()` 函数，它通常将模型加载到全局对象中。 此函数只能在 Docker 容器启动时运行一次。 

    * `run(input_data)` 函数，它使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化，但支持其他格式。

    有关示例，请参阅[图像分类教程](tutorial-deploy-models-with-aml.md#make-script)。

1. 创建名为 `myenv.yml` 的环境文件。 此文件是 Conda 环境规范，列出了脚本和模型所需的所有依赖项。 有关示例，请参阅[图像分类教程](tutorial-deploy-models-with-aml.md#make-myenv)。

1. 使用 `score.py` 和 `myenv.yml` 文件配置 Docker 映像：
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. 使用模型和映像配置创建映像：

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    创建映像大约需要 5 分钟。

## <a name="get-the-container-registry-credentials"></a>获取容器注册表凭据

Azure IoT 需要 Azure 机器学习服务用于存储 docker 映像的容器注册表的凭据。 使用以下步骤获取凭据：

1. 登录到 [Azure 门户](https://portal.azure.com/signin/index)。

1. 转到 Azure 机器学习服务工作区并选择“概述”。 要转到容器注册表设置，请选择“注册表”链接。

    ![容器注册表项的图片](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. 进入容器注册表后，选择“访问密钥”，然后启用管理员用户。

    ![访问密钥屏幕的图片](./media/how-to-deploy-to-iot/findaccesskey.png)

1. 保存“登录服务器”、“用户名”和“密码”的值。 

   如果要向 IoT Edge 设备提供对私有容器注册表中映像的访问权限，那么这些凭据是必需的。

## <a name="next-steps"></a>后续步骤

你已完成部署准备工作。 现在，使用[从 Azure 门户部署 Azure IoT Edge 模块](../../iot-edge/how-to-deploy-modules-portal.md)文档中的步骤部署到 Edge 设备。 为设备配置注册表设置时，请使用以前配置的凭据。
