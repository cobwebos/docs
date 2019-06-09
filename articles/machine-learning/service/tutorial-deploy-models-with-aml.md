---
title: 图像分类教程：部署模型
titleSuffix: Azure Machine Learning service
description: 本教程介绍如何使用 Azure 机器学习服务在 Python Jupyter notebook 中使用 scikit-learn 部署映像分类模型。 本教程是由两个部分构成的系列教程的第二部分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 9709d18b00d65578ca3a63fe5044e0b9f7b52d58
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515583"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>教程：在 Azure 容器实例中部署映像分类模型

本教程是由两个部分构成的系列教程的第二部分  。 在[上一个教程](tutorial-train-models-with-aml.md)中，定型了机器学习模型，然后在云中的工作区内注册了模型。  

现在，你已准备好在 [Azure 容器实例](https://docs.microsoft.com/azure/container-instances/)中部署模型作为 Web 服务。 Web 服务是一个映像，在本例中是 Docker 映像。 它用于封装评分逻辑和模型本身。 

在教程的此部分，你将使用 Azure 机器学习服务完成以下任务：

> [!div class="checklist"]
> * 设置测试环境。
> * 从工作区检索模型。
> * 在本地测试模型。
> * 将模型部署到容器实例。
> * 测试已部署的模型。

容器实例是用于测试和了解工作流的理想解决方案。 对于可缩放的生产部署，请考虑使用 Azure Kubernetes 服务。 有关详细信息，请参阅[部署方式及位置](how-to-deploy-and-where.md)。

>[!NOTE]
> 本文中的代码已使用 Azure 机器学习 SDK 版本 1.0.41 进行测试。

## <a name="prerequisites"></a>先决条件
跳到[设置开发环境](#start)来通读 Notebook 步骤。  

若要运行 Notebook，请首先完成以下教程中的模型训练：[教程（第 1 部分）：使用 Azure 机器学习服务训练图像分类模型](tutorial-train-models-with-aml.md)。   然后，使用同一 Notebook 服务器运行 **tutorials/img-classification-part2-deploy.ipynb** Notebook。


## <a name="start"></a>设置环境

首先，设置测试环境。

### <a name="import-packages"></a>导入包

导入本教程所需的 Python 包：

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>检索模型

你在上一个教程中于工作区内注册了一个模型。 现在，加载此工作区并将模型下载到本地目录：


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os 
ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>在本地测试模型

部署之前，请确保模型在本地运行：
* 加载测试数据。
* 预测测试数据。
* 检查混淆矩阵。

### <a name="load-test-data"></a>加载测试数据

从训练教程中创建的 **./data/** 目录中加载测试数据：

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>预测测试数据

若要获得预测结果，请将测试数据集馈送到模型：

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load( os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>检查混淆矩阵

生成一个混淆矩阵，便于查看测试集中有多少样本已正确分类。 注意不正确预测的错误分类值： 

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
   

使用 `matplotlib` 将混淆矩阵显示为图形。 在此图中，x 轴显示实际值，y 轴显示预测值。 每个网格的颜色表示错误率。 颜色越浅，错误率越高。 例如，许多应分类为 5 的值被错误地分类为 3 的值。 因此，(5,3) 处的网格颜色较亮：

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
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

## <a name="deploy-as-a-web-service"></a>部署为 Web 服务

测试模型并对结果感到满意后，请将模型部署为容器实例中托管的 Web 服务。 

若要为容器实例构建正确的环境，请提供以下组件：
* 显示如何使用模型的评分脚本。
* 显示需要安装的包的环境文件。
* 用于生成容器实例的配置文件。
* 之前已训练的模型。

<a name="make-script"></a>

### <a name="create-scoring-script"></a>创建评分脚本

创建名为 **score.py** 的评分脚本。 Web 服务调用使用此脚本来显示模型的用法。

在评分脚本中包含两个必需的函数：
* `init()` 函数，它通常将模型加载到全局对象中。 此函数只能在 Docker 容器启动时运行一次。 

* `run(input_data)` 函数，它使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化，但支持其他格式。

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>创建环境文件

接下来，创建名为 **myenv.yml** 的环境文件，用于指定脚本的所有包依赖项。 此文件用于确保在 Docker 映像中安装所有这些依赖项。 此模型需要 `scikit-learn` 和 `azureml-sdk`：

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
查看 `myenv.yml` 文件的内容：

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>创建配置文件

创建部署配置文件。 指定容器实例容器所需的 CPU 数目和 RAM 大小（单位为 GB）。 虽然这取决于具体的模型，但对于许多模型而言，默认的单核和 1 GB RAM 便已足够。 如果以后需要更多核心或 RAM，必须重新创建映像并重新部署服务。

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>在容器实例中部署
完成部署的估计时间为**大约 7 到 8 分钟**。

配置映像和部署。 下面的代码将完成这些步骤：

1. 使用以下文件生成映像：
   * 评分文件 `score.py`。
   * 环境文件 `myenv.yml`。
   * 模型文件。
1. 在工作区下注册该映像。 
1. 将映像发送到容器实例容器。
1. 使用映像在容器实例中启动容器。
1. 获取 Web 服务 HTTP 终结点。


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

获取评分 Web 服务的 HTTP 终结点，该终结点接受 REST 客户端调用。 可以与想要测试 Web 服务或要将其集成到应用程序中的任何人共享此终结点： 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>测试已部署的服务

之前你使用本地版本的模型对所有测试数据进行了评分。 现在，可以使用来自测试数据的 30 个映像的随机样本来测试部署的模型。  

下面的代码将完成这些步骤：
1. 将数据作为 JSON 数组发送到容器实例中托管的 Web 服务。 

1. 使用 SDK 的 `run` API 来调用服务。 还可以使用任何 HTTP 工具（如 **curl**）进行原始调用。

1. 打印返回的预测并将其与输入映像一起绘制。 红色字体和反色图像（黑底白色）用于突出显示错误分类的样本。 

由于模型精度较高，可能需要运行以下代码几次才能看到错误分类的样本：

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
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

下面是某个随机测试映像样本的结果：

![显示结果的图形](./media/tutorial-deploy-models-with-aml/results.png)

还可以发送原始 HTTP 请求以测试 Web 服务：

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

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

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>后续步骤

+ 了解 [Azure 机器学习服务的所有部署选项](how-to-deploy-and-where.md)。
+ 了解如何[创建 Web 服务的客户端](how-to-consume-web-service.md)。
+  以异步方式[对大量数据进行预测](how-to-run-batch-predictions.md)。
+ 使用 [Application Insights](how-to-enable-app-insights.md) 监视 Azure 机器学习模型。
+ 尝试学习[自动算法选择](tutorial-auto-train-models.md)教程。 
