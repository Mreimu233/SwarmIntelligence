# Exudate segmentation in fundus images using an ant colony optimization approach 

**Authors:** Carla Pereira, Luís Gonçalves, Manuel Ferreira

发表于 Information Sciences

## 1. 引言

糖网病 (diabetic retinopathy, DR), 是“糖尿病视网膜病变”的简称, 糖尿病会引起视网膜血管失调. 病症的发生率随着患糖尿病时间增加而提高. 患有糖尿病十五年以上的病人中大约60%的病人眼部血管会受损. 因此糖网的早期筛查对于疾病预防有着重要的意义. 目前早期筛查的手段主要是通过眼底图像分析进行.

泄露是DR的早期特征, 由视网膜血管渗透所致, 泄露区域的分割对于诊断有着重要的作用. 目前已有的分割方法主要有: 阈值法, 形态学方法, 区域增长和监督方法. 

## 2. 方法

由于泄露区域与边界有高对比度, 因此通常使用前处理提取候选区域. 区域的边界信息是筛选假阳性的重要特征. 因此为了增强区域的边界信息, 本文提出使用蚁群优化算法 (ant colony optimization, ACO)进行边界增强. 

### 2.1 前处理

眼底图像为RGB彩色图像, 其中目标区域再绿色通道是最明显的, 因此首先提取图像的绿色通道. 然后应用中值滤波器对图像背景进行估计, 以移除背景信息. 为了使用阈值法提取候选区域, 需要对图像进行标准化, 然后在直方图中选取阈值进行分割得到候选区域, 至此前处理工作完成, 如下图所示.

![眼底图像](https://raw.githubusercontent.com/Hanke98/SwarmIntelligence/master/张建伟/images/1-1.jpg)

### 2.2 ACO 算法

ACO算法主要用于评估候选区域的边界强度(尖锐程度), 因此只需要设计迭代过程使得蚁群趋向于区域边界即可. 

假设有 $K$ 只蚂蚁在大小为 $M_1\times M_2$ 的子图 $I$ 中寻找最优解 (图像边界), ACO 迭代过程如下:

```pseudocode
Determine the heuristic information and initialize the resultant image Ires = 0
For each original 128 × 128 image window
	Randomly initialize the positions of the K ants and the pheromone matrix t(0).
	For the construction step index n = 1:N
		For the ant index k = 1:K
			Consecutively move the kth ant for L steps, according to the probabilistic transition matrix p(n) (with a size of M_1M_2 × M_1M_2).
			Local update of the pheromone matrix
		End For
		Global update of the pheromone matrix
	End For
	Assign pheromone matrix t(N) to the correspondent window on the resultant image
End For
```

该算法包含两个关键步骤: 

1. 概率转移矩阵 $p^{(n)}$ 
2. 信息素矩阵 $\tau^{(n)}$ 的更新

对于概率转移矩阵, 本文使用了 Dorigo et al. 的方法:

![Formula-1](https://raw.githubusercontent.com/Hanke98/SwarmIntelligence/master/张建伟/images/1-3.jpg)

上式表示 ACO 迭代中的第 $n$ 步第 $k$ 只蚂蚁从节点 $(l, m)$ 移动到 $(i, j)$ 的概率, 这个概率同时取决于信息素和启发式信息两个因素. $\Omega_{(l, m)}$ 表示与点 $(l, m)$ 相连节点的集合. $\eta_{i, j}$ 表示到达节点 $(i, j)$ 的启发式信息, 在迭代过程中式固定的, 计算公式为 $\eta_{i,j}=\frac1ZV_c(I_{i,j})$, 其中 $Z=\sum_{i=1:M_1}\sum_{j=1:M_2}V_c(I_{i,j})$ 是归一化因子,  而 $I_{i,j}$ 是图像中的像素密度值. $V_c(I_{i,j})$ 表示了图像的边界信息, 计算公式为 $V_c(I_{i,j})=f(\lvert I_{i-2,j-1}-I_{i+2,j+1}\rvert+\cdots)$ . 其中函数 $f(\cdot)$ 的参数为下图中关于 $I_{i,j}$ 对称两点的密度差之和, 而 $f$ 定义为 $f(x)=\lambda x^2, x\geq0$ .

![启发式信息](https://raw.githubusercontent.com/Hanke98/SwarmIntelligence/master/张建伟/images/1-2.jpg)

对于信息素的更新, 其中局部更新由每只蚂蚁释放信息素完成:

![Formula-2](https://raw.githubusercontent.com/Hanke98/SwarmIntelligence/master/张建伟/images/1-4.jpg)

整体更新为信息素的蒸发: $\tau^{(n)}=(1-\phi)\cdot\tau^{(n-1)}+\phi\cdot\tau^{(0)}$ , 其中 $\phi\in(0, 1])$ 表示信息素蒸发率.

### 2.3 泄漏检测

如果边界强度大于阈值 $th$, 则该候选区域被认为是正样本. 为了尽可能多的检出并减少假阳性, 该阈值范围取为 $th\in(0.005, 0.2)$ .

