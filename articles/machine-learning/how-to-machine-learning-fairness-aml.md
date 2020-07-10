---
title: '在 Python (预览版中评估 ML 模型的公平) '
titleSuffix: Azure Machine Learning
description: 了解如何在中评估模型的公平 Azure 机器学习
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 07/09/2020
ms.custom: tracking-python
ms.openlocfilehash: 4f791ef9e18032ce6773f4262586431e693b43b2
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206742"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>将 Azure 机器学习与 Fairlearn 开源包一起使用，以 (预览版评估 ML 模型的公平) 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本操作方法指南中，你将了解如何将[Fairlearn](https://fairlearn.github.io/)开源 Python 包与 Azure 机器学习配合使用来执行以下任务：

* 评估模型预测的公平。 若要在机器学习中了解有关公平的详细信息，请参阅[机器学习一文中的公平](concept-fairness-ml.md)。
* 在 Azure 机器学习 studio 中上传、列出和下载公平评估见解。
* 请参阅 Azure 机器学习 studio 中的 "公平评估" 仪表板，与模型 () "公平见解交互。

>[!NOTE]
> 公平评估并非纯粹的技术运动。 **此包可帮助你评估机器学习模型的公平，但只有你可以配置并做出有关模型执行方式的决策。**  尽管此包有助于识别量化指标以评估公平，但机器学习模型的开发人员还必须执行定性分析来评估其自己的模型的公平。

## <a name="azure-machine-learning-fairness-sdk"></a>Azure 机器学习公平 SDK 

Azure 机器学习公平 SDK `azureml-contrib-fairness` 集成了 Azure 机器学习内的开源 Python 包[Fairlearn](http://fairlearn.github.io)。 若要了解有关 Fairlearn 在 Azure 机器学习中的集成的详细信息，请查看这些[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)。 有关 Fairlearn 的详细信息，请参阅[示例指南](https://fairlearn.github.io/auto_examples/notebooks/index.html)和[示例笔记本](https://github.com/fairlearn/fairlearn/tree/master/notebooks)。 

使用以下命令安装 `azureml-contrib-fairness` 和 `fairlearn` 包：
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>为单个模型上载公平见解

下面的示例演示如何使用公平包将模型公平见解上载到 Azure 机器学习，并查看 Azure 机器学习 studio 中的 "公平评估" 仪表板。

1. 训练 Jupyter 笔记本中的示例模型。 

    对于数据集，我们使用众所周知的成人人口数据集，我们使用 (加载这些数据集， `shap` 以方便) 。 出于本示例的目的，我们将此数据集视为一个贷款决策问题，并假设标签指示每个个体是否偿还过去的贷款。 我们将使用数据来训练预测值，以预测之前不可见的个人是否会报答贷款。 假设模型预测用于决定是否应向个人提供贷款。

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. 登录到 Azure 机器学习并注册模型。
   
    公平仪表板可以与已注册或未注册的模型集成。 在 Azure 机器学习中注册模型，步骤如下：
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_linear_regression", unmitigated_predictor)
    ```

3. 预先计算公平指标。

    使用 Fairlearn 的包创建仪表板词典 `metrics` 。 `_create_group_metric_set`方法的参数与仪表板构造函数类似，只是将敏感功能作为字典传递 (以确保名称) 可用。 在这种情况下，还必须指定预测类型 (二元分类) 调用此方法时。

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = unmitigated_predictor.predict(X_test)
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 上传预计算公平指标。
    
    现在，导入 `azureml.contrib.fairness` 包以执行上传：

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    创建一个试验，然后运行并上传仪表板：
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. 检查 Azure 机器学习 studio 中的 "公平" 仪表板

    如果你完成了前面的步骤 (上传 Azure 机器学习) 生成的公平见解，则可以在[Azure 机器学习 studio](https://ml.azure.com)中查看 "公平" 仪表板。 此仪表板是 Fairlearn 中提供的相同可视化仪表板，可用于分析敏感功能的子组中的发达国家 (例如，雌雄) 。
    通过以下途径之一访问 Azure 机器学习工作室中的可视化仪表板：

    * **试验窗格 (预览) **
    1. 在左侧窗格中选择“试验”，以查看在 Azure 机器学习中运行的试验列表。
    1. 选择特定的试验可查看该试验中的所有运行。
    1. 选择一个 "运行"，然后选择 "**公平**" 选项卡，然后选择 "说明可视化" 仪表板。


    [![公平仪表板](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **模型窗格**
    1. 如果按照前面的步骤注册了原始模型，则可以选择左窗格中的 "**模型**" 来查看它。
    1. 选择模型，然后选择 "**公平**" 选项卡，查看 "解释可视化效果" 仪表板。

    若要详细了解可视化仪表板及其包含的内容，请查看 Fairlearn 的[用户指南](https://fairlearn.github.io/user_guide/assessment.html#fairlearn-dashboard)。

s # # 为多个模型上载公平见解

如果有兴趣比较多个模型并了解其公平评估的不同之处，可以将多个模型传递到可视化仪表板，并浏览其性能-公平权衡。

1. 训练模型：
    
    除了以前的逻辑回归模型之外，我们现在还创建了一个基于支持向量计算机估计器的第二个分类器，并使用 Fairlearn 的包上传公平仪表板字典 `metrics` 。 请注意，在这里，我们跳过加载和预处理数据的步骤，直接转到模型定型阶段。


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. 注册模型

    接下来，在 Azure 机器学习中注册这两个模型。 为方便起见，在后续方法调用中，将结果存储在字典中，这会将 `id` 已注册模型的设置 (格式) 的字符串映射 `name:version` 到预测器本身：

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_linear_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. 在本地加载 Fairlearn 仪表板

    在将公平见解上载到 Azure 机器学习之前，可以在本地调用的 Fairlearn 仪表板中检查这些预测。 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. 预先计算公平指标。

    使用 Fairlearn 的包创建仪表板词典 `metrics` 。

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 上传预计算公平指标。
    
    现在，导入 `azureml.contrib.fairness` 包以执行上传：

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    创建一个试验，然后运行并上传仪表板：
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    与上一节类似，你可以遵循上述路径中所述的某个路径 (Azure 机器学习 studio 中的**试验**或**模型**) 来访问可视化仪表板，并在公平和性能方面对这两种模型进行比较。


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>上传 unmitigated 并缓解公平见解

您可以使用 Fairlearn 的[缓解算法](https://fairlearn.github.io/user_guide/mitigation.html)，将其生成的缓解模式 (s) 与原始的 unmitigated 模型进行比较，并在比较的模型之间导航性能/公平权衡。

若要查看演示如何使用[网格搜索](https://fairlearn.github.io/user_guide/mitigation.html#grid-search)缓解算法的示例 (这会创建一组具有不同公平和性能折衷的缓解模型) 查看此[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb)。 

如果在一次运行中上传多个模型的公平见解，则可以比较与公平和性能相关的模型。 您可以进一步单击模型比较图表中显示的任何模型，以便查看特定模型的详细公平信息。


[![模型比较 Fairlearn 仪表板](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>后续步骤

[了解有关模型公平的详细信息](concept-fairness-ml.md)

[查看 Azure 机器学习公平示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
