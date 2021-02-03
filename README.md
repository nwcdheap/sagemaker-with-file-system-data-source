# 使用Amazon FSx for Lustre 和 Amazon EFS 作数据源加快 Amazon Sagemaker 训练

## 背景

在 SageMaker 中训练模型一般使用Amazon Simple Storage Service (以下简称S3) 作为存储配合使用。事实上SageMaker做模型训练时已经支持多种数据源，比如[ Amazon FSx for Lustre ](https://aws.amazon.com/fsx/lustre/)和 [Amazon Elastic File System](https://aws.amazon.com/efs/) (EFS) 。SageMaker通过直接读取存储在EFS或者FSx for Luster上的数据来加快训练模型时数据加载进度。

Amazon FSx for Luster是托管的高性能文件系统，针对诸如机器学习，分析和高性能计算等工作负载进行了优化，并且原生支持与S3集成，从而可以使用Lustre轻松处理S3中数据集。Amazon EFS为基于Linux的工作负载提供了一个简单，可扩展的弹性文件系统，旨在为数千个 Amazon EC2 实例提供大规模并行共享访问模式，也支持与AWS 服务和本地资源一起配合使用。

一般情况下，使用SageMaker做机器学习模型训练时需要提供S3存储桶的路径，用于存储训练数据以及输出模型。如果已经有历史数据位于文件系统比如EFS或者FSx for Luster，可以在SageMaker创建训练任务的时候直接定义EFS 或者FSx for Luster做数据源，同样的如果在数据预处理阶段需要在Sagemaker Jupyter Notebook上跟这部分数据交互，也可以直接将EFS挂载到Sagemaker Jupyter instance，然后以本地文件的方式访问。另一方面，在文件输入模式(File Mode)下将S3用作训练数据源时，必须在训练作业开始时将所有训练数据从S3下载到附加到训练实例的EBS卷中。分布式文件系统（例如适用于Luster的Amazon FSx或EFS）可以消除此下载步骤，从而加快了机器学习训练的整体速度。



## 文件说明

- Sagemaker-with-filesystem-CarsClassifier.ipynb : 定义训练任务的数据源为EFS或者Fsx文件系统
- DataGenerate.ipynb：从公开数据集生成recordio文件
- Mounting-file-system-Create.sh：挂载EFS到Sagemaker Notebook时的指令，也可以用作Sagemaker Notebook生命周期配置中启动脚本
- Mounting-file-system-Start.sh：Sagemaker Notebook生命周期配置中创建时脚本
- data_train.rec /data_val.rec：RecordIO数据集

