+++
title = 'Gamma分布のCanonical Link Function導出過程'
date = 2024-07-29T10:26:02+09:00
draft = false
+++
## 1. Gamma分布の確率密度関数
gamma分布の確率密度関数は以下のように表されます：

$$
f(y; \beta, \alpha) = \frac{1}{\Gamma(\alpha) \cdot \beta^\alpha} \cdot y^{\alpha-1} \cdot e^{-y/\beta}
$$

ここで、$\beta$は尺度パラメータ、$\alpha$は形状パラメータです。

## 2. パラメータの置き換え
一般化線形モデル（GLM）の文脈では、通常$\beta$を$\mu/\alpha$と置き換えます。$\mu$は平均を表します。
この置き換えにより、確率密度関数は以下のようになります：

$$
f(y; \mu, \alpha) = \frac{1}{\Gamma(\alpha) \cdot (\mu/\alpha)^\alpha} \cdot y^{\alpha-1} \cdot e^{-\alpha y/\mu}
$$

## 3. 指数型分布族への変換
次に、この分布が指数型分布族の形式に従うことを示します。
指数型分布族の一般形は以下のとおりです：

$$
f(y; \theta) = \exp\left(\frac{y\theta - b(\theta)}{a(\phi)} + c(y, \phi)\right)
$$

この式を対数変換すると以下のようになります：

$$
\log(f(y; \theta)) = \frac{y\theta - b(\theta)}{a(\phi)} + c(y, \phi)
$$

gamma分布の確率密度関数を対数変換します：

$$
\log(f(y; \mu, \alpha)) = -\log(\Gamma(\alpha)) - \alpha\log(\mu/\alpha) + (\alpha-1)\log(y) - \alpha y/\mu
$$

これを整理して指数型分布族の形に合わせます：

$$
\begin{aligned}
-\alpha\log(\mu/\alpha) &= -\alpha(\log(\mu) - \log(\alpha)) = -\alpha\log(\mu) + \alpha\log(\alpha)\\
\log(f(y; \mu, \alpha)) &= -\log(\Gamma(\alpha)) + (-\alpha\log(\mu) + \alpha\log(\alpha)) + (\alpha-1)\log(y) - \alpha y/\mu\\
&= -\alpha y/\mu + (\alpha-1)\log(y) - \alpha\log(\mu) + \alpha\log(\alpha) - \log(\Gamma(\alpha))\\
&= \left(-\frac{\alpha}{\mu}\right)y + (\alpha-1)\log(y) - \alpha\log(\mu) + \alpha\log(\alpha) - \log(\Gamma(\alpha))
\end{aligned}
$$

## 4. $\beta$, $b(\beta)$, $a(\phi)$, $c(y, \phi)$の定義
ここで、$\beta$を以下のようにおきます。

$$
\beta = -\frac{\alpha}{\mu}
$$

この$\beta$を用いて、$b(\beta)$, $a(\phi)$, $c(y, \phi)$はそれぞれ以下のように表すことができます。
$$
\begin{aligned}
b(\beta) &= -\alpha\log(-\beta)\\
a(\phi) &= \frac{1}{\alpha}\\
c(y, \phi) &= (\alpha-1)\log(y) + \alpha\log(\alpha) - \log(\Gamma(\alpha))
\end{aligned}
$$

これで、gamma分布が指数型分布族の形式に従うことが示されました。

## 5. Canonical Link Functionの定義
canonical link functionは、以下のようなの形で表されます。
$$
\beta = g(\mu)
$$

ここで$g()$がlink functionです。よって、$\beta$の定義より、
$$
\begin{aligned}
\beta &= g(\mu) = -\frac{\alpha}{\mu}\\
\mu &= -\frac{\alpha}{\beta}
\end{aligned}
$$

したがって、$g(\mu) = -\frac{\alpha}{\mu}$がcanonical link functionとなります。
通常、$k$は既知のパラメータとして扱われるので、$-\frac{1}{\mu}$を canonical link function として使用します。

そして、この関数の逆関数が、実際にGLMで使用されるlink functionとなります。
逆関数は $g^{-1}(\beta) = -\frac{1}{\beta}$ となり、これは「逆数リンク関数」（reciprocal link function）と呼ばれます。

## 6. まとめ
gamma分布のcanonical link functionは$-\frac{1}{\mu}$で、その逆関数である$-\frac{1}{\beta}$がGLMで使用されます。
この link function を使用することで、線形予測子と平均パラメータ$\mu$の関係が適切にモデル化されます。
