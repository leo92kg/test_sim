# tf2ss function returns unstable system.



num, den, tf2ss, impulse, pole, zero

<pre>
<code>
>>
>> pkg load control; pkg load signal; clear; 
>> 
>> alpha=5.6*10^10; beta=1.2*10^10; omega=2*pi*4.1016*10^10; den1=[1 2*alpha alpha^2+omega^2]; den2=[1 2*beta beta^2+omega^2];
>> 
>> num=0.7*omega*[2*(beta-alpha) beta^2-alpha^2]; den=conv(den1, den2);
>> 
>> sys_tf=tf(num,den); figure(1); impulse(sys_tf);
>>  
>> [A,B,C,D]=tf2ss(num,den); sys_ss=ss(A,B,C,D); figure(2); impulse(sys_ss);
error: c2d: system is already discrete-time
error: called from
    c2d at line 69 column 5
    __time_response__ at line 125 column 7
    impulse at line 74 column 13
>>
</code>
</pre>

The state space model obtained by tf2ss function failed in the impulse response.\
I checked pole and zero both sides, pole values are the same but found zero value mismatch between sides.\
But I don't know why the impulse response of transfer function doesn't behave as expected.

<table>
  <tr>
    <td style="width: 50%;">
<pre>
<code>
>> pole(sys_tf)
ans =
  -5.6000e+10 + 2.5771e+11i
  -5.6000e+10 - 2.5771e+11i
  -1.2000e+10 + 2.5771e+11i
  -1.2000e+10 - 2.5771e+11i
>> zero(sys_tf)
ans = -34000000000
</code>
</pre>
    </td>
    <td style="width: 50%;">
<pre>
<code>
>> pole(sys_ss)
ans = [](0x0)
>> zero(sys_ss)
ans = [](0x1)
>>
<pre>
<code>
    </td>
  </tr>
</table>

<table>
  <tr>
    <td style="width: 50%;">
      pole(sys_tf)
      첫 번째 섹션의 내용을 작성하세요.
    </td>
    <td style="width: 50%;">
      # 두 번째 섹션 제목
      두 번째 섹션의 내용을 작성하세요.
    </td>
  </tr>
</table>

<pre>
<code>
>> pole(sys_tf)
ans =
  -5.6000e+10 + 2.5771e+11i
  -5.6000e+10 - 2.5771e+11i
  -1.2000e+10 + 2.5771e+11i
  -1.2000e+10 - 2.5771e+11i
</code>
</pre>

<pre>
<code>
>> pole(sys_ss)
ans = [](0x0)
>> zero(sys_ss)
ans = [](0x1)
>>
<pre>
<code>


Upon review, I found that there is a problem with the calculation.\
I watched the derivation process of the equation closely and got something weird.

$$Transfer\ Function = \dfrac{b_1s + b_2}{s^4Y(s) + a_1s^3Y(s) + a_2s^2Y(s) + a_3sY(s) + a_4}$$

$Let\ x_1=Y(s)\; \to x_1'=  sY(s)= x_2$\
$Let\ x_2=sY(s)\ \to x_2'=s^2Y(s)= x_3$\
$Let\ x_3=s^2Y(s) \to x_3'=s^3Y(s) = x_4$\
$Let\ x_4=s^3Y(s) \to x_4'=s^4Y(s) = -a_4 Y(s) -a_3 sY(s) -a_2 s^2Y(s) -a_1 s^3Y(s)= -a_4 x_1 -a_3 x_2 -a_2 x_3 -a_1 x_4 + u $

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

$Let\ x_1=Y(s)\ \to x_1'=  sY(s)= x_2$\
$Let\ x_2=sY(s)\ \to x_2'=s^2Y(s)= x_3$\
$Let\ x_3=s^2Y(s) \to x_3'=s^3Y(s) = x_4$\
$Let\ x_4=s^3Y(s) \to x_4'=s^4Y(s) = -a_4 Y(s) -a_3 sY(s) -a_2 s^2Y(s) -a_1 s^3Y(s)= -a_4 x_1 -a_3 x_2 -a_2 x_3 -a_1 x_4 + u $

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


<table>
  <tr>
    <td style="width: 50%;">
      # 첫 번째 섹션 제목
      첫 번째 섹션의 내용을 작성하세요.
    </td>
    <td style="width: 50%;">
      # 두 번째 섹션 제목
      두 번째 섹션의 내용을 작성하세요.
    </td>
  </tr>
</table>


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

sys_ss.a =
               x1          x2          x3          x4
   x1   -3.44e-15   2.365e-14  -1.879e-15           2
   x2         -10   1.776e-15  -2.365e-14        -4.2
   x3           <code><b>0</b></code>         -10  -1.774e-15        13.2
   x4           <code><b>0</b></code>           <code><b>0</b></code>         -10       -1000

sys_ss.b =
              u1
   x1          2
   x2    -0.0002
   x3  4.729e-15
   x4          <code><b>0</b></code>

sys_ss.c =
       x1  x2  x3  x4
   y1   <code><b>0</b></code>   <code><b>0</b></code>   <code><b>0</b></code>  -1

sys_ss.d =
       u1
   y1   <code><b>0</b></code>
>> 
>> sys_ssn
                                                                                                               
sys_ssn.a =
           x1      x2      x3      x4
   x1       <code><b>0</b></code>   3.182       <code><b>0</b></code>       <code><b>0</b></code>
   x2       <code><b>0</b></code>       <code><b>0</b></code>   0.132       <code><b>0</b></code>
   x3       <code><b>0</b></code>       <code><b>0</b></code>       <code><b>0</b></code>    1000
   x4  -4.762  -3.182  -0.132   -1000

sys_ssn.b =
       u1
   x1   <code><b>0</b></code>
   x2   <code><b>0</b></code>
   x3   <code><b>0</b></code>
   x4   1

sys_ssn.c =
              x1         x2         x3         x4
   y1      4.762  0.0001515          <code><b>0</b></code>          <code><b>0</b></code>

sys_ssn.d =
       u1
   y1   <code><b>0</b></code>  
</code>
</pre>

But there are unwanted non-zero values in the state space model produced by the tf2ss function.




# generalized method

$Let\ x_1=Y(s)\ \to x_1'=  sY(s)= x_2$\
$Let\ x_2=sY(s)\ \to x_2'=s^2Y(s)= x_3$\
$Let\ x_3=s^2Y(s) \to x_3'=s^3Y(s) = x_4$\
$Let\ x_4=s^3Y(s) \to x_4'=s^4Y(s) = -a_4 Y(s) -a_3 sY(s) -a_2 s^2Y(s) -a_1 s^3Y(s)= -a_4 x_1 -a_3 x_2 -a_2 x_3 -a_1 x_4 + u $

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

If the variables($\boldsymbol{\alpha, \beta, \gamma, \delta, \epsilon, \zeta, \eta, \theta}$) are modified to obtain "coefficient of the matrix = multiple of 2($2^n$)" after the continuous-to-discrete conversion,\
the computational load can be reduced.

<p>${\large{\color{#DD6565}It\ isn't\ logic\ to\ do\ "Let",\ so\ there\ is\ a\ problem\ in\ mathmatics.}}$</p>

2</sup>/</sup>3

$\ \boldsymbol{\alpha\beta\gamma\delta\epsilon\zeta\eta\theta} \$

# License
Educational Use License

This software is provided for **educational and research purposes only**.
You may use, modify, and distribute this code **only in a non-commercial
educational setting**. Any commercial use, including but not limited to
selling, licensing, or incorporating this software into commercial products,
is strictly prohibited without explicit permission from the author.

For inquiries regarding commercial use, please contact [Your Email].

