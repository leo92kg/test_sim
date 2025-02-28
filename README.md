# tf2ss function returns unstable system.
num, den, tf2ss, impulse, pole, zero

<pre>
<code>
>> num;
>> den;
>> [A,B,C,D]=tf2ss(num,den);
>> impulse(sys_tf);
>> impulse(sys_ss);
</code>
</pre>

Transfer function and state space model obtained by tf2ss function failed in the impulse response.\
I checked pole and zero both sides, pole values are the same but found zero value mismatch between sides.\
But I don't know why the impulse response of transfer function doesn't behave as expected.

<pre>
<code>
>> zero(sys_tf);
  return values
>> zero(sys_ss);
  return values
</code>
</pre>

Upon review, I found that there is a problem with the calculation.\
I watched the derivation process of the equation closely and got something weird.

$Transfer\ Function = \dfrac{b_1s + b_2}{s^4Y(s) + a_1s^3Y(s) + a_2s^2Y(s) + a_3sY(s) + a_4}$

$x_1=Y(s)\; \to x_1'=  sY(s)= x_2$\
$x_2=sY(s)\ \to x_2'=s^2Y(s)= x_3$\
$x_3=s^2Y(s) \to x_3'=s^3Y(s) = x_4$\
$x_4=s^3Y(s) \to x_4'=s^4Y(s) = -a_4 Y(s) -a_3 sY(s) -a_2 s^2Y(s) -a_1 s^3Y(s)= -a_4 x_1 -a_3 x_2 -a_2 x_3 -a_1 x_4$




## transfer function to state space model

## example
$Transfer\ Function = \dfrac{b_1s + b_2}{s^4Y(s) + a_1s^3Y(s) + a_2s^2Y(s) + a_3sY(s) + a_4}$

$x_1=Y(s)\; \to x_1'=  sY(s)= x_2$\
$x_2=sY(s)\ \to x_2'=s^2Y(s)= x_3$\
$x_3=s^2Y(s) \to x_3'=s^3Y(s) = x_4$\
$x_4=s^3Y(s) \to x_4'=s^4Y(s) = -a_4 Y(s) -a_3 sY(s) -a_2 s^2Y(s) -a_1 s^3Y(s)= -a_4 x_1 -a_3 x_2 -a_2 x_3 -a_1 x_4$

$\dot{a}$

$$ \dot{x}=\begin{pmatrix}
x & y \\
z & v
\end{pmatrix}*x +
\begin{pmatrix}
x \\
z 
\end{pmatrix}*u $$

```math
\left \begin{pmatrix}
x & y \\
z & v \left
\end{pmatrix}
```

$\begin{bmatrix}A1 &A2 & A3\\A4 & A5 & A6 \end{bmatrix}$


## This'll be a _Helpful_ Section About the Greek Letter Θ!
A heading containing characters not allowed in fragments, UTF-8 characters, two consecutive spaces between the first and second words, and formatting.

## This heading is not unique in the file

TEXT 1

# Links to the example headings above

<p>$\it{\large{\color{#DD6565}이텔릭체,\ 큰글씨,\ 빨간색}}$</p>

2</sup>/</sup>3

$\ \boldsymbol{\alpha\beta\gamma\delta\epsilon\zeta\eta\theta} \$

Let $x= \boldsymbol{\alpha} {\alpha} $

$$ A=\begin{pmatrix}
x & y \\
z & v
\end{pmatrix}*x +
\begin{pmatrix}
x \\
z 
\end{pmatrix}*u $$

$x1=Y(s)$    -> $x1'=sY(s)= x2$\
$x2=sY(s)$   -> $x2'=s^2Y(s)= x3$\
$x3=s^2Y(s)$ -> $x3'=s^3Y(s) = x4$\
$\dot{a}$


$x'$=$\' [$3\over4$  2 3] '\$ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[4 5 6]\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[7 8 9]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[2</sup>/</sup>3 2 3]&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[0]\
$x'$=[&nbsp;4 5 6]&nbsp; $x$ $+$ [0] $u$ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[7 8 9]

$x$


