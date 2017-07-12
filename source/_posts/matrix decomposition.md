---
title: 矩阵分解及其应用
date: 2017-04-06 22:23:00
tags: 线性代数
categories: 数学
---

预备
----

>*  定义1，给定一个由$n$维向量集合组成的集合$V$，如果集合$V$非空并且对于任意集合$V$中的向量，满足如下两个条件    
> 1) 若$a\in V,b\in V$ ,则$a+b\in V$；    
> 2) 若$a\in V,\lambda \in R$，则$\lambda a \in V$；    
> 则称集合$V$为向量空间

定义1说明在向量空间中，对向量加和运算和向量数乘运算封闭

> * 定义2，设$V$为向量空间，如果$r$个向量$a_{1},a_{2}，\cdots，a_{n} \in V$，并且满足    
> 1)$a_{1},a_{2}，\cdots，a_{n}$线性无关；    
> 2)$V$中任一向量都可由$a_{1},a_{2}，\cdots，a_{n}$线性标示；    
> 则称向量组$a_{1},a_{2}，\cdots，a_{n}$为向量空间$V$的一个基，$n$称为向量空间$V$的维数

给定向量空间$V$下的一个向量$x$，如果存在    
$$x=\lambda_{1}a_{1}+\lambda_{2}a_{2}+\cdots+\lambda_{n}a_{n} \tag{1}$$
 
则称数组$\lambda_{1},\lambda_{2},\cdots,\lambda_{n}$为向量$x$在基$a_{1},a_{2}，\cdots，a_{n}$下的坐标，向量$x$在基$a_{1},a_{2}，\cdots，a_{n}$下可以表示为    
$$x=[\lambda_{1}, \lambda_{2},\cdots,\lambda_{n}] \tag{2}$$
  
> * 定义3，自然基，在$n$维向量空间中取单位坐标向量组$e_{1},e_{2},\cdots e_{n}$作为基，向量空间中的任一向量可以表示为$x=x_{1}e_{1} + x_{2}e_{2}+ \cdots + x_{n}e_{n}$,称$e_{1},e_{2},\cdots e_{n}$为自然基，向量$x=[x_{1},x_{2},\cdots,x_{n}]$

在定义3中的自然基，任何两个基向量的内积为0，称基向量之间正交，每个基的模为1，称为单位向量。对一个基，如果任意一个向量为单位向量且任意两个向量正交，则称为规范正交基。    

> * 定义4，正交矩阵，如果一个$n$阶方阵满足$A^TA=E(即A^{-1}=A^T)$，则称方阵$A$为正交矩阵    
 
从正交矩阵的定义，可以导出正交矩阵行矩阵或列矩阵两两正交且模为1，假设$A$列矩阵为    
$$A^{T}A=\begin{bmatrix}
x_{1}^{T}\\ 
x_{2}^{T}\\ 
\vdots\\ 
x_{n}^{T}
\end{bmatrix}
\begin{bmatrix}
x_{1} &x_{2}  & \cdots  & x_{n} 
\end{bmatrix}
=
\begin{bmatrix}
1 & 0 &  0& 0\\ 
0 & 1 &  0& 0\\ 
\vdots &  \vdots& \vdots & \vdots\\ 
 0& 0 & 0 & 1
\end{bmatrix}
\Longrightarrow
x_{i}^{T}x_{j}=\delta_{ij}
=
\left\{\begin{matrix}
1,如果i=j\\ 
0,否则i\ne j
\end{matrix}\right. \tag{3}
$$


> * 定义5，称变换$T$是线性的，如果    
>  1、对于$T$定义域内的一切$u,v$，$T(u+v)=T(u)+T(v)$    
>  2、对一切$u$和标量c，$T(cu)=cT(u)$     
> * 定义6，矩阵变换，称一个矩阵乘以一个向量为一个矩阵变换，记作$x\to Ax$

一个变换就是一个映射，由定义可以看出，矩阵变换$x\to Ax$是一个线性变换

矩阵的几何意义
---------
我们知道对于一个向量，给定一组基下就会有一个坐标唯一描述这个向量，假如有两个基描述这个向量，这两个基有什么关系呢？假设向量空间$V$有两组基$a_{1},a_{2},\cdots,a_{n}和b_{1},b_{2},\cdots,b_{n}$，则基$b$可以由$a$来表示，即有
$$\left\{\begin{matrix}
b_{1}=p_{11}a_{1}+p_{21}a_{2}+\cdots +p_{n1}a_{n}\\ 
b_{2}=p_{12}a_{1}+p_{22}a_{2}+\cdots +p_{n2}a_{n}\\ 
\cdots \\ 
b_{n}=p_{1n}a_{1}+p_{2n}a_{2}+\cdots +p_{nn}a_{n}
\end{matrix}\right. \tag{4}$$

写成矩阵形式，则有
$$\begin{bmatrix}
b_{1}\\ 
b_{2}\\ 
\vdots\\ 
b_{n}
\end{bmatrix}
=
\begin{bmatrix}
p_{11} & p_{21} & \cdots & p_{n1}\\ 
p_{12} & p_{22} & \cdots & p_{n2}\\ 
\vdots & \vdots & \vdots & \vdots\\ 
p_{1n} & p_{n2} & \cdots & p_{nn}
\end{bmatrix}
\begin{bmatrix}
a_{1}\\ 
a_{2}\\ 
\vdots\\ 
a_{n}
\end{bmatrix}
=P^{T}
\begin{bmatrix}
a_{1}\\ 
a_{2}\\ 
\vdots\\ 
a_{n}
\end{bmatrix} \tag{5}
$$

称矩阵$P$为从基$a$到基$b$的过渡矩阵,因为基是线性无关的，因此矩阵$P$是可逆的，也就是说，两个基之间可以通过一个矩阵来转换。

> * 定理1，设$V_{n}$中的一个向量$r$,在基$a_{1},a_{2},\cdots,a_{n}$下的坐标为$[ x_{1},x_{2},\cdots,x_{n}]$，在基$b_{1},b_{2},\cdots,b_{n}$下的坐标为$[x_{1}',x_{2}',\cdots,x_{n}']$，矩阵$P$为从基$a$到基$b$的过渡矩阵，则有坐标变换公式
> $$\begin{bmatrix}
x_{1}\\ 
x_{2}\\ 
\vdots\\ 
x_{n}
\end{bmatrix}
=
P
\begin{bmatrix}
x_{1}'\\ 
x_{2}'\\ 
\vdots\\ 
x_{n}'
\end{bmatrix} \tag{6}$$

来看一个特殊的情况，假设基向量$a$为自然基，则有

$$\begin{bmatrix}
b_{1}\\ 
b_{2}\\ 
\vdots\\ 
b_{n}
\end{bmatrix}
=
\begin{bmatrix}
p_{11} & p_{21} & \cdots & p_{n1}\\ 
p_{12} & p_{22} & \cdots & p_{n2}\\ 
\vdots & \vdots & \vdots & \vdots\\ 
p_{1n} & p_{n2} & \cdots & p_{nn}
\end{bmatrix}
\begin{bmatrix}
a_{1}\\ 
a_{2}\\ 
\vdots\\ 
a_{n}
\end{bmatrix}
=P^{T} \tag{7}
$$

因此，一个向量在标准坐标系下坐标为$x=[x_{1},x_{2},\cdots,x_{n}]$,在基$b$下的坐标为$x'=x^{T}[b_{1}^{T},b_{2}^{T},\cdots,b_{n}^{T}]$,公式也可以写作
$$EX^{T}=BX'^{T} \tag{8}$$
也就是矩阵乘法$AX=b$可以认为是在标准坐标系下坐标是$b$，在$A$组成的基下的坐标是$x$

给定一个矩阵$A$,矩阵$Ax$可以看成一个基变换，如果认为基变换前后是同一个基，则可以认为$Ax$是对向量进行角度及长度的变换，矩阵可以认为是一个线性变换，也就是变换基坐标和变换向量是相对的

下面用二维图示进行说明，给定一个二维对角矩阵
$$M=\begin{bmatrix}
3 & 0\\ 
0 & 1
\end{bmatrix} \tag{9}$$
从几何上讲，$M$是将二维平面上的点$(x,y)$经过线性变换到另外一个点的变换矩阵

$$M=\begin{bmatrix}
3 & 0\\ 
0 & 1
\end{bmatrix}
\begin{bmatrix}
x\\ 
y
\end{bmatrix}
=
\begin{bmatrix}
3x\\ 
y
\end{bmatrix} \tag{10}
$$
变换的效果如下图所示，变换后的平面仅仅是沿 X 水平方面进行了拉伸3倍，垂直方向是并没有发生变化。
<center>
![](/img/1.jpg)
</center>
现在看下矩阵

$$M=\begin{bmatrix}
2 & 1\\ 
1 & 2
\end{bmatrix}
\begin{bmatrix}
x\\ 
y
\end{bmatrix}
=
\begin{bmatrix}
2x+y\\ 
x+2y
\end{bmatrix} \tag{11}
$$
这个矩阵产生的变换效果如下图所示
<center>
![](/img/2.jpg)
</center>
 这种变换效果看起来非常的奇怪，在实际环境下很难描述出来变换的规律 ( 这里应该是指无法清晰辨识出旋转的角度，拉伸的倍数之类的信息)。还是基于上面的对称矩阵，假设我们把左边的平面旋转45度角，然后再进行矩阵$M$的线性变换，效果如下图所示：
<center>
![](/img/3.jpg)
</center>

看起来是不是有点熟悉？ 对的，经过 M 线性变换后，跟前面的对角矩阵的功能是相同的，都是将网格沿着一个方向拉伸了3倍。

从上边看出，一个矩阵可以对向量进行旋转和拉伸作用，有没有矩阵对向量仅仅进行旋转变化，不进行拉伸呢？正交变换就仅仅对向量进行旋转，不进行拉伸

> * 定理二，若$P$为正交矩阵，则线性变换$y=Px$称为正交变换，并且变换后向量大小不变

向量是否拉伸可以通过向量的2阶范数来反映，即

$$\parallel y \parallel =\sqrt{y^{T}y}=\sqrt{x^{T}P^{T}Px}=\sqrt{x^{T}x}=\parallel x \parallel \tag{12}$$


下面以一个图示来演示正交变换,假设二维空间中的一个向量$OA$，它在标准坐标系也即$e1、e2$表示的坐标是中表示为$(a,b)^{T}$，现在把它用另一组坐标$e1'、e2'$表示为$(a',b')^{T}$，存在矩阵$U$使得$U(a',b')^{T}=(a,b)^{T}$

<center>
![](/img/4.jpg)
</center>

容易求出$U$，其中$e_{1}'=[\cos\theta,\sin\theta]^{T}，e_{2}'=[-\sin\theta,\cos\theta]$，因此
$$U=\begin{bmatrix}
\cos\theta & -\sin\theta\\ 
\sin\theta & \cos\theta 
\end{bmatrix} \tag{13}$$

下图展示了基变换和向量方向变换的等价
<center>
![](/img/5.jpg)
</center>

有没有矩阵变换，仅仅对向量进行拉伸，不改变方向呢，这就是方阵的特征值和特征向量

方阵的特征值及特征向量
---------------------
> * 定义7，设$A$是$n$阶矩阵，如果$\lambda$和$n$维非零列向量$x$满足如下关系
  $$Ax=\lambda x \tag{14}$$
  则称$\lambda$为矩阵$A$的特征值，向量$x$为矩阵$A$的特征向量

从定义看出，矩阵$A$仅仅对特征向量进行了$\lambda$的拉伸，假设所有的特征值组成可逆矩阵$Q$，特征值组成对角矩阵$\Sigma$，则矩阵$A$可以分解为
$$A=Q\Sigma Q^{-1} \tag{15}$$
以上称为矩阵的特征值分解


>* 定义8，矩阵的相似性，设$A,B$都是$n$阶矩阵，如果存在可逆矩阵$P$,使得，
$$P^{-1}AP=B \tag{16}$$
则称$A,B$相似

> * 定理三，如果$n$阶矩阵$A,B$相似，则他们有相同的特征多项式，从而有相同的特征值

容易证明 

$$\left | B-\lambda E \right | = \left | P^{-1}AP-P^{-1}(\lambda E)P \right | = \left | P^{-1}(A-
\lambda E)P \right | = \left | P^{-1} \right | \left | A-
\lambda E \right | \left | P \right | = \left | A-\lambda E \right | \tag{17}$$

因此，如果一个矩阵$A$跟一个上(下)三角矩阵或者对角阵相似，则对角线的$n$个值就是$A$的特征值。同理，对于$n$阶矩阵$A$，能否找到一个可逆矩阵$P$使得$P^{-1}AP=\Lambda$，其中$\Lambda$为对角矩阵，如果存在，则称方阵$A$的对角化分解。

首先来看如果存在可逆矩阵$P$，它会满足什么条件，根据定义，把可逆矩阵写成列向量，即$P=(p_{1},p_{2},\cdots,p_{n})$ ，根据$P^{-1}AP=\Lambda$ ,有$AP=\Lambda P$,既有


$$A(p_{1},p_{2},\cdots,p_{n})=(p_{1},p_{2},\cdots,p_{n})\begin{bmatrix}
\lambda_{1} &  &  & \\ 
 & \lambda_{2} &  & \\ 
 &  & \ddots   & \\ 
 &  &  &  \lambda_{n}
\end{bmatrix}
=(\lambda_{1}p_{1},\lambda_{2}p_{2},\cdots,\lambda_{n}p_{n}) \tag{18}$$

即，如果矩阵$A$能对角化，则对角化$\Lambda$为特征值组成的对角矩阵，可逆矩阵为特征向量组成的矩阵，因此特征值分解可以实现矩阵的对角化分解。

> * 定理四，一个$n$矩阵能够对角化的充分必要条件是它有$n$个线性无关的特征向量。特别的，对于对称矩阵$A(即A^{T}=A)$，它的任意两个不同特征值对应的特征向量正交；并且必有正交阵$P$，使
$$P^{-1}AP=P^{T}AP=\Lambda \tag{19}$$
$\Lambda$为特征值组成的对角化矩阵

####矩阵相似的意义####
我们知道矩阵对应一个在给定基下的线性变换，如果同一个线性变换，在基$a$下的变换矩阵为$A$，在$b$下的变换矩阵为B，则$A,B$有什么关系呢?
根据基变换的性质，基$b$可以由基$a$和一个矩阵乘积来表示，即$b=aP$，则对于线性变换$L$有

$$\begin{array}{l}
L(b)=L(a)P \\ \Rightarrow
{[ L(b_1) \cdots L(b_n) ] = [ L(a_1) \cdots L(a_n) ]\; P }\\ \Rightarrow
{(b_1\cdots b_n)B=(a_1\cdots a_n)AP}\\ \Rightarrow 
{(a_1\cdots a_n)PB=(a_1\cdots a_n)AP}\\ \Rightarrow {PB=AP}
\\ \Rightarrow {B=P^{-1}AP}
\end{array} \tag{20}$$

也就是说，两个矩阵相似，表示这两个矩阵是在不同的基表示下的同一个线性变换，特别，可逆矩阵$P$为两个基的过渡矩阵



####特征值及特征向量的意义#####
根据矩阵相似的意义，方阵$A$的特征值分解可以理解两个不同基下的同一个变换，其中特征向量组成的矩阵是两个变换的过渡矩阵，特别的，假设其中一个基是自然基，则另一个基为$P$或者$P^{-1}$，假设对角阵对应的基为自然基，则方阵$A$对应的基为$P$，也就是向量$A$的变换是对自然基组成的向量做特征值大小的缩放。    

从线性空间的角度看，在一个定义了内积的线性空间里，对一个$n$阶对称方阵进行特征分解，就是产生了该空间的$n$个标准正交基，然后把矩阵投影到这$n$个基上。$n$个特征向量就是$n$个标准正交基，因为对称方阵的特征向量是一个正交矩阵，所以，N个特征向量就是N个标准正交基，而特征值的模则代表矩阵在每个基上的投影长度。而特征值的模则代表矩阵在每个基上的投影长度。特征值越大，说明矩阵在对应的特征向量上的方差越大，功率越大，信息量越多。

对于小方阵，特征值及特征向量可以通过定义来求得。下面来看怎么求特征值及特征向量，由定义式可有
$$(A-\lambda E)x=0 \tag{21}$$
这是一个$n$个未知数$n$个方程的齐次线性方程组，因它有非零解的条件是特征多项式为$0$，即
$$\left |A-\lambda E  \right |=0 \tag{22}$$

假设$n$阶方阵$A$的特征值分别为$\lambda_{1},\lambda_{2},\cdots,\lambda_{n}$，特征值有如下性质    
* 1) $\lambda_{1}+\lambda_{2}+\cdots+\lambda_{n} = a_{11}+a_{22}+\cdots+a_{nn}$    
* 2) $\lambda_{1}\lambda_{2}\cdots\lambda_{n}=\left |A\right|$

但对于大型的方阵，可以通过矩阵的QR分解来求。

矩阵的奇异值分解
---------------

根据特征值分解的描述，特征值的大小可以看成矩阵在某一方向的拉伸大小，如果矩阵在某些方向上的拉伸比较小，也就是特征值比较小，如果不考虑这些特征值的影响，矩阵的变换作用影响不大，因此可以用特征值分解来降维。    

上面讲的特征值分解是针对方阵的，那么对于普通的矩阵，会有这样的分解吗？其实，对于任何矩阵矩阵$A_{mn}$，存在一个正交矩阵$U_{mm}$，一个正交矩阵$V_{nn}$，一个对角矩阵$\Lambda_{mn}$，使得

$$A_{mn}=U_{mm}\Lambda_{mn}V^{T}_{nn} \tag{23}$$
成立，这就是矩阵的奇异值分解。

首先对于任何$A_{mn}$，都有$A^{T}A$是对称矩阵，根据对称矩阵的特征分解，有
$$A^{T}A=Q\Sigma Q^{-1} \tag{24}$$

假设我们得到一组正交基${v_1,v_2,\cdots,v_n}$，即$A^{T}Av_i = \lambda_iv_i$，下面看对正交基进行$A$线性变换的向量组${Av_1,Av_2,\cdots,Av_n}$


$$(Av_{i},Av_{j})\\
=(Av_{i})^{T}(Av_{j})\\
=v^{T}_{i}A^{T}Av_{j}\\
=v^{T}_{i}(\lambda_{j}v_{j})\\
=\lambda_{j}v^{T}_iv_{j}\\
=0 \tag{25}
$$


也就是经过矩阵$A$线性变换后的基${Av_1,Av_2,\cdots,Av_n}$也是正交的，将其标准化，即将$Av_{i}$变换为

$$u_{i}=\frac{Av_{i}}{\left | Av_{i} \right | } = \frac{1}{\sqrt{\lambda_{i}}}Av_{i} \tag{26}$$

如果令$\delta_{i} = \sqrt{\lambda_{i}}$，称$\delta_{i}$为奇异值，则有$Av_{i} = \sqrt{\lambda_{i}}u_{i}$

假设矩阵$A$的秩为$rank(A)=r$，则$rank（A^{T}A）=rank(AA^{T})=rank(A)=r$，则对称矩阵$A^{T}A$有$r$个非零的特征值，因此对于这$r$个特征值对应的特征向量都有$Av_{i}\ne0$,而为$0$的特征值对应的特征向量都有$Av_{i}=0$

因此，由$u_1,u_2,\cdots,u_r$组成的基是一个正交基，如果$r<m$，通过正交化将其扩展为$R^{m}$上的正交基

$$AV=A(v_{1},v_{2},\cdots,v_{n})\\
=(Av_{1},Av_{2},\cdots,Av_{r},0,0,\cdots,0)\\
=(\delta_{1}u_{1},\delta_{2}u_{2},\cdots,\delta_{r}u_{r},0,0,\cdots.0)
=U\Sigma\\
\Longrightarrow A=U\Sigma V_{T} \tag{27}
$$

$v_{i}$是对称矩阵$A^{T}A$的特征向量，称为$A$的右奇异向量，实际上$u_{i}$是$AA^{T}$的特征向量，称为$A$的左奇异向量。

写成分块形式如下

$$A=\left[
\begin{array}{ccc|ccc}
	u_{1} & \cdots & u_{k} & u_{k+1} & \cdots & u_{m}
\end{array}
\right]
\left[
\begin{array}{ccc|c}
\sigma_{1} &  & &\\
 & \ddots & & 0\\
 &  &  \sigma_{k} & \\ \hline
 &   & &   \\
 & 0 & & 0 \\
 &   & &   \\
\end{array}
\right]
\begin{bmatrix}
v^{T}_{1} \\ 
\vdots \\ 
v^{T}_{k}\\ \hline
v^{T}_{k+1}\\
\vdots \\
v^{T}_{n}
\end{bmatrix}\\
\Longrightarrow
A=
\begin{bmatrix}
u_{1} & \cdots & u_{k}  
\end{bmatrix}
\begin{bmatrix}
\sigma_{1} & & \\
& \ddots & \\
& & \sigma_{k}	
\end{bmatrix}
\begin{bmatrix}
v^{T}_{1}\\
\vdots\\
v^{T}_{k}	
\end{bmatrix}
+
\begin{bmatrix}
u_{k+1} & \cdots & u_{m}  
\end{bmatrix}
\begin{bmatrix}
& & \\
& 0 & \\
& & \\	
\end{bmatrix}
\begin{bmatrix}
v^{T}_{k+1}\\
\vdots\\
v^{T}_{n}	
\end{bmatrix}\\
\Longrightarrow
A=
\begin{bmatrix}
u_{1} & \cdots & u_{k}  
\end{bmatrix}
\begin{bmatrix}
\sigma_{1} & & \\
& \ddots & \\
& & \sigma_{k}	
\end{bmatrix}
\begin{bmatrix}
v^{T}_{1}\\
\vdots\\
v^{T}_{k}	
\end{bmatrix} \tag{28}
$$

奇异值的意义跟特征值的意义相似，正交基$U_{mm}$表示原空间的正交基，正交基$V_{nn}$表示目标空间的正交基，而对角矩阵$\Lambda$表示变换的重要性

奇异值的应用
-----------

####数据压缩
从分解公式看出，如果矩阵的秩$r$远小于$m,n$，则矩阵可以压缩标示而且又不失真，特别的如果忽略小的奇异值，还能对数据进行压缩，比如一张图像就是一个大的二维矩阵，如果是彩色的就是三个大矩阵，比如一幅$1000*1000$的图像$A$，存储就需要$1000000$个像素了，如果矩阵的秩是$r$，则总共需要存储的数据是$1000*r+r*r+1000+r$,如果考虑$k$大的特征值，只需要$1000*k+k*k+1000+k$

下面给出一个图像进行svd分解后选取不同的特征值进行图像还原的效果
<center>
![](/img/lena.jpg)
![](/img/6.jpg)
![](/img/pic.jpg)
![](/img/7.jpg)
</center>


####求解PCA
####隐语义分析(Latent Semantic Analysis)
在文本处理中，潜在语义分析（LSA）假设在词项和文档之间有一个潜在语义层，词汇和语义产生关联，文档也和语义产生关联。这样通过语义概念，可以将词项和文档放在一个语义空间中观察分析。比如文档分类问题，隐含的主题可以理解为文档属于什么主题的文档(体育，娱乐，科技等)，LSA使用SVD分解来求解，下面以一个例子来说明，假设有3个文档
<center>
![](/img/8.jpg)
</center>

查询词：gold silver truck
首先把文档分词组织成一个二维矩阵，查询词组织成一个向量，如下
<center>
![](/img/9.jpg)
</center>

对矩阵$A$进行SVD分解，结果如下
<center>
![](/img/10.jpg)
</center>

只保留2个奇异值，则变为
<center>
![](/img/11.jpg)
</center>
这样可以求得三个文档在语义空间上的映射为
<center>
d1(-0.4945, 0.6492)    
d2(-0.6458, -0.7194)    
d3(-0.5817, 0.2469)    
</center>

将查询词映射到语义空间

<center>
![](/img/12.jpg)
</center>

这样就可以用相似性来度量查询词跟文档的相关性了
<center>
![](/img/13.jpg)
</center>
