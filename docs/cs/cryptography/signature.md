# 数字签名

## 数字签名

### 目标

- 收方能够确认或证实发方的签名，但 **不能伪造**；
- 发方发出签名的消息给收方后，就 **不能再否认** 他所签发的消息；
- 收方对已收到的签名消息 **不能否认**，即有收报认证；
- 第三者 **可以确认** 收发双方之间的消息传送，但 **不能伪造** 这一过程。



### 数字签名与消息认证

对于收发双方而言，如果没有利害冲突，消息认证对于 **防止第三方破坏** 的功能已经足够：

- 收方能够验证消息发送者身份是否被篡改；
- 收方能够验证所发消息内容是否被篡改。

当收发双方存在潜在利益冲突时，需要引入数字签名技术解决其中的纠纷：

- 数字签名能确定消息来源的真实性；
- 数字签名能保证实体身份的真实性；
- 数字签名是不可否认的。



### 数字签名与公钥加密

公钥加密：

- A 采用 B 的 **公开密钥** 对信息加密，A 将密文发给 B；
- B用 **自己的私钥对收到的密文解密**，恢复出明文。

数字签名：

- A 采用自己的 **私钥** 对消息 $m$ 签名，A 将 $m$ 和签名发给 B；
- B 收到 A 的签名后，采用 **A 的公钥** 来验证签名的有效性。



### 数字签名方案

定义：一个签名方案是一个满足下列条件的五元组 $(\mathcal P, \mathcal A, \mathcal K, \mathcal S, \mathcal V)$：

1. $\mathcal P$ 是由所有可能的消息组成的有限集合。

2. $\mathcal A$ 是由所有可能的签名组成的有限集合。

3. $\mathcal K$ 为密钥空间，是所有可能的密钥组成的有限集合。

4. 对每个 $K \in \mathcal K$，有一个签名算法 $\text{sig}_K \in \mathcal S$ 和一个验证算法 $\text{ver}_K \in \mathcal V$。对每一个消息 $x \in P$ 和每一个签名 $y \in \mathcal A$，每个 $\text{sig}_K: \mathcal P \rightarrow \mathcal A$ 和 $\text{ver}_K: \mathcal P \times \mathcal A \rightarrow \{\text{true}, \text{false}\}$ 都是满足下列条件的函数：

$$
\text{ver}_K(x, y) = 
\begin{cases}
\text{true} & y = \text{sig}_K(x) \\
\text{false} & y \neq \text{sig}_K(x)
\end{cases}
$$



## RSA 数字签名体制

### RSA 数字签名方案

密码体制：设 $n = pq$，其中 $p, q$ 是素数。设 $\mathcal P = \mathcal A = \mathbb Z_n$，定义

$$
\mathcal K = \{(n, p, q, a, b | a b \equiv 1 \mod \phi(n)) \}
$$

$(n, b)$ 为公钥，$(p, q, a)$ 为私钥。对 $K = (n, p, q, a, b)$，定义 $\text{sig}_K(x) = x^a \mod n$，以及 $\text{ver}_K(x, y) = \text{true} \iff x \equiv y^b \mod n$。



### 签名同态特性

RSA 的乘法同态性：如果 $s_1 = m_1^a \mod n, s_2 = m_2^a \mod n$ 分别是消息 $m_1, m_2$ 的签名，则容易由此得出消息 $m = m_1 \cdot m_2$ 的合法签名 $s_1 \cdot s_2 \mod n$。

解决方案：先 Hash 再签名。



## ElGamal 签名体制

### ElGamal 签名方案

密码体制：设 $p$ 是一个使得在 $\mathbb Z_p$ 上的离散对数问题难以处理的素数，设 $g \in \mathbb Z_p^*$ 是一个原根。设 $\mathcal P = \mathbb Z_p^*, \mathcal A = \mathbb Z_p^* \times \mathbb Z_{p - 1}^*$，定义

$$
\mathcal K = \{(p, g, a, \beta) | \beta \equiv g ^a \mod p \}
$$

$(p, g, \beta)$ 是公钥，$a$ 是私钥。

对 $K = (p, g, a, \beta)$ 和一个密码的随机数 $k \in \mathbb Z_{p - 1}^*$，定义

$$
\text{sig}_K(x, k) = (\gamma, \delta)
$$

其中

$$
\gamma = g^k \mod p, \delta = (x - a \gamma)k^{-1} \mod (p - 1)
$$

对 $x, \gamma \in \mathbb Z_p^*$ 和 $\delta \in \mathbb Z_{p - 1}$，定义

$$
\text{ver}_K(x, (\gamma, \delta)) = \text{true} \iff \beta^{\gamma} \gamma ^ {\delta} \equiv g ^x \mod p
$$

> 注：此处的 $x$ 也可以使用 Hash 后的 $H(x)$。

> 证明：
> 
> $$
> \beta^{\gamma} \gamma^{\delta} \equiv g^{a \gamma}g^{k \delta} \equiv g^{a \gamma + k \delta} \mod p
> $$
> 
> 而
> 
> $$
> a \gamma + k \delta \equiv a \gamma + (x - a \gamma) \equiv x \mod p - 1
> $$
> 
> 故由欧拉定理
> 
> $$
> \beta^{\gamma} \gamma ^{\delta} \equiv g^x \mod p
> $$



### ElGamal 签名安全性

#### 伪造签名

对于一个敌手，若其在不知道私钥 $a$ 的情况下希望对给定消息 $x$ 伪造签名，则有两种伪造路径：

1. 选定 $\gamma$，利用其寻找对应的 $\delta$，则必须计算离散对数问题。

2. 选定 $\delta$，利用其寻找对应的 $\gamma$，则需求解等式：

$$
\beta^{\gamma}\gamma^{\delta} \equiv g^x \mod p
$$
求解这个等式难度未知，且没有已知的可行办法。



#### 同模数攻击

如果攻击者掌握了同一随机数 $k$ 下的两个消息 $m_1, m_2$ 的合法签名 $(\gamma_1, \delta_1), (\gamma_2, \delta_2)$，就会构造如下的方程组：

$$
\begin{cases}
m_1 = \gamma_1 a + \delta_1 k \\
m_2 = \gamma_2 a + \delta_2 k
\end{cases}
$$

则可以通过解此方程组同时破译 $a, k$。

因此，要确保此签名体制的安全性，就必须保证每次签名时，选择不同的随机数 $k$。



## ElGamal 签名体制的变种

### Schnorr 签名体制

ElGamal 签名有可能较长，对于智能卡等潜在应用而言不是最好的方案，而 Schnorr 签名体制对这一点进行了改进。

密码体制：设 $p$ 是一个使得在 $\mathbb Z_p$ 上的离散对数问题难以处理的素数，$q$ 是能被 $p - 1$ 整除的素数。设 $g \in \mathbb Z_p^*$ 是 $1$ 模 $p$ 的 $q$ 次根（$g^q \equiv 1 \mod p$），$\mathcal P = \{0, 1\}^*$，$\mathcal A = \mathbb Z_q \times \mathbb Z_q$，并定义

$$
\mathcal K = \{(p, q, \alpha, g , \beta) | \beta \equiv g^a \mod p \}
$$

其中 $0 \le a \le q - 1$，$(p, q, g, \beta)$ 是公钥，$a$ 为私钥。最后，设 $h: \{0, 1\}^* \rightarrow \mathbb Z_q$ 是一个安全的 Hash 函数。

对于 $K = (p, q, g, a, \beta)$ 和一个秘密的随机数 $k$，$1 \le k \le q - 1$，定义

$$
\text{sig}_K(x, k) = (\gamma, \delta)
$$

其中 $\gamma = h(x \lvert \rvert g^k \mod p)$ 且 $\delta = k + a \gamma \mod q$。

对于 $x \in \{0, 1\}^*$ 和 $\gamma, \delta \in \mathbb Z_q$，验证是通过下面计算完成的：

$$
\text{ver}_K(x, (\gamma, \delta)) = \text{true} \iff h(x \lvert \rvert g^\delta \beta^{-\gamma} \mod p) = \gamma
$$

> 注：Schnorr 相对于 ElGamal 有几点优点：
>
> - 长度短：由 $\lvert q \rvert $ 和 $\lvert h(x) \rvert$ 决定。
> - 速度快：$w = g^k \mod p$ 可以提前计算，签名只需要一次乘法和一次加法。




### DSS 签名体制

在 ElGamal 的基础上，使用 $\mathbb Z_p^*$ 的一个 $q$ 阶子群，得到 DSA 算法。

密码体制：设 $p$ 是长为 $L$ 比特的素数，在 $\mathbb Z_p$ 上其离散对数问题是难以处理的，其中 $L \equiv 0 \mod 64$ 且 $512 \le L \le 1024$，$q$ 是能被 $p - 1$ 整除的160比特素数。设 $g \in \mathbb Z_p^*$ 是1模 $p$ 的 $q$ 次方根。设 $\mathcal P = \{0, 1\}^*, \mathcal A = \mathbb Z_q^* \times \mathbb Z_q^*$，并定义

$$
\mathcal K = \{(p, q, g, a, \beta) | \beta \equiv g^a \mod p \}
$$

其中 $0 \le a \le q - 1$，$(p, q, g, \beta)$ 是公钥，$a$ 为私钥。

对于 $K = (p, q, g, a, \beta)$ 和一个秘密的随机数 $k$，$1 \le k \le q - 1$，定义

$$
\text{sig}_K(x, k) = (\gamma, \delta)
$$

其中

$$
\gamma = (g^k \mod p) \mod q
$$

并且

$$
\delta = (h(x) + g \gamma)k^{-1} \mod q
$$

验证过程为

$$
e_1 = h(x)\delta^{-1} \mod q \\
e_2 = \gamma \delta^{-1} \mod q \\
\text{ver}_K(x, (\gamma, \delta)) = \text{true} \iff (g^{e_1}\beta^{e_2} \mod p) \mod q = \gamma
$$



## 特殊签名体制

### 不可否认签名

在签名者合作下，才能验证签名。



### 防失败签名

当 A 的签名受到攻击，甚至在分析出 A 的私钥的情况下，攻击者也难以伪造 A 的签名。



### 盲签名

加入盲因子，使得签名者不知道签署的文件内容。



### 群签名

只有群中的成员才能代表群体签名。



### 代理签名

某人授权其代理进行的签名，在委托签名时，签名密钥不交给代理人。



### 指定证实人签名

指定一个人负责证实所有人的签名。

优点：有助于防止签名失效。



### 一次性签名

签名者至多只能对一个消息进行签名，否则签名就可能被伪造。

优点：产生和验证速度都非常快，特别适用于计算能力比较低的芯片和智能卡实现。
