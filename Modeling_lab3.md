# Лабораторная работа №3 по моделированию
## Постановка задачи

Физическая суть лабораторной: необходимо рассчитать температуру на всей длине стержня, который нагревают с одного из торцов.

![](/assets/l3_scheme.png)

Исходные данные:
* l = 10см (Длина стержня)
* R = 0.5см (Радиус стержня)
* Tenv = 300К (Температура окружающей среды)
* F0 = 100 Вт / (см^2 * К) (Плотность теплового потока)
* k0 = 0.1 Вт / (см * К) (Коэффициент теплопроводности в начале стержня)
* kN = 0.2 Вт / (см * К) (Коэффициент теплопроводности в конце стержня)
* α0 = 1e-2  Вт / (см^2 * К) (Коэффициент теплоотдачи в начале стержня)
* αN = 0.9e-2  Вт / (см^2 * К) (Коэффициент теплоотдачи в конце стержня)

Все параметры могут быть изменены из интерфейса

Рассчитывать T(x) будем при помощи подготовленного уравнения теплопроводности:

![](http://latex.codecogs.com/svg.latex?\frac{d}{dx}\left&space;(&space;k(x)\frac{dT}{dx}&space;\right&space;)&space;-&space;\frac{2\alpha(x)}{R}T(x)&space;&plus;&space;\frac{2\alpha(x)}{R}*Tenv&space;=&space;0)

И заданных граничных условий:

![](http://latex.codecogs.com/svg.latex?(x&space;=&space;0)&space;\Rightarrow&space;-k\frac{dT}{dx}&space;=&space;F_0)

![](http://latex.codecogs.com/svg.latex?(x&space;=&space;l)&space;\Rightarrow&space;-k\frac{dT}{dx}&space;=&space;\alpha(T(l)&space;-&space;T_{env}))

## Коэффициенты теплопроводности и теплоотдачи

_Рассмотрим только нахождение коэффициента теплоотдачи, коэффициент теплопроводности будет вычисляться точно так же, надо только заменить везде α на k._
По условию коэффициент `α` есть функция от `х`:

![](http://latex.codecogs.com/svg.latex?\alpha&space;(x)&space;=&space;\frac{a}{x-b})

Нам неизвестны коэффициенты `a` и `b`, однако нам даны `α(0) = α0` и `α(l) = αN`. Нужно лишь составить систему из двух уравнений и решить ее. Задача достаточно проста, и приводить саму систему и ее решение не будем. Итак, из системы получаем коэффициенты `a` и `b`:

![](http://latex.codecogs.com/svg.latex?b&space;=&space;\frac{\alpha_Nl}{\alpha_N&space;-&space;\alpha_0})

![](http://latex.codecogs.com/svg.latex?a&space;=&space;-\alpha_0&space;b)

## Метод прогонки

Несколько заходя вперед, скажем, что для решения исходного уравнения надо будет получить разностную схему, из которой, в свою очередь, получится система из N уравнений, которую можно будет подогнать и решить методом прогонки. Метод прогонки дается в общем виде, и предполагается, что закодить ее надо также в общем виде.

Имеем систему уравнений следующего вида:

![](http://latex.codecogs.com/svg.latex?A_ny_{n-1}-B_ny_n&plus;C_ny_{n&plus;1}=-F_n,&space;1&space;\leq&space;n&space;\leq&space;N-1) (т.е. это не одно уравнение, а набор для n от 1 до N-1)

![](http://latex.codecogs.com/svg.latex?K_0y_0&plus;M_0y_1=P_0)

![](http://latex.codecogs.com/svg.latex?K_Ny_N&plus;M_Ny_{N-1}=P_N)

В качестве исходных данных в метод прогонки надо будет передавать:
* 4 массива: `An`, `Bn`, `Cn`, `Fn`
* 3 параметра для граничного условия слева: `K0`, `M0`, `P0`
* 3 параметра для граничного условия справа: `KN`, `MN`, `PN`

По сути, система решается в два прохода: прямой и обратный.

В прямом ходе вычисляем прогоночные коэффициенты `ξ` и `η` от 1 до :

![](http://latex.codecogs.com/svg.latex?\xi_{n&plus;1}&space;=&space;\frac{C_n}{B_n&space;-&space;A_n\xi_n})

![](http://latex.codecogs.com/svg.latex?\eta_{n&plus;1}&space;=&space;\frac{F_n&plus;A_n\eta_n}{B_n&space;-&space;A_n\xi_n})

Начальные значения `ξ` и `η` берутся из граничных условий:

![](http://latex.codecogs.com/svg.latex?\xi_1&space;=&space;-\frac{M_0}{K_0};&space;\eta_1&space;=&space;\frac{P_0}{K_0})

_Обратите внимание, что прогоночные коэффициенты начинаются с первого, а не с нулевого. И вообще осторожнее с индексами в этом методе, с ними связана большая часть багов._

Затем в обратном ходе вычисляется решение системы `y0...yN`:

![](http://latex.codecogs.com/svg.latex?y_n=\xi_{n&plus;1}y_{n&plus;1}&plus;\eta_{n&plus;1})

Начальное значение `yN` считается из граничных условий:

![](http://latex.codecogs.com/svg.latex?y_N=\frac{P_N&space;-&space;M_N\eta_N}{K_N&plus;M_N\xi_N})

## Получение разностной схемы из ДУ

Разностная схема в данном случае получается при помощи достаточно сильного колдунства и интегро-интерполяционного метода. Все формулы будут приведены в общем виде, но поменять функции на те, что даны в лабораторной будет несложно. Полный вывод можно найти в лекциях.

Итак, имеем уравнение:

![](http://latex.codecogs.com/svg.latex?(1):\frac{d}{dx}\left(&space;k(x)\frac{dU}{dx}\right&space;)-p(x)U&plus;f(x)=0)

Введем обозначение:

![](http://latex.codecogs.com/svg.latex?(2):F=-k(x)\frac{dU}{dx})

Тогда:

![](http://latex.codecogs.com/svg.latex?(3):-\frac{dF}{dx}-p(x)U&plus;f(x)=0)

Проинтегрируем **(3)** на ячейке:

![](http://latex.codecogs.com/svg.latex?-\int_{x_{n-1/2}}^{x_{n&plus;1/2}}\frac{dF}{dx}dx-\int_{x_{n-1/2}}^{x_{n&plus;1/2}}p(x)U(x)dx&plus;\int_{x_{n-1/2}}^{x_{n&plus;1/2}}f(x)dx=0)

Первый интеграл считается тривиально, а второй и третий проинтегрируем методом средних прямоугольников:

![](http://latex.codecogs.com/svg.latex?(4):F_{n-1/2}-F_{n&plus;1/2}-p_ny_nh&plus;f_nh=0)

Проинтегрируем **(2)**:

![](http://latex.codecogs.com/svg.latex?\int_{x_n}^{x_{n&plus;1}}\frac{F}{k(x)}dx=-\int_{x_n}^{x_{n&plus;1}}\frac{dU}{dx}dx)

...блджать, как будто это кто-то будет изучать.

### Суть

После определенных манипуляций имеем выражение вида

![](http://latex.codecogs.com/svg.latex?A_ny_{n-1}-B_ny_n&plus;C_ny_{n&plus;1}=-D_n)

которое нам и нужно для метода прогонки. Соответствующие коэффициенты вычисляются как:

![](http://latex.codecogs.com/svg.latex?A_n&space;=&space;\frac{X_{n-1/2}}{h})

![](http://latex.codecogs.com/svg.latex?C_n&space;=&space;\frac{X_{n&plus;1/2}}{h})

![](http://latex.codecogs.com/svg.latex?B_n&space;=&space;A_n&plus;C_n&plus;p_nh)

![](http://latex.codecogs.com/svg.latex?D_n=f_nh)

При этом

![](http://latex.codecogs.com/svg.latex?X_{n&plus;1/2}&space;=&space;\frac{2k_nk_{n&plus;1}}{k_n&plus;k_{n&plus;1}};X_{n-1/2}&space;=&space;\frac{2k_nk_{n-1}}{k_n&plus;k_{n-1}})

![](http://latex.codecogs.com/svg.latex?k_n=k(x_n))

![](http://latex.codecogs.com/svg.latex?p_n=\frac{2\alpha(x_n)}{R})

![](http://latex.codecogs.com/svg.latex?f_n=\frac{2\alpha(x_n)}{R}T_{env})

## Граничные условия

Нам уже известны все параметры, кроме коэффициентов K0, M0, P0 левого граничного условия и KN, MN, PN правого граничного условия.

### Левое граничное условие

Для получения уравнения вида `K0*y0 + M0*y1 = P0` проинтегрируем **(3)** на половине первого шага:

![](https://latex.codecogs.com/svg.latex?\int_{0}^{x_{1/2}}\frac{dF}{dx}dx-\int_{0}^{x_{1/2}}p(x)U(x)dx&plus;\int_{0}^{x_{1/2}}f(x)dx=0)

_Не особо понял, куда делся минус в первом интеграле, но так дано в лекциях_

Первый интеграл раскрывается тривиально, а второй и третий посчитаем численно методом трапеций (с одним интервалом).

![](https://latex.codecogs.com/svg.latex?F_{1/2}-F_0-\frac{p_{1/2}y_{1/2}&plus;p_0y_0}{2}\frac{h}{2}&plus;\frac{f_{1/2}&plus;f_0}{2}\frac{h}{2}=0)

Из полученного при выводе разностной схемы (эта часть вывода пока опущена) имеем:

![](https://latex.codecogs.com/svg.latex?F_{1/2}=X_{1/2}\frac{y_0-y_1}{h})

`F0` мы берем из заданного граничного условия (фактически, это тот самый `F0`, который значится в исходных данных). Подставляем значения в уравнение, приводим к благообразному виду и имеем:

![](https://latex.codecogs.com/svg.latex?(X_{1/2}&plus;\frac{h^2}{8}p_{1/2}&plus;\frac{h^2}{4}p_0)y_0&space;&plus;&space;(\frac{h^2}{8}p_{1/2}-X_{1/2})y_1&space;=&space;hF_0&plus;\frac{h^2}{4}(f_{1/2}&plus;f_0))



Соответственно, коэффициент при y0 будет K0, коэффициент при y1 - M0, и справа стоит P0.

### Правое граничное условие

Его вывод на лекциях не давался - нужно сделать самостоятельно и показать при сдаче.

Итак, похожим образом интегрируем на половине теперь уже последнего интервала:

![](http://latex.codecogs.com/svg.latex?-\int_{x_{N-1/2}}^{x_N}\frac{dF}{dx}dx-\int_{x_{N-1/2}}^{x_N}p(x)U(x)dx&plus;\int_{x_{N-1/2}}^{x_N}f(x)dx=0)

_Минус перед первым интегралом снова тут. Почему? Чтоб я знал, но он есть в примере для правого граничного условия в лекциях_

Также раскрываем второй и третий интегралы методом трапеций:

![](http://latex.codecogs.com/svg.latex?F_{N-1/2}-F_N-\frac{p_{N-1/2}y_{N-1/2}&plus;p_Ny_N}{2}\frac{h}{2}&plus;\frac{f_{N-1/2}&plus;f_N}{2}\frac{h}{2}=0)

Имеем по выведенной формуле:

![](http://latex.codecogs.com/svg.latex?F_{N-1/2}=X_{N-1/2}\frac{y_{N-1}-y_N}{h})

Из граничного условия имеем:

![](http://latex.codecogs.com/svg.latex?F_N=\alpha(T(l)-T_{env})=\alpha(y_N-T_{env}))

Подставляем все это в уравнение:

![](http://latex.codecogs.com/svg.latex?X_{N-1/2}\frac{y_{N-1}-y_N}{h}-\alpha(y_N-T_{env})-(p_Ny_N&plus;\frac{p_{N-1}&plus;p_N}{2}\frac{y_{N-1}&plus;y_N}{2})\frac{h}{4}&plus;(f_N&plus;\frac{f_N&plus;f_{N-1}}{2})\frac{h}{4}=0)

Приводим к "каноничному" виду:

![](http://latex.codecogs.com/svg.latex?(-\frac{X_{N-1/2}}{h}-\alpha-\frac{p_Nh}{4}-h\frac{p_{N-1&plus;p_N}}{16})y_N&plus;(\frac{X_{N-1/2}}{h}-h\frac{p_{N-1&plus;p_N}}{16})y_{N-1}=-\alpha&space;T_{env}-h\frac{3f_N&plus;f_{N-1}}{8})

Коэффициенты будут соответственно KN, MN, PN.

Теперь вся задача сводится к тому, чтобы вычислить 4 массива, 6 параметров из граничных условий и передать и в метод решения прогонкой.
На выходе имеем искомый массив T(x).



