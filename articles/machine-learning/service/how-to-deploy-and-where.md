---
title: 将模型部署为 Web 服务
titleSuffix: Azure Machine Learning service
description: 了解如何以及在何处部署 Azure 机器学习服务模型，包括：Azure 容器实例、Azure Kubernetes 服务、Azure IoT Edge 和现场可编程门阵列。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 17193bf3285a2052a913293ec3adc6f9b8884f72
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435934"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure 机器学习服务部署模型

Azure 机器学习服务允许使用 SDK 以多种方式部署已训练的模型。 本文档介绍了如何将模型作为 Web 服务部署在 Azure 云或 IoT 边缘设备中。

可以将模型部署到以下计算目标：

| 计算目标 | 部署类型 | Description |
| ----- | ----- | ----- |
| [Azure 容器实例 (ACI)](#aci) | Web 服务 | 快速部署。 适用于开发或测试。 |
| [Azure Kubernetes 服务 (AKS)](#aks) | Web 服务 | 非常适合用于大规模生产部署。 提供了自动缩放和快速的响应时间。 |
| [Azure IoT Edge](#iotedge) | IoT 模块 | 在 IoT 设备上部署模型。 推断在设备上进行。 |
| [现场可编程门阵列 (FPGA)](#fpga) | Web 服务 | 以超低的延迟进行实时推断。 |

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]

## <a name="prerequisites"></a>先决条件

- 已安装 Azure 机器学习服务工作区，以及适用于 Python 的 Azure 机器学习 SDK。 使用 [Azure 机器学习快速入门](quickstart-get-started.md)，了解如何获取这些先决条件。

- 采用 pickle (`.pkl`) 或 ONNX (`.onnx`) 格式的已训练模型。 如果没有已训练的模型，请使用[训练模型](tutorial-train-models-with-aml.md)教程中的步骤训练一个模型，并将其注册到 Azure 机器学习服务。

- 代码部分假设 `ws` 引用你的机器学习工作区。 例如，`ws = Workspace.from_config()`。

## <a name="deployment-workflow"></a>部署工作流

为所有计算目标部署模型的过程类似：

1. 训练模型。
1. 注册模型。
1. 创建映像配置。
1. 创建映像。
1. 将映像部署到计算目标。
1. 测试部署
1. （可选）清理项目。

    * **部署为 Web 服务**时，可以使用三个部署选项：

        * [部署](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-)：使用此方法时，不需要注册模型或创建映像。 但是，无法控制模型或映像的名称，也无法控制关联的标记和说明。
        * [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-)：使用此方法时，不需要创建映像。 但无法控制创建的映像的名称。
        * [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-)：使用此方法之前，请注册模型并创建映像。

        本文档中的示例使用 `deploy_from_image`。

    * **部署为 IoT Edge 模块**时，必须注册模型并创建映像。

## <a name="register-a-model"></a>注册模型

只能部署已训练的模型。 可以使用 Azure 机器学习或其他服务训练模型。 若要从文件注册模型，请使用以下代码：

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

> [!NOTE]
> 该示例使用存储为 pickle 文件的模型，但你也可以使用 ONNX 模型。 有关使用 ONNX 模型的详细信息，请参阅 [ONNX 和 Azure 机器学习](how-to-build-deploy-onnx.md)文档。

有关详细信息，请参阅 [Model 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的参考文档。

## <a id="configureimage"></a>创建映像配置

部署的模型打包为映像。 该映像包含运行模型所需的依赖项。

对于 **Azure 容器实例**、**Azure Kubernetes 服务**和 **Azure IoT Edge** 部署，将使用 `azureml.core.image.ContainerImage` 类来创建映像配置。 然后，使用映像配置创建新的 Docker 映像。 

以下代码演示如何创建新的映像配置：

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml",
                                                 description = "Image with ridge regression model",
                                                 tags = {"data": "diabetes", "type": "regression"}
                                                 )
```

此配置使用 `score.py` 文件将请求传递给模型。 此文件包含两个函数：

* `init()`：此函数通常将模型载入全局对象。 此函数只能在 Docker 容器启动时运行一次。 

* `run(input_data)`：此函数使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化，但支持其他格式。

有关示例 `score.py` 文件，请参阅[图像分类教程](tutorial-deploy-models-with-aml.md#make-script)。 有关使用 ONNX 模型的示例，请参阅 [ONNX 和 Azure 机器学习](how-to-build-deploy-onnx.md)文档。

`conda_file` 参数用于提供 conda 环境文件。 此文件定义已部署的模型的 conda 环境。 有关创建此文件的详细信息，请参阅[创建环境文件 (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file)。

有关详细信息，请参阅 [ContainerImage 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)的参考文档

## <a id="createimage"></a>创建映像

创建映像配置后，可以使用它来创建映像。 此映像存储在工作区的容器注册表中。 创建后，可将同一映像部署到多个服务。

```python
# Create the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**时间估计**：大约 3 分钟。

同时注册多个映像时，会自动控制映像的版本。 例如，为注册为 `myimage` 的第一个映像分配 ID `myimage:1`。 下一次将某个映像注册为 `myimage` 时，新映像的 ID 是 `myimage:2`。

有关详细信息，请参阅 [ContainerImage 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)的参考文档。

## <a name="deploy-the-image"></a>部署映像

部署过程根据要部署到的计算目标而略有不同。 使用以下部分中的信息了解如何部署到：

* [Azure 容器实例](#aci)
* [Azure Kubernetes 服务](#aks)
* [Project Brainwave（现场可编程门阵列）](#fpga)
* [Azure IoT Edge 设备](#iotedge)

### <a id="aci"></a>部署到 Azure 容器实例

如果满足以下一个或多个条件，请使用 Azure 容器实例将模型部署为 Web 服务：

- 你需要快速部署并验证你的模型。 ACI 部署在 5 分钟内完成。
- 正在测试一个开发中的模型。 若要查看 ACI 的配额和区域可用性，请参阅文档 [Azure 容器实例的配额和区域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)。

若要部署到 Azure 容器实例，请使用以下步骤：

1. 定义部署配置。 以下示例定义一种使用 1 个 CPU 核心和 1 GB 内存的配置：

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. 若要部署在本文档的[创建映像](#createimage)部分创建的映像，请使用以下代码：

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **时间估计**：大约 3 分钟。

    > [!TIP]
    > 如果在部署期间出现错误，请使用 `service.get_logs()` 查看 AKS 服务日志。 记录的信息可能指示错误的原因。

有关详细信息，请参阅 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) 和 [Webservice](https://docs.microsoft.comS/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py) 类的参考文档。

### <a id="aks"></a>部署到 Azure Kubernetes 服务

若要将模型部署为大规模生产 Web 服务，请使用 Azure Kubernetes 服务 (AKS)。 可以使用现有 AKS 群集，也可使用 Azure 机器学习 SDK、CLI 或 Azure 门户创建新群集。

对于工作区而言，创建 AKS 群集是一次性过程。 可以将此群集重复用于多个部署。 如果删除该群集，则在下次需要进行部署时必须创建新群集。

Azure Kubernetes 服务提供了以下功能：

* 自动缩放
* 日志记录
* 模型数据集合
* Web 服务的快速响应时间

若要部署到 Azure Kubernetes 服务，请使用以下步骤：

1. 若要创建 AKS 群集，请使用以下代码：

    > [!IMPORTANT]
    > 创建 AKS 群集是工作区的一次性过程。 创建后，即可重复使用此群集进行多个部署。 如果删除该群集或包含该群集的资源组，则在下次需要进行部署时必须创建新群集。
    > 对于 [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果为 agent_count 和 vm_size 选择自定义值，则需要确保 agent_count 乘以 vm_size 的结果大于或等于 12 个虚拟 CPU。 例如，如果对 vm_size 使用“Standard_D3_v2”（有 4 个虚拟 CPU），则应该为 agent_count 选择 3 或更大的数字。

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget

    # Use the default configuration (you can also provide parameters to customize this)
    prov_config = AksCompute.provisioning_configuration()

    aks_name = 'aml-aks-1' 
    # Create the cluster
    aks_target = ComputeTarget.create(workspace = ws, 
                                        name = aks_name, 
                                        provisioning_configuration = prov_config)

    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

    **时间估计**：大约 20 分钟。

    > [!TIP]
    > 如果 Azure 订阅中已有 AKS 群集并且其版本为 1.11.*，则可以使用该群集来部署映像。 以下代码演示如何将现有群集附加到工作区：
    >
    > ```python
    > # Set the resource group that contains the AKS cluster and the cluster name
    > resource_group = 'myresourcegroup'
    > cluster_name = 'mycluster'
    > 
    > # Attatch the cluster to your workgroup
    > attach_config = AksCompute.attach_configuration(resource_group = resource_group,
    >                                          cluster_name = cluster_name)
    > compute = ComputeTarget.attach(ws, 'mycompute', attach_config)
    > 
    > # Wait for the operation to complete
    > aks_target.wait_for_completion(True)
    > ```

2. 若要部署在本文档的[创建映像](#createimage)部分创建的映像，请使用以下代码：

    ```python
    from azureml.core.webservice import Webservice, AksWebservice

    # Set configuration and service name
    aks_config = AksWebservice.deploy_configuration()
    aks_service_name ='aks-service-1'
    # Deploy from image
    service = Webservice.deploy_from_image(workspace = ws, 
                                                name = aks_service_name,
                                                image = image,
                                                deployment_config = aks_config,
                                                deployment_target = aks_target)
    # Wait for the deployment to complete
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

    > [!TIP]
    > 如果在部署期间出现错误，请使用 `service.get_logs()` 查看 AKS 服务日志。 记录的信息可能指示错误的原因。

有关详细信息，请参阅 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py) 类的参考文档。

### <a id="fpga"></a>部署到现场可编程门阵列 (FPGA)

使用 Project Brainwave 可以在实时推断请求时实现超低的延迟。 Project Brainwave 可加快部署于 Azure 云中现场可编程门阵列上的深度神经网络 (DNN) 的速度。 常用 DNN 可用作传输学习的特征化器，也可以使用基于你自己的数据训练的权重进行自定义。

有关使用 Project Brainwave 部署模型的演练，请参阅[部署到 FPGA](how-to-deploy-fpga-web-service.md) 文档。

### <a id="iotedge"></a>部署到 Azure IoT Edge

Azure IoT Edge 设备是运行 Azure IoT Edge 运行时的基于 Linux 或 Windows 的设备。 机器学习模型可以作为 IoT Edge 模块部署到这些设备。 将模型部署到 IoT Edge 设备允许设备直接使用模型，而不必将数据发送到云进行处理。 你可以获得更快的响应时间，执行更少的数据传输。

Azure IoT Edge 模块将从容器注册表部署到设备。 从模型创建映像时，该映像将存储在工作区的容器注册表中。

#### <a name="set-up-your-environment"></a>设置环境

* 开发环境。 有关详细信息，请参阅[如何配置开发环境](how-to-configure-environment.md)文档。

* Azure 订阅中的 [Azure IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)。 

* 定型的模型。 有关如何训练模型的示例，请参阅[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)文档。 [在适用于 Azure IoT Edge GitHub 存储库的 AI 工具包](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)上提供了预训练模型。

#### <a name="prepare-the-iot-device"></a>准备 IoT 设备
必须创建 IoT 中心并注册设备，或通过[此脚本](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister)重复使用现有的设备。

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you wnat to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

保存 "cs":"{复制此字符串}" 后面的最终连接字符串。

将[此脚本](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge)下载到 UbuntuX64 IoT Edge 节点或 DSVM 并运行以下命令，以初始化设备：

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

IoT Edge 节点已准备好接收 IoT 中心的连接字符串。 查找 ```device_connection_string:``` 行，并粘贴上面复制内容的引号之间的连接字符串。

此外，可以遵循[快速入门：将第一个 IoT Edge 模块部署到 Linux x64 设备](../../iot-edge/quickstart-linux.md)文档，来逐步了解如何注册设备和安装 IoT 运行时。


#### <a name="get-the-container-registry-credentials"></a>获取容器注册表凭据
若要将 IoT Edge 模块部署到设备，Azure IoT 需要容器注册表（Azure 机器学习服务在其中存储 Docker 映像）的凭据。

可通过两种方式轻松检索所需的容器注册表凭据：

+ **在 Azure 门户中**：

  1. 登录到 [Azure 门户](https://portal.azure.com/signin/index)。

  1. 转到 Azure 机器学习服务工作区并选择“概述”。 要转到容器注册表设置，请选择“注册表”链接。

     ![容器注册表项的图片](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. 进入容器注册表后，选择“访问密钥”，然后启用管理员用户。
 
     ![访问密钥屏幕的图片](./media/how-to-deploy-and-where/findaccesskey.png)

  1. 保存“登录服务器”、“用户名”和“密码”的值。 

+ **使用 Python 脚本**：

  1. 在运行的上述代码后面使用以下 Python 脚本来创建容器：

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. 保存 ContainerURL、servername、username 和 password 的值。 

     必须使用这些凭据向 IoT Edge 设备提供对专用容器注册表中映像的访问权限。

#### <a name="deploy-the-model-to-the-device"></a>将模型部署到设备

可以通过运行[此脚本](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel)并提供上述步骤中的以下信息来轻松部署模型：容器注册表名称、用户名、密码、映像位置 URL、所需部署名称、IoT 中心名称和创建的设备 ID。 可以在 VM 中遵循以下步骤实现此目的： 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

或者，可以遵循[从 Azure 门户部署 Azure IoT Edge 模块](../../iot-edge/how-to-deploy-modules-portal.md)文档中的步骤，将映像部署到设备。 为设备配置注册表设置时，请使用工作区容器注册表的登录服务器、用户名和密码。

> [!NOTE]
> 如果你不熟悉 Azure IoT，请参阅以下文档获取该服务的入门信息：
>
> * [快速入门：将第一个 IoT Edge 模块部署到 Linux 设备](../../iot-edge/quickstart-linux.md)
> * [快速入门：将第一个 IoT Edge 模块部署到 Windows 设备](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>测试 Web 服务部署

若要测试 Web 服务部署，可以使用 Webservice 对象的 `run` 方法。 以下示例将某个 JSON 文档设置为 Web 服务，并显示结果。 发送的数据必须符合模型的预期。 在此示例中，数据格式与糖尿病模型预期的输入相匹配。

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

## <a name="update-the-web-service"></a>更新 Web 服务

若要更新 Web 服务，请使用 `update` 方法。 以下代码演示如何将 Web 服务更新为使用新映像：

```python
from azureml.core.webservice import Webservice

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)
# Update the image used by the service
service.update(image = new-image)
print(service.state)
```

> [!NOTE]
> 更新映像时，Web 服务不会自动更新。 必须手动更新要使用新映像的每个服务。

## <a name="clean-up"></a>清理

若要删除已部署的 Web 服务，请使用 `service.delete()`。

若要删除映像，请使用 `image.delete()`。

若要删除已注册的模型，请使用 `model.delete()`。

## <a name="next-steps"></a>后续步骤

* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [如何运行批量预测](how-to-run-batch-predictions.md)
