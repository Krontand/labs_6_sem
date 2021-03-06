# Лабораторная №2 по моделированию

## Условие лабораторной

Дан колебательный контур с газоразрядной трубкой

![](/assets/l2_scheme.png)

Из некоторых физических соображений получена система дифференциальных уравнений:

![A](/assets/2_1.png)

![B](/assets/2_2.png)

Ее необходимо решить (численно) и построить графики:

* I(t)   - сила тока в цепи
* Uc(t)  - напряжение на конденсаторе 
* Ucp(t) - напряжение на лампе
* Rp(t)  - сопротивление лампы


Rp, оно же сопротивление газоразрядной трубки, находится в зависимости от силы тока:

![](/assets/2_3.png)

Давление _p_ в формуле находится из следующего уравнения:

![](/assets/2_4.png)

Функция T(r):

![](/assets/2_5.png)

Электропроводность `σ(T, p)`, концентрация тяжелых частиц `n(T, p)`, а также `T0(I)` и `m(I)` заданы таблично.

### Исходные данные

Задается шаг по времени и количество узлов.

Параметры могут меняться из интерфейса.
* Rk = 0.2 Ом (Сопротивление)
* Lk = 60e-6 Гн (Индуктивность)
* Ck = 150e-6 Ф (Емкость конденсатора)
* R = 0.35 см (Радиус трубки, фигурирует в верхней границе интеграла)
* p0 = 0.5 атм
* Tstart = 300 К (p0 и Tstart используются в уравнении для нахождения давления p)
* Tw = 2000К (Возможно, задавались другие значения, скажем, 3000К или еще что-нибудь, на результат влияет несильно)
* le = 12 см (Расстояние между электродами лампы)
* Uc0 = 3000 В (Напряжение на конденсаторе в начальный момент времени t = 0)
* I0 = 0 .. 2 А (Сила тока в цепи в начальный момент времени t = 0)

## Интерполяция табличных данных
***
Начнем с самой простой части. Для нахождения сопротивления постоянно потребуется брать данные из трех таблиц.

I|T0|m
-----|-------|----
0.5  |6400   |0.40
1    |6790   |0.55
5    |7150   |1.70
10   |7270   |3.0
50   |8010   |11.0
200  |9185   |32.0
400  |10010  |40.0
800  |11140  |41.0
1200 |12010  |39.0

Здесь все просто, используем обычную линейную интерполяцию:

![](/assets/2_6.png)
, где `(I0, T0)` и `(I1, T1)` - узлы таблицы, между которыми находится нужное значение.

_Замечание. Здесь и далее при интерполяции: если нужно проинтерполировать точку, выходящую за границы данных таблицы, то нужно взять две ближайшие к ней точки. Предположим, если нужно получить T0 для I = 1500А, то из таблицы мы возьмем точки с I = 1200А и I = 800A. Формулы при этом не меняются._

Аналогично находим `m(I)`
***
Также имеем еще две похожие таблицы - для электропроводности и концентрации частиц.
### Концентрация тяжелых частиц n

T|p = 5атм|p = 15атм|p = 25атм
---|---|---|---
1000 | 3.62e+01 | 1.086e+02 | 1.81e+02
1500 | 2.41e+01 | 7.242e+01 | 1.21e+02
2000 | 1.81e+01 | 5.431e+01 | 9.05e+01
2500 | 1.45e+01 | 4.345e+01 | 7.24e+01
3000 | 1.21e+01 | 3.621e+01 | 6.04e+01
3500 | 1.03e+01 | 3.104e+01 | 5.17e+01
4000 | 9.05e+00 | 2.716e+01 | 4.53e+01
4500 | 8.05e+00 | 2.414e+01 | 4.02e+01
5000 | 7.24e+00 | 2.173e+01 | 3.62e+01
6000 | 6.03e+00 | 1.810e+01 | 3.02e+01
7000 | 5.16e+00 | 1.550e+01 | 2.58e+01
8000 | 4.49e+00 | 1.350e+01 | 2.25e+01
9000 | 3.92e+00 | 1.188e+01 | 1.99e+01
10000 | 3.39e+00 | 1.044e+01 | 1.76e+01
11000 | 2.87e+00 | 9.089e+00 | 1.54e+01
12000 | 2.37e+00 | 7.784e+00 | 1.34e+01
13000 | 1.94e+00 | 6.564e+00 | 1.14e+01
14000 | 1.62e+00 | 5.516e+00 | 9.72e+00
15000 | 1.40e+00 | 4.695e+00 | 8.30e+00

### Коэффициент электропроводности σ

T|p = 5атм|p = 15атм|p = 25атм
---|---|---|---
2000 | 0.525e-3 | 0.309e-3 | 0.239e-3
3000 | 0.525e-2 | 0.309e-2 | 0.239e-2
4000 | 0.525e-1 | 0.309e-1 | 0.239e-1
5000 | 0.442e+0 | 0.270e+0 | 0.214e+0
6000 | 0.283e1 | 0.205e1 | 0.175e1
7000 | 0.741e1 | 0.606e1 | 0.545e1
8000 | 0.138e2 | 0.120e2 | 0.111e2
9000 | 0.220e2 | 0.199e2 | 0.188e2
10000 | 0.317e2 | 0.296e2 | 0.284e2
11000 | 0.428e2 | 0.411e2 | 0.399e2
12000 | 0.547e2 | 0.541e2 | 0.533e2
13000 | 0.664e2 | 0.677e2 | 0.677e2
14000 | 0.769e2 | 0.815e2 | 0.825e2
15000 | 0.861e2 | 0.938e2 | 0.965e2
16000 | 0.941e2 | 0.105eЗ | 0.109eЗ
17000 | 0.102eЗ | 0.115eЗ | 0.120eЗ
18000 | 0.112eЗ | 0.124eЗ | 0.131e3
19000 | 0.126eЗ | 0.135e3 | 0.142eЗ
20000 | 0.149eЗ | 0.150eЗ | 0.154eЗ

Здесь необходима интерполяция по двум переменным: линейная интерполяция по давлению и логарифмическая - по температуре.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e7/Bilinear_interpolation.png/220px-Bilinear_interpolation.png) 
Картинка с википедии "для ясности"
* Определяете "квадрат", в котором необходимо интерполировать
* Интерполируете по одной из осей (по аналогии с рисунком выше - находим значения R1 и R2
* Интерполируете по второй оси и получаете нужное значение

Я сначала интерполирую по температуре (хотя лучше было бы сначала по давлению, но кого это волнует?). 

От обычной линейной интерполяции эта отличается лишь тем, что интерполируем мы не значение функции f(x), а ее логарифм - log(f(x)). Затем нужно взять экспоненту и получится искомый результат.

![](/assets/2_7.png)

![](/assets/2_8.png)

Две формулы выше отличаются только значением давления (условно говоря, находим точку слева и точку справа).

И теперь интерполируем по второй оси:

![](/assets/2_9.png)

Все то же самое происходит для второй таблицы σ.

_Замечание. В идеале будет не считать логарифмы каждый раз, а завести вторую таблицу, в которой хранить заранее посчитанные логарифмы._

## Нахождение Rp(I)
***

**Силу тока надо брать по модулю, она может быть отрицательной!**

Для нахождения _Rp_ необходимо воспользоваться данной формулой:

![](/assets/2_10.png)
Однако в ней нам неизвестно давление _p_. Оно находится из следующего уравнения:

![](/assets/2_11.png)

В этом уравнении все величины заданы, неизвестно лишь _p_. 

Это уравнение вида f(x) = 0 можно решить методом дихотомии. 

_Замечание. Я не буду детально описывать метод половинного деления, потому что... ну просто не буду, и все._

Сначала необходимо локализовать корень, т.е. найти границы отрезка, гарантированно содержащего корень. Я, чтобы точно не пропустить корень, начинаю искать отрезок с _p = 1_ с шагом _h = 1_. Нам нужно найти такой отрезок, чтобы у функции на его концах были разные знаки, т.е. `f(p) * f(p+h) < 0`. После этого сокращаем интервал половинным делением, пока не достигнем нужной точности.

**О точности решения уравнения**

Часто в цикле пишут условие вида _while |r - l| > ε_. Таким образом мы задаем _абсолютную_ точность, и это некошерно. Лучше задавать _относительную_ точность, т.е. условие будет выглядеть как _while |(r-l)/x| > ε_, где _x = (r + l) / 2_.
Градов мне делал внушение на эту тему на втором курсе, возможно, в этот раз тоже вспомнит.

**Вычисление интеграла**

Каждый раз при вычислении функции нам необходимо считать интеграл 

![](/assets/2_12.png)

Для этого воспользуемся методом Симпсона

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/66c8c4e7dbfcaf9b3045745b4fd7b9240f1cae71)

Вообще для данного метода нужно любое нечетное количество узлов, но повелением свыше интеграл считается с использованием **41** узла.
В начале вычисления сопротивления _Rp_ (а именно ради него все это происходит, если кто забыл) стоит завести массив температур _T( r )_ из 41 элемента. Температура высчитывается по формуле:

![](/assets/2_13.png), где
_T0_ и _m_ считаются из таблицы (ага! именно здесь проявляется зависимость Rp от силы тока I), а Tw и R - константы.
Теперь можно считать интеграл по данной формуле Симпсона. _n(T, p)_ получается опять-таки из табличных значений.
***
После решения уравнения полученное значение давления _p_ можно подставить в формулу для Rp:

![](/assets/2_14.png)

Интеграл в ней считается аналогичным образом, массив температур можно использовать тот же самый, а σ(T, p) берется опять-таки из таблицы.

### Краткое повторение вышеописанного
***
1. Составить массив температур для интегрирования
2. Решить уравнение с интегралом для получения p
3. Подставить p в итоговую формулу и получить Rp

## Решение системы ОДУ
***
Наконец мы добрались непосредственно до решения дифференциальных уравнений.
По условию необходимо реализовать два метода: метод Рунге-Кутта 4 порядка и неявный метод трапеций.

В обоих методах начальные значения I0 и Uc0 заданы в исходных данных.

### Метод Рунге-Кутта 4 порядка для системы ОДУ
***
_Замечание. Посмотреть метод в общем виде можно в [гугле](http://www.codenet.ru/progr/alg/Runge-Kutt-Method/) или в лекциях._

Для начала выразим значения производных `dI/dt` и `dUc/dt` и для удобства обозначим их соответсвенно `f` и `g`.

![](/assets/2_15.png)

![](/assets/2_16.png)

Решение системы заключается в применении формул:

![](/assets/2_17.png)
![](/assets/2_18.png)

Коэффициенты k, m считаются следующим образом:

![](/assets/2_19.png)

![](/assets/2_20.png)

![](/assets/2_21.png)

![](/assets/2_22.png)

Помимо `I(t)` и `Uc(t)`, которые тут вычисляются, надо также вывести:
 * `Rp(t)` - по сути, это просто значение сопротивления _Rp_ для соответствующего значения силы тока _I_
 * `Ucp(t)` - это напряжение на трубке, считается как _Ucp = I * Rp_

### Неявный метод трапеций
***
Этот метод неявный и считается более устойчивым, однако тут будет всякая непотребная математика.

Возьмем уже знакомые уравнения:

![](/assets/2_23.png)

![](/assets/2_24.png)

Запишем выражения для метода:

![](/assets/2_25.png)

![](/assets/2_26.png)

Подставим выражения _f_ и _g_:

![](/assets/2_27.png)

![](/assets/2_28.png)

Ну зашибись (на самом деле нет), получили систему с двумя неизвестными: `In+1` и `Ucn+1`. Теперь нужно подставить выражение для `Ucn+1` из второго уравнения в первое. Получившийся трэш надо решить относительно `In+1`. Я забил это добро в вольфрам и получил следующий результат:

![](/assets/2_29.png)

Но и это еще не все: можно заметить, что в правой части фигурирует `Rp(In+1)`. Фактически, мы получили уравнение вида x = f(x). Решить данное уравнение можно методом простой итерации:

![](/assets/2_30.png)

Сначала в правую часть подставляется уже известный In. Полученное значение снова подставляем в правую часть и получаем уже новое значение, более точное. Операцию повторяем до тех пор, пока не будет достигнута необходимая точность (как ранее в методе половинного деления, но здесь наше условие выглядит как while  ![](http://www.matheboard.de/latex2png/latex2png.php?|(I^{(s)}-I^{(s-1)})/I^{(s)}|%3E\varepsilon).

После получения `In+1` находим `Ucn+1` по ранее указанной формуле:

![](/assets/2_31.png)

И не забываем про значения Rp и Ucp.


## Как выглядит решение

Для шага 1e-5 и 70 узлов

![](http://meson.ad-l.ink/82wtR2DNh/image.png)




