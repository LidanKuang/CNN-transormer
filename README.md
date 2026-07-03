# fNIRS-Transformer
## Transformer Model for Functional Near-Infrared Spectroscopy Classification
This work has been accepted for publication in the IEEE Journal of Biomedical and Health Informatics ( https://ieeexplore.ieee.org/document/9670659).

Our other work may also be useful to you：***A General and Scalable Vision Framework for Functional Near-Infrared Spectroscopy Classification***  ( https://ieeexplore.ieee.org/abstract/document/9828508).

##  1.  Abstract

Functional near-infrared spectroscopy (fNIRS) is a promising neuroimaging technology. The fNIRS classification problem has always been the focus of the brain-computer interface (BCI). Inspired by the success of Transformer based on self-attention mechanism in the fields of natural language processing and computer vision, we propose an fNIRS classification network based on Transformer, named **fNIRS-T**. We explore the spatial-level and channel-level representation of fNIRS signals to improve data utilization and network representation capacity. Besides, a preprocessing module, which consists of one-dimensional average pooling and layer normalization, is designed to replace filtering and baseline correction of data preprocessing. It makes fNIRS-T an end-to-end network, called **fNIRS-PreT**. 

功能性近红外光谱技术（fNIRS）是一种具有广阔前景的神经影像技术。fNIRS 分类问题一直是脑机接口（BCI）领域的研究焦点。受基于自注意力机制的 Transformer 模型在自然语言处理和计算机视觉领域成功应用的启发，我们提出了一种基于 Transformer 的 fNIRS 分类网络**fNIRS-T**。该网络通过探索 fNIRS 信号的空间层面和通道层面表示，有效提升了数据利用率和网络表征能力。此外，我们设计了由一维平均池化和层归一化组成的预处理模块，替代了传统数据预处理中的滤波和基线校正步骤，使 fNIRS-T 成为端到端网络**fNIRS-PreT**。


<div align='center'>
<img src = 'fig/model.png'>
</div>


## 2. Datasets and Preprocessing

We conduct experiments on three open-access datasets. For data preprocessing, we follow the original literature and code.

### 2.1.  Dataset A
Paper: [1], [2]

Dataset:  http://bnci-horizon-2020.eu/database/data-sets 


<div align='center'>
<img src = 'fig/Dataset A.png'>
</div>

### 2.2.  Dataset B

This is a hybrid EEG+fNIRS dataset, and we only use fNIRS data.

Paper: [3]

Dataset:  http://doc.ml.tu-berlin.de/hBCI 

Github:  https://github.com/JaeyoungShin/hybrid-BCI 

### 2.3.  Dataset C
Paper: [4]

Dataset: https://doi.org/10.6084/m9.figshare.9783755.v1 

Github: https://github.com/JaeyoungShin/fNIRS-dataset 

### 2.4. Data Segmentation

For Dataset A, B, and C, the total number of trials are 348, 1740, and 2250, respectively.  Data segmentation is crucial for deep neural networks to classify fNIRS signals. For sampling points of the fNIRS channel, ***start*** and ***end*** represent the start and end of the data split, respectively. "**2**" means two chromophores: HbO and HbR.数据分割

对于数据集 A、B 和 C，试验次数分别为 348、1740 和 2250。数据分割对深度神经网络分类 fNIRS 信号至关重要。对于 fNIRS 通道的采样点，**start**和**end**分别表示数据分割的起点和终点。“**2**” 表示两种发色团：HbO 和 HbR。

| Dataset |           (*start*, *end*)           | Size (trial, channel*2, *end-start*) |
| :-----: | :----------------------------------: | :----------------------------------: |
|    A    | (0, 140) for MA; (160, 300) for Rest |           (348, 104, 140)            |
|    B    |              (100, 300)              |           (1740, 72, 200)            |
|    C    |              (20, 276)               |           (2250, 40, 256)            |



## 3. Flooding Level

Inspired by piecewise decaying learning rates, we decay the flooding level at the specified epoch to obtain continuous regularization. 受分段衰减学习率启发，我们在指定周期对泛洪水平进行衰减以实现持续正则化。

| Dataset |   Model    |      (epoch, flooding level)      |
| :-----: | :--------: | :-------------------------------: |
|  **A**  |  fNIRS-T   |                 -                 |
|  **A**  | fNIRS-PreT |                 -                 |
|  **B**  |  fNIRS-T   | (1, 0.45), (30, 0.40), (50, 0.35) |
|  **B**  | fNIRS-PreT | (1, 0.40), (30, 0.38), (50, 0.35) |
|  **C**  |  fNIRS-T   | (1, 0.45), (30, 0.40), (50, 0.35) |
|  **C**  | fNIRS-PreT | (1, 0.45), (30, 0.40), (50, 0.35) |



## 4. Code

 ***scripts***:  The  directory contains conversion code for the three datasets. Although converting to *.xls* format increases read time and dataset storage space, it is convenient to visualize fNIRS signals. For fNIRS-PreT, the ***filtering*** and ***baseline correction*** codes (in ***B_mat2xls.m*** and ***C_mat2xls.m***) need to be ***manually*** disabled or annotated.

***KFold_Train.py*** :  K-fold cross-validation to train the models.

***KFold_ACC.py*** :  Calculate the average K-fold cross-validation accuracy.

***LOSO_Train.py*** :  Leave-one-subject-out cross-validation (LOSO-CV) to train the models.

***LOSO_Results.py*** :  Evaluate experimental results.

***LOSO_Split.py*** :  One subject's data is used as the test set, and the rest is used as the training set.

***model.py*** :  Code for fNIRS-T and fNIRS-PreT.

***dataloader.py*** :  Load the specified dataset.

4.1 Extract datasets A, B, and C using the mat files from the scripts folder. You will need to download the BBCI library from the dataset link. For fNIRS-PreT, the ***filtering*** and ***baseline correction*** codes (in ***B_mat2xls.m*** and ***C_mat2xls.m***) need to be ***manually*** disabled or annotated.

4.2 Run ***KFold_Train.py*** to perform KFold-CV training. Run ***KFold_ACC.py*** to obtain results.

4.3 Run ***LOSO_Train.py*** to execute LOSO-CV training. Run ***LOSO_Results.py*** to obtain results.

You need to specify the dataset, model, and dataset path before training.

scripts: 该目录包含三个数据集的转换代码。尽管转换为 .xls 格式会增加读取时间和数据集存储空间，但它便于可视化 fNIRS 信号。对于 fNIRS-PreT，需要手动禁用或注释滤波和基线校正代码（在 B_mat2xls.m 和 C_mat2xls.m 中）。

KFold_Train.py: 使用 K 折交叉验证训练模型。

KFold_ACC.py: 计算平均 K 折交叉验证准确率。

LOSO_Train.py: 使用留一被试交叉验证（LOSO-CV）训练模型。

LOSO_Results.py: 评估实验结果。

LOSO_Split.py: 将一个被试的数据用作测试集，其余数据用作训练集。

model.py: fNIRS-T 和 fNIRS-PreT 的代码。

dataloader.py: 加载指定的数据集。

4.1 使用 scripts 文件夹中的 mat 文件提取数据集 A、B 和 C。您需要从数据集链接下载 BBCI 库。对于 fNIRS-PreT，需要手动禁用或注释滤波和基线校正代码（在 B_mat2xls.m 和 C_mat2xls.m 中）。

4.2 运行 KFold_Train.py 进行 K 折交叉验证训练。运行 KFold_ACC.py 获取结果。

4.3 运行 LOSO_Train.py 执行留一被试交叉验证训练。运行 LOSO_Results.py 获取结果。

在训练之前，您需要指定数据集、模型和数据集路径。

```
    # Select dataset
    dataset = ['A', 'B', 'C']
    dataset_id = 0
    print(dataset[dataset_id])

    # Select model
    models = ['fNIRS-T', 'fNIRS-PreT']
    models_id = 0
    print(models[models_id])

    # Select the specified path
    data_path = 'data'
```



## 5. Citation

If you found the study useful for you, please consider citing it.
```
@ARTICLE{Wang2022Transformer,  
author  = {Wang, Zenghui and Zhang, Jun and Zhang, Xiaochu and Chen, Peng and Wang, Bing},  
journal = {IEEE Journal of Biomedical and Health Informatics},   
title   = {Transformer Model for Functional Near-Infrared Spectroscopy Classification},   
year    = {2022},  
volume  = {26},  
number  = {6},  
pages   = {2559-2569},  
doi     = {10.1109/JBHI.2022.3140531}}
```

Our other work:
```
@ARTICLE{Wang2022VisionfNIRSFramework,
author  = {Wang, Zenghui and Zhang, Jun and Xia, Yi and Chen, Peng and Wang, Bing},
journal = {IEEE Transactions on Neural Systems and Rehabilitation Engineering}, 
title   = {A General and Scalable Vision Framework for Functional Near-Infrared Spectroscopy Classification}, 
year    = {2022},
volume  = {30},
number  = {},
pages   = {1982-1991},
doi     = {10.1109/TNSRE.2022.3190431}}
```


## References

[1] G. Bauernfeind, R. Scherer, G. Pfurtscheller, and C. Neuper, “Single-trial classifification of antagonistic oxyhemoglobin responses during mental arithmetic,” *Med. Biol. Eng. Comput.*, vol. 49, no. 9, pp. 979–984, 2011.

[2] G. Pfurtscheller, G. Bauernfeind, S. C. Wriessnegger, and C. Neuper,“Focal frontal (de) oxyhemoglobin responses during simple arithmetic,” *Int. J. Psychophysiol.*, vol. 76, no. 3, pp. 186–192, 2010.

[3] J. Shin, A. von Luhmann, B. Blankertz, D.-W. Kim, J. Jeong, H.-J. Hwang, and K.-R. Muller, “Open access dataset for EEG+NIRS single-trial classifification,” *IEEE Trans. Neural Syst. Rehabil. Eng.*, vol. 25, no. 10, pp. 1735–1745, 2017.

[4] S. Bak, J. Park, J. Shin, and J. Jeong, “Open-access fNIRS dataset for classifification of unilateral finger-and foot-tapping,” *Electronics*, vol. 8, no. 12, p. 1486, 2019.

```
def Load_Dataset_A(data_path, model):
    """
    Load Dataset A. Since the dataset does not provide code, we follow the original literature to preprocess the data.

    Args:
        data_path (str): dataset path.
        model (str): 'fNIRS-T' or 'fNIRS-PreT'. fNIRS-T uses preprocessed data, and fNIRS-PreT uses raw data.

    Returns:
        feature : fNIRS signal data.
        label : fNIRS labels.
    """
    assert model in ['fNIRS-T', 'fNIRS-PreT'], ''' The model parameter is 'fNIRS-T' or 'fNIRS-PreT' '''
    feature = []
    label = []
    for sub in range(1, 9):
        if sub >= 4:
            trial_num = 4
        else:
            trial_num = 3
        for i in range(1, trial_num + 1):
            times = i
            path = data_path + '/s' + str(sub) + '/s' + str(sub) + str(times) + '_hb.xls'
            hb = pd.read_excel(path, header=None)
            path = data_path + '/s' + str(sub) + '/s' + str(sub) + str(times) + '_trial.xls'
            trial = pd.read_excel(path, header=None)
            path = data_path + '/s' + str(sub) + '/s' + str(sub) + str(times) + '_y.xls'
            y = pd.read_excel(path, header=None)

            hb = np.array(hb)
            trial = np.array(trial)
            # 1:MA, 2:REST
            y = np.array(y)

            HbO = hb[:, 0:52]
            HbR = hb[:, 52:104]

            # fNIRS-T uses preprocessed data
            if model == 'fNIRS-T':
                b, a = signal.butter(4, 0.018, 'lowpass')
                HbO = signal.filtfilt(b, a, HbO, axis=0)
                HbR = signal.filtfilt(b, a, HbR, axis=0)
                b, a = signal.butter(3, 0.002, 'highpass')
                HbO = signal.filtfilt(b, a, HbO, axis=0)
                HbR = signal.filtfilt(b, a, HbR, axis=0)

            HbO = HbO.transpose((1, 0))
            HbR = HbR.transpose((1, 0))

            MA_tr = []
            REST_tr = []
            for i in range(y.shape[0]):
                if y[i, 0] == 1:
                    MA_tr.append(trial[i, 0])
                else:
                    REST_tr.append(trial[i, 0])

            HbO_MA = []
            HbO_BL = []
            HbR_MA = []
            HbR_BL = []

            for i in range(int(len(MA_tr))):
                tr = MA_tr[i]
                HbO_MA.append(HbO[:, tr: tr + 140])
                HbR_MA.append(HbR[:, tr: tr + 140])
                HbO_BL.append(HbO[:, tr + 160: tr + 300])
                HbR_BL.append(HbR[:, tr + 160: tr + 300])

            # fNIRS channels = 52, sampling points = 140
            HbO_MA = np.array(HbO_MA).reshape((-1, 1, 52, 140))
            HbO_BL = np.array(HbO_BL).reshape((-1, 1, 52, 140))
            HbR_MA = np.array(HbR_MA).reshape((-1, 1, 52, 140))
            HbR_BL = np.array(HbR_BL).reshape((-1, 1, 52, 140))

            HbO_MA = np.concatenate((HbO_MA, HbR_MA), axis=1)
            HbO_BL = np.concatenate((HbO_BL, HbR_BL), axis=1)

            for i in range(HbO_MA.shape[0]):
                feature.append(HbO_MA[i, :, :, :])
                feature.append(HbO_BL[i, :, :, :])

                label.append(0)
                label.append(1)

        print(str(sub) + '  OK')

    feature = np.array(feature)
    label = np.array(label)
    print('feature', feature.shape)
    print('label', label.shape)

    return feature, label
```

```
def Load_Dataset_B(data_path, start=100, end=300):
    """
    Load Dataset B

    Args:
        data_path (str): dataset path.
        start (int): start sampling point, default=100.
        end (int): end sampling point, default=300.

    Returns:
        feature : fNIRS signal data.
        label : fNIRS labels.
    """
    feature = []
    label = []
    for sub in range(1, 30):
        name = data_path + '/' + str(sub) + '/' + str(sub) + '_oxy.xls'
        oxy = pd.read_excel(name, header=None, sheet_name=None)
        name = data_path + '/' + str(sub) + '/' + str(sub) + '_deoxy.xls'
        deoxy = pd.read_excel(name, header=None, sheet_name=None)
        name = data_path + '/' + str(sub) + '/' + str(sub) + '_desc.xls'
        desc = pd.read_excel(name, header=None)

        HbO = []
        HbR = []
        for i in range(1, 61):
            name = 'Sheet' + str(i)
            HbO.append(oxy[name].values)
            HbR.append(deoxy[name].values)

        # (60, 350, 36) --> (60, 36, 350)
        HbO = np.array(HbO).transpose((0, 2, 1))
        HbR = np.array(HbR).transpose((0, 2, 1))
        desc = np.array(desc)

        HbO_MA = []
        HbO_BL = []
        HbR_MA = []
        HbR_BL = []
        for i in range(60):
            if desc[i, 0] == 1:
                HbO_MA.append(HbO[i, :, start:end])
                HbR_MA.append(HbR[i, :, start:end])
            elif desc[i, 0] == 2:
                HbO_BL.append(HbO[i, :, start:end])
                HbR_BL.append(HbR[i, :, start:end])

        # (30, 36, 200) --> (30, 1, 36, 200)
        HbO_MA = np.array(HbO_MA).reshape((30, 1, 36, end-start))
        HbO_BL = np.array(HbO_BL).reshape((30, 1, 36, end-start))
        HbR_MA = np.array(HbR_MA).reshape((30, 1, 36, end-start))
        HbR_BL = np.array(HbR_BL).reshape((30, 1, 36, end-start))

        # (30, 2, 36, 200)
        HbO_MA = np.concatenate((HbO_MA, HbR_MA), axis=1)
        HbO_BL = np.concatenate((HbO_BL, HbR_BL), axis=1)

        for i in range(30):
            feature.append(HbO_MA[i, :, :, :])
            feature.append(HbO_BL[i, :, :, :])
            label.append(0)
            label.append(1)

        print(str(sub) + '  OK')

    feature = np.array(feature)
    label = np.array(label)

    print('feature ', feature.shape)
    print('label ', label.shape)

    return feature, label
```

```
if dataset[dataset_id] == 'A':
    flooding_level = [0, 0, 0]
    Subjects = 8
    if models[models_id] == 'fNIRS-T':
        feature, label = Load_Dataset_A(data_path, model='fNIRS-T')
    elif models[models_id] == 'fNIRS-PreT':
        feature, label = Load_Dataset_A(data_path, model='fNIRS-PreT')
elif dataset[dataset_id] == 'B':
    if models[models_id] == 'fNIRS-T':
        flooding_level = [0.45, 0.40, 0.35]
    else:
        flooding_level = [0.40, 0.38, 0.35]
    Subjects = 29
    feature, label = Load_Dataset_B(data_path)
```

```
if dataset[dataset_id] == 'A':
    if models[models_id] == 'fNIRS-T':
        net = fNIRS_T(n_class=2, sampling_point=sampling_points, dim=64, depth=6, heads=8, mlp_dim=64).to(device)
    elif models[models_id] == 'fNIRS-PreT':
        net = fNIRS_PreT(n_class=2, sampling_point=sampling_points, dim=64, depth=6, heads=8, mlp_dim=64).to(device)
elif dataset[dataset_id] == 'B':
    if models[models_id] == 'fNIRS-T':
        net = fNIRS_T(n_class=2, sampling_point=sampling_points, dim=64, depth=6, heads=8, mlp_dim=64).to(device)
    elif models[models_id] == 'fNIRS-PreT':
        net = fNIRS_PreT(n_class=2, sampling_point=sampling_points, dim=64, depth=6, heads=8, mlp_dim=64).to(device)
```
