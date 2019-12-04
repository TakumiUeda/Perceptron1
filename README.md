#教科書について
ゼロから作るDeep Learningという本を買いました。このページはその内容をMATLAB（~~Pycharm開くのがだるかった~~）で実装したまとめです。

#論理演算って何
論理演算は１や０といくつかの計算規則で定義される演算です。コンピュータなどに使われています。
今回扱う基本演算はAND、OR、NAND、EX-ORの４つです。まずはこの4つを詳しくみていきます。

## AND
ANDは通常の掛け算と同じで書き方だけ異なり、
$$X=A \cdot B$$
と書きます。このような式を論理式と言います。論理演算の結果をまとめたものを真理値表といい、ANDの真理値表は

|A  |B  |X  |
|---|---|---|
|0  |0  |0  |
|0  |1  |0  |
|1  |0  |0  |
|1  |1  |1  |

## OR
ORは通常のたし算と同じですが、１＋１が１となることだけ異なる点に注意が必要です。
論理式は
$$X=A + B$$
と書きます。真理値表は

|A  |B  |X  |
|---|---|---|
|0  |0  |0  |
|0  |1  |1  |
|1  |0  |1  |
|1  |1  |1  |

## NOT
NOTは独特の演算で、１が０、０が１となります。
論理式は
$$X=\overline{A}$$
と書きます。真理値表は

|A  |X  |
|---|---|
|0  |1  |
|1  |0  |

## NAND
NANDはANDの結果にNOTを適応したものです。
論理式は
$$X=\overline{A \cdot B}$$
と書きます。真理値表は

|A  |B  |X  |
|---|---|---|
|0  |0  |1  |
|0  |1  |1  |
|1  |0  |1  |
|1  |1  |0  |

## EX-OR
EX-ORは排他的論理和とも呼ばれ独特の演算です。
論理式は
$$X=\overline{A} \cdot B+A \cdot \overline{B}$$
と書きます。真理値表は
|A  |B  |X  |
|---|---|---|
|0  |0  |0  |
|0  |1  |1  |
|1  |0  |1  |
|1  |1  |0  |

今回は教科書に従い
$$X=\overline{A \cdot B} \cdot (A+B)$$
を使用します。この式はドモルガンの法則
$$\overline{A \cdot B}= \overline{A} + \overline{B}$$
を使えば
$$X=\overline{A \cdot B} \cdot (A+B)=\overline{A} \cdot B+A \cdot \overline{B}$$
を確かめられます。

#パーセプトロン
## ANDの実装
次の式で実装します。

```math
\begin{align} y = \begin{cases} 1 \hspace{5mm}(x_{1} w_{1} +x_{2} w_{2} \leq \theta)\\ 0 \hspace{5mm} (x_{1} w_{1} +x_{2} w_{2} \gt \theta) \end{cases} \end{align}
```

このようなモデルをパーセプトロンと呼ぶことにします。パーセプトロンを１つ実装するコードは

```
w1=1; w2=1;theta=1;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
tmp=x1*w1+x2*w2;
tmp(tmp<=theta)=0;
tmp(tmp>theta)=1
```

ここで

```math
\begin{align} y = \begin{cases} 1 \hspace{5mm}(x_{1} w_{1} +x_{2} w_{2} \leq \theta)\\ 0 \hspace{5mm} (x_{1} w_{1} +x_{2} w_{2} \gt \theta) \end{cases} \end{align}
```

式を

```math
\begin{align} y = \begin{cases} 1 \hspace{5mm}(b+x_{1} w_{1} +x_{2} w_{2} \leq 0)\\ 0 \hspace{5mm} (b+x_{1} w_{1} +x_{2} w_{2} \gt 0) \end{cases} \end{align}
```

として実装します。$w$は重み、$b$はバイアスなどと呼ばれます。

```
w1=1; w2=1;b=-1;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
tmp=b+x1*w1+x2*w2;
tmp(tmp<=0)=0;
tmp(tmp>0)=1
```

##NANDとORの実装
重みとバイアスをいじくって実装します。

```
OR
x1|x2|y
0  |  0|0
0  |  1|1
1  |  0|1
1  |  1|1
w1=1; w2=1;b=-0.2;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
tmp=b+x1*w1+x2*w2;
tmp(tmp<=0)=0;
tmp(tmp>0)=1

NAND
x1|x2|y
0  |  0|1
0  |  1|1
1  |  0|1
1  |  1|0
w1=-0.5; w2=-0.5;b=0.7;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
tmp=b+x1*w1+x2*w2;
tmp(tmp<=0)=0;
tmp(tmp>0)=1
```

##EX-ORの実装
EX-ORは直線で分離できません。（曲線ならできます）そのため、パーセプトロンを一つ使う方法ではEX-ORを実現できません。
そこで先ほど変形した式を使います
$$X=\overline{A \cdot B} \cdot (A+B)$$
この式をそれぞれ実装します。（複数のパーセプトロンを利用して実装します。）

```
XOR
x1|x2|y
0  |  0|1
0  |  1|1
1  |  0|1
1  |  1|0
%NAND
w1=-0.5; w2=-0.5;b=0.7;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
s1=b+x1*w1+x2*w2;
s1(s1<=0)=0;
s1(s1>0)=1
%OR
w1=1; w2=1;b=-0.2;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
s2=b+x1*w1+x2*w2;
s2(s2<=0)=0;
s2(s2>0)=1
%XOR
tmp=s1*w1+s2*w2;
tmp(tmp<=theta)=0;
tmp(tmp>theta)=1
```

##まとめ
論理演算の例で単層パーセプトロンを扱いましたが、はじめのAND以外は全て同じプログラムで重みとバイアスが異なるのみでした。（EX-ORは例外）
学習とは重みやバイアスを適切な値に設定することを指します。
またANDなどのパーセプトロンを一つ使う方法を単層パーセプトロン、EX-ORのように複数個使う方法を他そうパーセプトロンと言います。

#ソース全体
```
% 単純パーセプトロンを使って
% 論理演算のAND、OR、NAND、EX-ORを実装してみた 
%
% Author:      Takumi Ueda
% Date:        2019-12-4
掃除
clear
AND
x1|x2|y
0  |  0|0
0  |  1|0
1  |  0|0
1  |  1|1
w1=1; w2=1;theta=1;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
tmp=x1*w1+x2*w2;
tmp(tmp<=theta)=0;
tmp(tmp>theta)=1
別の実装方法
重みを実装
w1=1; w2=1;b=-1;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
tmp=b+x1*w1+x2*w2;
tmp(tmp<=0)=0;
tmp(tmp>0)=1

OR
x1|x2|y
0  |  0|0
0  |  1|1
1  |  0|1
1  |  1|1
w1=1; w2=1;b=-0.2;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
tmp=b+x1*w1+x2*w2;
tmp(tmp<=0)=0;
tmp(tmp>0)=1

NAND
x1|x2|y
0  |  0|1
0  |  1|1
1  |  0|1
1  |  1|0
w1=-0.5; w2=-0.5;b=0.7;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
tmp=b+x1*w1+x2*w2;
tmp(tmp<=0)=0;
tmp(tmp>0)=1

XOR
x1|x2|y
0  |  0|1
0  |  1|1
1  |  0|1
1  |  1|0
%NAND
w1=-0.5; w2=-0.5;b=0.7;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
s1=b+x1*w1+x2*w2;
s1(s1<=0)=0;
s1(s1>0)=1
%OR
w1=1; w2=1;b=-0.2;
x1=[0 0 1 1]';
x2=[0 1 0 1]';
s2=b+x1*w1+x2*w2;
s2(s2<=0)=0;
s2(s2>0)=1
%XOR
tmp=s1*w1+s2*w2;
tmp(tmp<=theta)=0;
tmp(tmp>theta)=1
```

#github
https://github.com/rein-chihaya/Perceptron1

#License
MIT

#参考文献
斎藤康毅.ゼロから作るDeep Learning: Pythonで学ぶディープラーニングの理論と実装.オライリー・ジャパン，2016
