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
$x_4=s^3Y(s) \to x_4'=s^4Y(s) = -a_4 Y(s) -a_3 sY(s) -a_2 s^2Y(s) -a_1 s^3Y(s)= -a_4 x_1 -a_3 x_2 -a_2 x_3 -a_1 x_4 + u $

$$x'=\begin{bmatrix}
0 & 1 & 0 & 0\\
0 & 0 & 1 & 0\\
0 & 1 & 0 & 1\\
-a_4 & -a_3 & -a_2 & -a_1
\end{bmatrix} + \begin{bmatrix}
0\\
0\\
0\\
1
\end{bmatrix} u$$
$$y = \begin{bmatrix}
b_2 & b_1 & 0 & 0
\end{bmatrix} + 0*u$$

There is no reason to do "Let", and as you know, there are many methods.

Unlike the previous method, I changed it as follows.

$x_1=Y(s)\; \to x_1'=  sY(s)= x_2$\
$x_2=sY(s)\ \to x_2'=s^2Y(s)= x_3$\
$x_3=s^2Y(s) \to x_3'=s^3Y(s) = x_4$\
$x_4=s^3Y(s) \to x_4'=s^4Y(s) = -a_4 Y(s) -a_3 sY(s) -a_2 s^2Y(s) -a_1 s^3Y(s)= -a_4 x_1 -a_3 x_2 -a_2 x_3 -a_1 x_4 + u $

$$x'=\begin{bmatrix}
0 & 1 & 0 & 0\\
0 & 0 & 1 & 0\\
0 & 1 & 0 & 1\\
-a_4 & -a_3 & -a_2 & -a_1
\end{bmatrix} + \begin{bmatrix}
0\\
0\\
0\\
1
\end{bmatrix} u$$
$$y = \begin{bmatrix}
b_2 & b_1 & 0 & 0
\end{bmatrix} + 0*u$$

So I got An, Bn, Cn, Dn matrix and it works in the impulse response.

<pre>
<code>
>> 
>> sys_ssn=ss(An,Bn,Cn,Dn);
>> 
>> sys_ss
  A,B,C,D
>> 
>> sys_ssn
  An,Bn,Cn,Dn
>> 
>> pole(sys_ss)
  pole values
>> 
>> ploe(sys_ssn)
  pole values
>> 
>> zero(sys_ss)
  zero value
>> 
>> zero(sys_ssn)
  zero value
>> 
</code>
</pre>

Moreover, there are more zero values than the state space model obtained from the tf2ss function.

The following example works properly with both the transfer function and the state space mode.

<pre>
<code>
>> 
>> num=[0.02 2000]; den=[1 1000 132 420 2000];
>> 
>> sys_tf=tf(num,den); figure(1); impulse(sys_tf);
>> 
>> [A,B,C,D]=tf2ss(num,den); sys_ss=ss(A,B,C,D); figure(2); impulse(sys_ss);
>> 

>> An=[0 1 0 0;0 0 1 0;0 0 0 1;-a4 -a3 -a2 -a1];
>> Bn=[0 0 0 1]';
>> Cn=[b2 b1 0 0];
>> Dn=0;
>> 
>> sys_ss
  <span style="color: red"> Some green text </span>
    <span
    class="ml-1 d-inline-block border circle color-border-subtle"
    style="background-color: #aa4400; height: 8px; width: 8px;"
  ></span>
  <code> <i><b>This text will be italic</b></i> <b>this text will be bold</b> </code>
  <code>some <span style="color:blue">beautiful</span> text here</code>

sys_ss.a =
               x1          x2          x3          x4
   x1   -3.44e-15   2.365e-14  -1.879e-15           2
   x2         -10   1.776e-15  -2.365e-14        -4.2
   x3          <code> <b>0</b>  </code>       -10  -1.774e-15        13.2
   x4          <code> <b>0</b>  </code>        <code> <b>0</b>  </code>       -10       -1000

sys_ss.b =
              u1
   x1          2
   x2    -0.0002
   x3  4.729e-15
   x4          0

sys_ss.c =
       x1  x2  x3  x4
   y1   0   0   0  -1

sys_ss.d =
       u1
   y1   0
>> 
>> sys_ssn
                                                                                                               
sys_ssn.a =
           x1      x2      x3      x4
   x1       0   3.182       0       0
   x2       0       0   0.132       0
   x3       0       0       0    1000
   x4  -4.762  -3.182  -0.132   -1000

sys_ssn.b =
       u1
   x1   0
   x2   0
   x3   0
   x4   1

sys_ssn.c =
              x1         x2         x3         x4
   y1      4.762  0.0001515          0          0

sys_ssn.d =
       u1
   y1   0  
</code>
</pre>

But there are unwanted non-zero values in the state space model produced by the tf2ss function.




# generalized method

$x_1=\boldsymbol{\alpha}Y(s)\; \to x_1'=  sY(s)= x_2$\
$x_2=\boldsymbol{\beta}sY(s)\ \to x_2'=s^2Y(s)= x_3$\
$x_3=\boldsymbol{\gamma}s^2Y(s) \to x_3'=s^3Y(s) = x_4$\
$x_4=s^3Y(s) \to x_4'=s^4Y(s) = -a_4 Y(s) -a_3 sY(s) -a_2 s^2Y(s) -a_1 s^3Y(s)= -a_4 x_1 -a_3 x_2 -a_2 x_3 -a_1 x_4 + u $





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


