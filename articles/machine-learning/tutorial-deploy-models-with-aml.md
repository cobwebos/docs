---
title: 图像分类教程：部署模型
titleSuffix: Azure Machine Learning
description: 本教程（由两部分组成的系列教程的第二部分）介绍如何通过 Azure 机器学习在 Python Jupyter notebook 中使用 scikit-learn 部署图像分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: 5d064b0953d8d6e9089dcfa765ff29bb97088f34
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801104"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>教程：在 Azure 容器实例中部署映像分类模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本教程是由两个部分构成的系列教程的第二部分  。 在[上一个教程](tutorial-train-models-with-aml.md)中，训练了机器学习模型，然后在云中的工作区内注册了模型。  现在，你已准备好将模型部署为 Web 服务。 Web 服务是一个映像，在本例中是 Docker 映像。 它用于封装评分逻辑和模型本身。 

在教程的此部分中，你将使用 Azure 机器学习完成以下任务：

> [!div class="checklist"]
> * 设置测试环境。
> * 从工作区检索模型。
> * 将模型部署到容器实例。
> * 测试已部署的模型。

容器实例是用于测试和了解工作流的理想解决方案。 对于可缩放的生产部署，请考虑使用 Azure Kubernetes 服务。 有关详细信息，请参阅[部署方式及位置](how-to-deploy-and-where.md)。

>[!NOTE]
> 本文中的代码已使用 Azure 机器学习 SDK 版本 1.0.83 进行测试。

## <a name="prerequisites"></a>先决条件

若要运行 Notebook，请首先完成以下教程中的模型训练：[教程（第 1 部分）：训练映像分类模型](tutorial-train-models-with-aml.md)。   然后，在克隆的 tutorials/image-classification-mnist-data 文件夹中打开 img-classification-part2-deploy.ipynb 笔记本   。

如果希望在自己的[本地环境](how-to-configure-environment.md#local)中使用此教程，也可以在 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 上找到它。  请确保已在环境中安装了 `matplotlib` 和 `scikit-learn`。 

> [!Important]
> 本文的其余部分包含的内容与在笔记本中看到的内容相同。  
>
> 如果要在运行代码时继续阅读，请立即切换到 Jupyter 笔记本。
> 若要在笔记本中运行单个代码单元，请单击代码单元，然后按 **Shift+Enter**。 或者，通过从顶部工具栏中选择“全部运行”  来运行整个笔记本。

## <a name="set-up-the-environment"></a><a name="start"></a>设置环境

首先，设置测试环境。

### <a name="import-packages"></a>导入包

导入此教程所需的 Python 包。


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>部署为 Web 服务

将该模型部署为在 ACI 中托管的 Web 服务。 

要为 ACI 构建正确的环境，请提供以下信息：
* 显示如何使用模型的评分脚本
* 要生成 ACI 的配置文件
* 之前已定型的模型

### <a name="create-scoring-script"></a>创建评分脚本

创建名为 score.py 的评分脚本，由 Web 服务调用用于显示如何使用该模型。

必须在评分脚本中包含两个必需的函数：
* `init()` 函数，它通常将模型加载到全局对象中。 此函数只能在 Docker 容器启动时运行一次。 

* `run(input_data)` 函数，它使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化，但支持其他格式。

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>创建配置文件

创建部署配置文件并指定 ACI 容器所需的 CPU 数量和 RAM 大小（单位为 GB）。 虽然这取决于具体模型，但对于许多模型而言，默认的 1 核和 1 GB 的 RAM 通常已足够。 如果你认为以后需要更多核心或 RAM，请重新创建映像并重新部署服务。


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>在 ACI 中部署
估计完成时间：约 2-5 分钟 

配置映像和部署。 下面的代码将完成这些步骤：

1. 使用训练过程中保存的环境 (`tutorial-env`) 创建包含模型所需依赖项的环境对象。
1. 使用以下项创建将模型部署为 Web 服务所需的推理配置：
   * 评分文件 (`score.py`)
   * 在上一步中创建的环境对象
1. 将模型部署到 ACI 容器。
1. 获取 Web 服务 HTTP 终结点。


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

获取评分 Web 服务的 HTTP 终结点，该终结点接受 REST 客户端调用。 可以与想要测试 Web 服务或要将其集成到应用程序中的任何人共享此终结点。


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>测试模型


### <a name="download-test-data"></a>下载测试数据
将测试数据下载到“./data/”  目录


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>加载测试数据

从定型教程中创建的 ./data/  目录中加载测试数据。


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>预测测试数据

将测试数据集提供给模型以获得预测。


下面的代码将完成这些步骤：
1. 将数据作为 JSON 数组发送到 ACI 中托管的 Web 服务。 

1. 使用 SDK 的 `run` API 来调用服务。 还可以使用任何 HTTP 工具（如 curl）进行原始调用。


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>检查混淆矩阵

生成一个混淆矩阵，便于查看测试集中有多少样本已正确分类。 请注意不正确预测的错误分类值。


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

输出显示混淆矩阵：

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

使用 `matplotlib` 将混淆矩阵显示为图形。 在此图中，X 轴表示实际值，Y 轴表示预测的值。 每个网格的颜色表示错误率。 颜色越浅，错误率越高。 例如，许多应分类为 5 的值被错误地分类为 3 的值。 因此，(5,3) 处的网格颜色较亮。

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![显示混淆矩阵的图表](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>显示预测

使用来自测试数据的 30 个映像的随机样本来测试部署的模型。  


1. 打印返回的预测并将其与输入映像一起绘制。 红色字体和反向图像（黑底白色）用于突出显示错误分类的样本。 

 由于模型精度较高，可能需要运行以下代码几次才能看到错误分类的样本。



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

还可以发送原始 HTTP 请求以测试 Web 服务。


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>清理资源

若要保留资源组和工作区用于其他教程和探索，可以使用此 API 调用仅删除容器实例部署：

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>后续步骤

+ 了解 [Azure 机器学习的所有部署选项](how-to-deploy-and-where.md)。
+ 了解如何[创建 Web 服务的客户端](how-to-consume-web-service.md)。
+  以异步方式[对大量数据进行预测](how-to-use-parallel-run-step.md)。
+ 使用 [Application Insights](how-to-enable-app-insights.md) 监视 Azure 机器学习模型。
+ 尝试学习[自动算法选择](tutorial-auto-train-models.md)教程。 
