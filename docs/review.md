# 🧠 Knowledge Review

## 1. 核心定义
无穷级数 $\sum_{n=1}^{\infty} a_n$ 的敛散性取决于其部分和序列 $S_n$ 的极限。

$$S_n = a_1 + a_2 + ... + a_n$$

## 2. 关键判别法 (Flowchart)
* **n项判别法**：如果 $\lim_{n \to \infty} a_n \neq 0$，则级数发散。
* **比值判别法**：计算 $\rho = \lim_{n \to \infty} |\frac{a_{n+1}}{a_n}|$
    * 若 $\rho < 1$，绝对收敛 ✅
    * 若 $\rho > 1$，发散 ❌