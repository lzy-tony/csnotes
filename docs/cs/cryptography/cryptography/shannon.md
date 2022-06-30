# Shannon理论

## 密码体制安全性

- 无条件安全性：完善保密性。

  即使攻击者有无限的计算资源也不可能攻破密码体制，则该密码体制是无条件安全的。

- 计算安全性：破译一个密码体制所做的计算上的努力。

  如果使用最好的算法破译一个密码体制至少需要一个特定的非常大的数字，定义该密码体制是计算安全的。

- 可证明安全性：通过归约的方式为安全性提供证据。

  如果可以破译密码体制 $A$，则可以解决一个数学难题 $B$。



### 完善保密性

密码体制的随机变量视角：

假设 $(\mathcal P, \mathcal C, \mathcal K, \mathcal E, \mathcal D)$ 是一个特定的密码体制。$P(\boldsymbol x = x)$ 表示明文 $x$ 发生的先验概率，$P(\boldsymbol K = K)$ 表示密钥 $K$ 发生的概率。由 $\mathcal P$ 和 $\mathcal K$ 的概率分布可以导出 $\mathcal C$ 的概率分布，对于任意 $y \in \mathcal C$，有：

$$
P(\boldsymbol y = y) = \sum \limits_{\boldsymbol K: y \in \mathcal C(K)} P(\boldsymbol K = K)P(\boldsymbol x = d_K(y))
$$

同样，可以利用 Bayes 公式计算出给定密文明文的分布。

$$
P(\boldsymbol x = x| \boldsymbol y = y) = \frac{P(\boldsymbol x = x) \times \sum \limits_{K: x = d_K(y)} P(\boldsymbol K = K)}{\sum \limits_{\boldsymbol K: y \in \mathcal C(K)} P(\boldsymbol K = K)P(\boldsymbol x = d_K(y))}
$$

定义：一个密码体制具有完善保密性，即，如果对于任意的 $x \in \mathcal P$ 和 $y \in \mathcal C$，有 $P(x | y) = P(x)$。

定理：假设移位密码的26个密钥是等概率使用的，则对于任意的明文概率分布，移位密码具有完善保密性。

> 注：需要单个密钥加密单个明文，否则使用不是等概率。

定理：假设密码体制 $(\mathcal P, \mathcal C, \mathcal K, \mathcal E, \mathcal D)$ 满足 $|\mathcal K| = | \mathcal C| = |\mathcal P|$，这个密码体制是完善保密的，当且仅当每个密码被使用的概率是 $\frac 1{|\mathcal K|}$，并且对于任意的 $x \in \mathcal P$ 和 $y \in \mathcal C$，存在唯一的密钥 $K$ 使得 $e_K(x) = y$。



### 一次一密（OTP）

密码体制：假设 $n \ge 1$ 是正整数，$\mathcal P = \mathcal C = \mathcal K = (\mathbb Z_2)^n$。对于 $K \in (\mathbb Z_2)^n$，定义 $e_K(x)$ 为 $K$ 和 $x$ 的异或。因此，如果 $x = (x_1, \dots, x_n)$ 并且 $K = (K_1, \dots, K_n)$，则：

$$
e_K(x) = (x_1 + K_1, \dots, x_n + K_n) \mod 2 \\
$$

解密和加密过程相同，如果 $y = (y_1, \dots, y_n)$，则

$$
d_K(y) = (y_1 + K_1, \dots, y_n + K_n) \mod 2
$$

定理：OTP具备完善保密性。

> 证明：
> 
> $$
> \begin{align} 
> P(\boldsymbol C = c |\boldsymbol P = p) &= P(\boldsymbol K = p \oplus c) = \frac 1 {2^n} \\
> P(\boldsymbol C = c) &= \sum \limits_{p \in P} P(\boldsymbol P = p)P(\boldsymbol C = c | \boldsymbol P = p) \\
> &= \frac 1 {2^n} \sum \limits_{p \in P} P(\boldsymbol P = p) = \frac 1 {2^n}
> \end{align}
> $$
> 
> 因此有
> 
> $$
> P(\boldsymbol P = p | \boldsymbol C = c) = \frac{P(\boldsymbol C = c | \boldsymbol P = p) P(\boldsymbol P = p)}{P(\boldsymbol C = c)} = P(\boldsymbol P = p)
> $$

**无法被破译** 的（可利用信息熵证明），一个 **无法被破译的密码系统必须具备与一次一密相同的条件** ，即密钥必须有以下特征：

- 完全随机；
- 不能重复使用；
- 保密；
- 和明文一样长。