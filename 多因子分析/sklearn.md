
[sklearn](https://scikitlearn.com.cn/#_1)

Estimator 可训练的对象
Predictor 具备预测能力的对象（回归、分类等）
Transformer（转换器） 负责数据预处理、特征工程
Model Evaluator（评估模型性能

Pipeline（流水线）
串联多个处理步骤，自动调用 `fit()` & `transform()`


特点 
✅ `fit(X, y)`: 训练模型，**必须实现**  
✅ `get_params() / set_params()`: 获取/设置参数，支持自动调参  
✅ `BaseEstimator`: 统一管理参数


Transformer 主要用于 **数据预处理**（如归一化、缺失值填充、降维）。
![[Pasted image 20250218222942.png]]




在 `sklearn` 里，`Pipeline` 允许把多个 `Transformer` 和 `Estimator` 串联起来，自动执行 `fit()` 和 `transform()`。

