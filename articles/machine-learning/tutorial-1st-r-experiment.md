---
title: 教程：使用 R 创建机器学习模型
titleSuffix: Azure Machine Learning
description: 在本教程中，我们将使用 Azure 机器学习 R SDK 创建逻辑回归模型，该模型预测交通事故中的死亡几率。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: dea5b3fb6cf20924666668e59e370399664d6b28
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684747"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model"></a>教程：使用 R 创建机器学习模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本教程中，我们将使用 Azure 机器学习 R SDK 创建逻辑回归模型，该模型预测交通事故中的死亡几率。 你将了解 Azure 机器学习云资源如何与 R 一起工作，提供一个可缩放的环境以用来训练和部署模型。  

将在本教程中执行以下任务：
> [!div class="checklist"]
> * 创建 Azure 机器学习工作区
> * 将笔记本文件夹和运行本教程所需的文件克隆到工作区
> * 从工作区打开 RStudio
> * 加载数据并准备训练
> * 将数据上传到数据存储，使之可用于远程训练
> * 创建计算资源以远程训练模型
> * 训练 `caret` 模型以预测死亡几率
> * 部署预测终结点
> * 在 R 中测试模型

如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。


## <a name="create-a-workspace"></a>创建工作区

Azure 机器学习工作区是云中的基础资源，用于试验、训练和部署机器学习模型。 它将 Azure 订阅和资源组关联到服务中一个易于使用的对象。 

通过 Azure 门户创建工作区，该门户是用于管理 Azure 资源的基于 Web 的控制台。 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> 记下你的工作区和订阅   。 你将需要这些项才能确保在正确的位置创建试验。 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>克隆笔记本文件夹

本示例使用工作区中的云笔记本服务器来实现免安装的预配置体验。 如果你希望控制环境、包和依赖项，请使用[自己的环境](https://azure.github.io/azureml-sdk-for-r/articles/installation.html)。

在 Azure 机器学习工作室中完成以下试验设置和运行步骤，该工作室是包含用于为所有技能级别的数据科学实践者执行数据科学方案的机器学习工具的合并界面。

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。

1. 选择创建的订阅和工作区。

1. 选择左侧的“笔记本”  。

1. 打开“Samples”文件夹  。

1. 打开 R 文件夹  。

1. 打开包含版本号的文件夹。  此数字表示 R SDK 的当前版本。

1. 选择 **vignettes** 文件夹右侧的“...”，然后选择“克隆”。  

    ![克隆文件夹](media/tutorial-1st-r-experiment/clone-folder.png)

1. 将显示文件夹列表，其中显示了访问工作区的每个用户。  选择要将“vignettes”文件夹克隆到其中的文件夹  。

## <a name="a-nameopenopen-rstudio"></a><a name="open">打开 RStudio

在计算实例或 Notebook VM 上使用 RStudio 运行此教程。  

1. 选择左侧的“计算”  。

1. 如果没有计算资源，请添加一个。

1. 计算运行后，使用 RStudio 链接打开 RStudio  。

1. 在 RStudio 中，“vignettes”文件夹位于右下位置“文件”部分中的“用户”下几级的位置    。  在 vignettes 下选择“train-and-deploy-to-aci”文件夹，找到本教程中所需的文件  。 

> [!Important]
> 本文的余下部分包含 *train-and-deploy-to-aci.Rmd* 文件中所示的相同内容。 如果你有 RMarkdown 方面的经验，可随意使用该文件中的代码。  或者，可将该文件或本文中的代码片段复制/粘贴到 R 脚本或命令行中。  


## <a name="set-up-your-development-environment"></a>设置开发环境
本教程中的开发工作设置包括以下操作：

* 安装所需程序包
* 连接到工作区，使计算实例能够与远程资源通信
* 创建一个试验用于跟踪运行
* 创建用于训练的远程计算目标

### <a name="install-required-packages"></a>安装所需程序包

 * 从 CRAN 安装最新版本。

    ```R
    # install the latest version from CRAN
    install.packages("azuremlsdk")
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```
    
* 或从 GitHub 安装开发版本。

    ```R
    # or install the development version from GitHub
    remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```

现在，继续导入 azuremlsdk  包。

```R
library(azuremlsdk)
```

训练和评分脚本（`accidents.R` 和 `accident_predict.R`）有其他一些依赖项。 如果你打算在本地运行这些脚本，请确保同时安装了这些必需的包。

### <a name="load-your-workspace"></a>加载工作区
实例化现有工作区中的某个工作区对象。 以下代码将从 **config.json** 文件加载工作区详细信息。 还可以使用 [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html) 检索工作区。

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>创建试验
Azure ML 试验跟踪一组运行（通常来自相同的训练脚本）。 创建一个试验来跟踪运行，这些运行是基于事故数据训练 caret 模型的。

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>创建计算目标
Azure 机器学习托管计算 (AmlCompute) 是一项托管服务，可以让数据科学家在 Azure 虚拟机群集上定型机器学习模型。 示例包括带 GPU 支持的 VM。 本教程将创建一个单节点 AmlCompute 群集作为训练环境。 如果工作区中尚无计算群集，以下代码将创建计算群集。

如果没有计算群集，可能需要等待几分钟时间来预配计算群集。

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>准备要训练的数据
本教程使用美国[国内高速公路交通安全管理](https://cdan.nhtsa.gov/tsftables/tsfar.htm)中的数据（感谢 [Mary C. Meyer 和 Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm) 提供）。
此数据集包括美国发生的 25,000 多次车祸的数据，以及可用于预测死亡几率的变量。 首先，将数据导入 R 中，将其转换为新的数据帧 `accidents` 用于分析，然后将其导出到 `Rdata` 文件。

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>将数据上传到数据存储
将数据上传到云中，使远程训练环境能够对其进行访问。 每个 Azure 机器学习工作区均附带一个默认的数据存储，其中存储了与 Azure Blob 容器（在附加到工作区的存储帐户中预配）的连接信息。 以下代码将前面创建的事故数据上传到该数据存储。

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>训练模型

对于本教程，请使用远程计算群集基于上传的数据拟合一个逻辑回归模型。 若要提交作业，需要：

* 准备训练脚本
* 创建估算器
* 提交作业

### <a name="prepare-the-training-script"></a>准备训练脚本
本教程的同一目录中已提供了一个名为 `accidents.R` 的训练脚本。 请注意**训练脚本中**的以下详细信息，这些操作的目的是利用 Azure 机器学习进行训练：

* 训练脚本采用 `-d` 参数来查找包含训练数据的目录。 稍后定义并提交作业时，需要指向数据存储来获取此参数。 Azure ML 会将存储文件夹装载到训练作业的远程群集。
* 训练脚本使用 `log_metric_to_run()` 将最终准确度作为指标，记录到 Azure ML 中的运行记录。 Azure ML SDK 提供一组日志记录 API，用于在训练运行期间记录各种指标。 这些指标将记录到试验运行记录中，并在其中持久保存。 以后随时可以访问这些指标，或者在[工作室](https://ml.azure.com)的运行详细信息页中查看这些指标。 参阅有关整套日志记录方法 `log_*()` 的[参考](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation)。
* 训练脚本将模型保存到一个名为**outputs** 的目录中。 Azure ML 将对 `./outputs` 文件夹进行特殊的处理。 在训练期间，Azure ML 会自动将写入到 `./outputs` 的文件上传到运行记录，并将其持久保存为项目。 将训练的模型保存到 `./outputs` 即使是在运行结束之后以及不再可以访问远程训练环境的情况下，你仍可以访问和检索模型文件。

### <a name="create-an-estimator"></a>创建估算器

Azure ML 评估器封装了在计算目标上执行训练脚本所需的运行配置信息。 Azure ML 运行在指定的计算目标上作为容器化作业运行。 默认情况下，为训练作业生成的 Docker 映像将包含 R、Azure ML SDK 和一组通用的 R 包。 查看此处包含的默认包的完整列表。

若要创建评估器，请定义：

* 包含用于训练的脚本的目录 (`source_directory`)。 此目录中的所有文件将上传到群集节点以供执行。 该目录必须包含训练脚本以及所需的任何其他脚本。
* 要执行的训练脚本 (`entry_script`)。
* 计算目标 (`compute_target`)，在本例中为前面创建的 AmlCompute 群集。
* 训练脚本中所需的参数 (`script_params`)。 Azure ML 将使用 `Rscript` 以命令行脚本的形式运行训练脚本。 本教程在脚本中指定一个参数，即数据目录的装入点，可以使用 `ds$path(target_path)` 访问它。
* 用于训练的任何环境依赖项。 为训练生成的默认 Docker 映像已包含训练脚本中所需的三个包（`caret`、`e1071` 和 `optparse`）。  因此，无需指定附加的信息。 如果使用默认未包含的 R 包，请使用评估器的 `cran_packages` 参数添加附加的 CRAN 包。 有关完整的可配置选项集，请参阅 [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) 参考。

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>在远程群集上提交作业

最后，请在群集上提交要运行的作业。 `submit_experiment()` 返回一个 Run 对象，然后，你可以使用该对象来与运行对接。 总的来说，首次运行需要大约 10 分钟  。 但对于后续的运行，只要脚本依赖项未更改，就会重复使用同一个 Docker 映像。  在这种情况下，映像将会缓存，容器启动速度要快得多。

```R
run <- submit_experiment(exp, est)
```

可以在 RStudio Viewer 中查看运行详细信息。 单击提供的“Web 视图”链接会转到 Azure 机器学习工作室，在其 UI 中可以监视运行。

```R
view_run_details(run)
```

模型训练在后台发生。 在运行更多代码之前，请耐心等待，直到该模型完成定型。

```R
wait_for_run_completion(run, show_output = TRUE)
```

You and colleagues with access to the workspace can submit multiple experiments in parallel, and Azure ML will take of scheduling the tasks on the compute cluster. 甚至可以将群集配置为自动扩展到多个节点，并在队列中不再存在计算任务时缩减。 此配置可让团队经济高效地共享计算资源。

## <a name="retrieve-training-results"></a>检索训练结果
完成训练后，可以访问已保存到运行记录的作业项目，包括记录的所有指标，以及已训练的最终模型。

### <a name="get-the-logged-metrics"></a>获取记录的指标
训练脚本 `accidents.R` 中记录了来自模型的一个指标：训练数据中的预测准确度。 可以在[机器学习工作室](https://ml.azure.com)中查看指标，或者将其作为 R 列表提取到本地会话，如下所示：

```R
metrics <- get_run_metrics(run)
metrics
```

如果已运行多个试验（例如，使用不同的变量、算法或超参数），可以使用每个运行提供的指标来比较和选择要在生产环境中使用的模型。

### <a name="get-the-trained-model"></a>获取已训练的模型
可以检索已训练的模型，并在本地 R 会话中查看结果。 以下代码将下载 `./outputs` 目录的内容，其中包括模型文件。

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

可以看到一些导致评估的死亡几率上升的因素：

* 撞击速度较高 
* 男性驾驶员
* 乘员年龄较大
* 乘客

可以看到死亡几率下降的因素：

* 有安全气囊
* 系了安全带
* 正面碰撞 

汽车制造年份没有明显的影响。

可以使用此模型做出新的预测：

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>部署为 Web 服务

使用该模型可以预测碰撞时的死亡危险几率。 使用 Azure ML 将模型部署为预测服务。 本教程将在 [Azure 容器实例](https://docs.microsoft.com/azure/container-instances/) (ACI) 中部署 Web 服务。

### <a name="register-the-model"></a>注册模型

首先，使用 [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html) 将下载的模型注册到工作区。 注册的模型可以是任意文件集合，但在本例中，使用 R 模型对象已足够。 Azure ML 将使用注册的模型进行部署。

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>定义推理依赖项
若要为模型创建 Web 服务，首先需要创建一个评分脚本 (`entry_script`)，这是一个 R 脚本，它会提取输入变量值（采用 JSON 格式）并从模型输出预测结果。 本教程使用随附的评分文件 `accident_predict.R`。 评分脚本必须包含 `init()` 方法，该方法加载模型并返回一个函数，而该函数则使用该模型基于输入数据进行预测。 有关更多详细信息，请参阅[文档](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details)。

接下来，为脚本的包依赖项定义 Azure ML **环境**。 使用环境指定运行脚本所需的 R 包（来自 CRAN 或其他位置）。 还可以提供环境变量的值，脚本可以引用这些值来修改其行为。 默认情况下，Azure ML 会生成为训练的评估器所用的相同默认 Docker 映像。 由于本教程没有特殊的要求，因此可以创建一个不使用特殊属性的环境。

```R
r_env <- r_environment(name = "basic_env")
```

若要改用自己的 Docker 映像进行部署，请指定 `custom_docker_image` 参数。 有关用于定义环境的完整可配置选项集，请参阅 [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) 参考。

完成所有准备工作后，接下来需要创建一个**推理配置**用于封装评分脚本和环境依赖项。

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>部署到 ACI
本教程将服务部署到 ACI。 此代码预配单个容器来响应入站请求，此容器适合用于测试和轻量负载。 有关其他可配置选项，请参阅 [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html)。 （对于生产规模的部署，还可以[部署到 Azure Kubernetes 服务](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html)。）

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

现在，请将模型部署为 Web 服务。 部署**可能需要花费几分钟时间**。 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>测试已部署的服务

将模型部署为服务后，可以使用 [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html) 在 R 中测试该服务。  提供一组新数据用于预测，将其转换为 JSON，然后将其发送到该服务。

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

还可以获取 Web 服务的、接受 REST 客户端调用的 HTTP 终结点。 可以与想要测试 Web 服务或要将其集成到应用程序中的任何人共享此终结点。

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>清理资源

请删除不再需要的资源。 请不要删除将来仍要使用的任何资源。 

删除 Web 服务：
```R
delete_webservice(aci_service)
```

删除已注册的模型：
```R
delete_model(model)
```

删除计算群集：
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>删除所有内容

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

还可保留资源组，但请删除单个工作区。 显示工作区属性，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

* 在 R 中完成第一个 Azure 机器学习试验后，请详细了解[适用于 R 的 Azure 机器学习 SDK](https://azure.github.io/azureml-sdk-for-r/index.html)。

* 通过其他 *vignettes* 文件夹中的示例，详细了解如何将 Azure 机器学习与 R 配合使用。
