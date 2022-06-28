# 古典密码学

## 密码体制

一个密码体制是满足以下条件的五元组 $(\mathcal P, \mathcal C, \mathcal K, \mathcal E, \mathcal D)$。

1. $\mathcal P$ 表示所有可能的明文组成的有限集。
2. $\mathcal C$ 表示所有可能的密文组成的有限集。
3. $\mathcal K$ 代表密钥空间，是由所有可能的密钥组成的有限集。
4. 对任意的 $K \in \mathcal K$，都存在一个加密法则 $e_K \in \mathcal E$ 和对应的解密法则 $d_K \in \mathcal D$，并且对每一个 $e_K: \mathcal P \rightarrow \mathcal C$ 和 $d_K: \mathcal C \rightarrow \mathcal P$，对任意的明文 $x \in \mathcal P$，都有 $d_K(e_K(x)) = x$。



## 简单密码体制

### 移位密码

令 $\mathcal P = \mathcal C = \mathcal K = \mathbb Z_{26}$，定义 $e_K(x) = (x + K) \mod 26$。

若取 $K = 3$，则为凯撒密码。



### 代换密码

令 $\mathcal P = \mathcal C = \mathbb Z_{26}$，定义 $e_{\pi}(x) = \pi(x)$，$\mathcal K $ 为26个字母所组成的所有可能置换。



### 仿射密码

$e(x) = (ax + b) \mod 26$。

若使密码能够解密，则需要仿射函数是一个单射函数。上述方程等价于同余方程

$$
ax \equiv y - b \mod 26
$$

上述同余方程有唯一解的充要条件是 $\text{gcd}(a, 26) = 1$。

定理：由上可知，仿射密码的密钥空间大小为 $m\phi(m)$。



### Vigenere 密码

密码体制：设 $m$ 是一个正整数，定义 $\mathcal P = \mathcal C = \mathcal K = (\mathbb Z_{26})^m$，对任意的密钥 $K = (k_1, \dots, k_m)$，定义：

$$
e_K(x_1, \dots, x_m) = (x_1 + k_1, \dots, x_m + k_m)
$$

和

$$
d_K(y_1, \dots, y_m) = (y_1 - k_1, \dots, y_m - k_m)
$$

定理：Vigenere 密码的密钥空间大小为 $26^m$。



### 希尔密码

在 Vigenere 密码的基础上利用了 $\mathbb Z_{26}$ 上的线性变换。

$$
(y_1, \dots, y_m) = (x_1, \dots, x_m) \begin{pmatrix} k_{11} & \cdots  & k_{1m} \\ \vdots & \ddots & \vdots \\ k_{m1} & \cdots & k_{mm} \end{pmatrix}
$$

密码体制：设 $m \ge 2$ 为正整数，$\mathcal P = \mathcal C = (\mathbb Z_{26})^m$，且

$$
\mathcal K = \{定义在\mathbb Z_{26} 上的 m \times m 的可逆矩阵 \}
$$

对任意的密钥 $K$，定义：

$$
e_K(x) =  xK
$$

和

$$
d_K(y) = yK^{-1}
$$

以上运算都是在 $\mathbb Z_{26}$ 上进行的。



### 置换密码

密码体制：令 $m$ 为一正整数，$\mathcal P = \mathcal C = (\mathbb Z_{26})^m$，$\mathcal K$ 由所有定义在集合 $\{1, 2, \dots, m\}$ 上的置换组成，对任意的密钥 $\pi$，定义加密变换

$$
e_{\pi}(x_1, \dots, x_m) = (x_{\pi(1)}, \dots, x_{\pi(m)})
$$

相应的解密变换为

$$
d_{\pi}(y_1, \dots, y_m) = (y_{\pi^{-1}(1)}, \dots, y_{\pi^{-1}(m)})
$$



### Playfair 密码

双字代换：

- 产生密钥表，$5 \times 5$ 的矩阵；
- 加密信息。

算法步骤：

- 将消息分成两字母一组。如果成对后有两个相同字母紧挨或最后一个字母是单个的，就插入一个字母X。

  > communist $\rightarrow$ co, mx, mu, ni, st。

- 若明文 $p_1p_2$ 在同一行，对应明文 $c_1c_2$ 分别是紧靠 $p_1p_2$ 右端的字母。

- 若 $p_1 p_2$ 在同一列，对应密文 $c_1 c_2$ 分别是紧靠 $p_1 p_2$ 下方的字母。

- 若 $p_1 p_2$ 不在同一行，不在同一列，则 $c_1 c_2$ 是由 $p_1 p_2$ 确定的矩形的其他两角的字母。



### 流密码

基本思想为产生一个密钥流 $z = z_1z_2 \dots$，然后用其加密明文串。

> 区别于分组密码，其中连续的明文元素是使用相同的密钥 $K$ 加密的。



## Enigma

### Enigma 结构

3个转子（5选3），插线板10对交换。

密钥空间：

$$
(5 \times 4 \times 3) \times (26^3) \times (\frac{26!}{6! \times 10! \times 2^{10}}) = 158,962,555,217,826,360,000
$$

> 注：在后期引入了 ring setting 之后 $26^3$ 变为 $26^5$。



### Enigma 破译

#### Rejewski

利用早期德军在文件开头发送两遍日密钥的特性。

> 在不考虑插线板交换的情况下，有如下特性：
>
> 假设日密钥为 $\alpha \beta \gamma$，电报前6个字母分别为 HGABLE，那么有：
> 
> $$
> \alpha(A_0) = H, \beta(A_1) = G, \gamma(A_2) = A, \\
> \alpha(A_3) = B, \beta(A_4) = L, \gamma(A_5) = E,
> $$
> 
> 由于 Enigma 的加密和解密是对称的，则容易发现 $H(A_0)(A_3) = B$。
>
> 可以通过大量收集电报找到对应的字母替换，并形成环。
>
> 容易发现，环的长度是不受插线板影响的，因此可以通过遍历进行破译。
>
> 加入插线板后，使用模糊匹配。



#### Turing

已知明文攻击。

猜测初始转子位置，对于一直明文的序列寻找环。



## 密码分析

根据敌手掌握的信息类型的不同：

- 唯密文攻击：只有密文信息。
- 已知明文攻击：知道当前密钥下的一些明密文对。
- 选择明文攻击：知道一些特定明文对应的密文。
- 选择密文攻击：知道一些特定的密文对应的明文。

Kerckhoff's Principle（密码学的基本假设）：一个密码系统的安全性不在于对加密算法进行保密，而仅在于对密钥的保密。



### 词频分析

可针对仿射密码、代换密码等体制进行唯密文攻击。



### Vigenere 密码分析

#### Kasiski 测试法

两段相同的明文段将被加密成相同的密文段，则他们的位置间距为 $m$ 的倍数。

在具体实行时，搜索长度至少为3的相同密文段，记录这些相同密文段到起始点之间的距离，加入得到如下几个距离 $\delta_1, \delta_2, \dots$，那么可以猜测 $m$ 为这些 $\delta_i$ 的最大公因数的因子。



#### 重合指数法

设 $\boldsymbol{x} = x_1x_2 \dots x_n$ 是一条长度为 $n$ 的串，$\boldsymbol x$ 的重合指数 $I_c(\boldsymbol x)$ 定义为 $\boldsymbol x$ 中两个随机元素相同的概率：

$$
I_c(\boldsymbol x) = \frac{\sum \limits_{i = 0}^{25} \dbinom {f_i} 2}{\dbinom n 2}
$$

设 $f_0, \dots, f_{25}$ 分别表示所有字母在 $\boldsymbol x$ 中出现的频数，则

- 若 $\boldsymbol x$ 是英语文本串，则 $I_c(\boldsymbol x) = \sum \limits_{i = 0}^{25} p_i^2 = 0.065$；
- 若 $\boldsymbol x$ 是一个完全的随机串，则 $I_c(\boldsymbol x) = 26 (\frac 1 {26})^2 = 0.038$。

对于 Vigenere 密码加密的密文，将其分组可以写成

$$
\boldsymbol y_1 = y_1 y_{m + 1}y_{2m + 1} \dots \\
\boldsymbol y_2 = y_2 y_{m + 2}y_{2m + 2} \dots \\
\vdots \\
\boldsymbol y_m = y_m y_{2m} y_{3m} \dots
$$

如果分组长度为 $m$，则每一串的重合系数都应该接近 $0.065$。



### 希尔密码分析

唯密文攻击破解希尔密码较为困难，但通过已知明文攻击可以通过求逆的方式快速破解。