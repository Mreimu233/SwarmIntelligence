# Swarm intelligence in clustering

## Papers

### Clustering

- A clustering algorithm based on swarm intelligence (CSI)[^1]

  "simulates a process performed by ants to clean their nest by grouping dead bodies"

  CSI 把一个 M x M 的网格，和 N 个 d-维向量（对象）作为聚类的输入。每一只蚂蚁可以看到与其相邻位置的所有向量。所有蚂蚁可以在网格间移动、拿起一个向量、携带或放下它。

  初始下，蚂蚁和向量被随机放在网格的任何位置，蚂蚁进行随机游走。如果一只没有携带向量的蚂蚁看到一个向量i，蚂蚁拿起i的概率是i与周围向量的不相似度；如果一个携带了向量i的蚂蚁看到一个空网格，蚂蚁放下i的概率是i与周围向量的相似度。

### Document Clustering

- CSIM[^2], based on CSI and k-means

  two-stage algorithm: 

  1. 通过CSI算法将相似的文档放到了相近的位置，并得到初始的k个中心; 
  2. 用k-means聚类。


[^1]: Bin, W. and Zhongzhi, S. (2001) ‘A clustering algorithm based on swarm intelligence’, Proc. International Conferences on Info-tech & Info-net, Vol. 3, pp.58–66. 
[^2]: Bin, W., Yi, Z., Shaohui, L. and Shi, Z. (2002) ‘CSIM: a document clustering algorithm based on swarm intelligence’, Proc. of the 2002 Congress on Evolutionary Computation CEC02,
Vol. 1, pp.477–482. 
