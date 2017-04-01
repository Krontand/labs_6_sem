# Лабораторная работа №3 по моделированию
### Постановка задачи
Физическая суть лабораторной: необходимо рассчитать температуру на всей длине стержня, который нагревают с одного из торцов.

![](https://4.downloader.disk.yandex.ru/disk/a62e92dde1940ab0dd5e13a9a876e61c416950764a82cf8252830f5dfc09f7d3/58e04e13/m8c41i0kWxNC1MHB2salV-ckta-UvEVi_sf8fdWtUBttPGhs0dPMytnZDuj0bJxAhYDtgupEuM8_pI-4LjUA-w%3D%3D?uid=0&filename=%D1%84%D1%81.png&disposition=inline&hash=&limit=0&content_type=image%2Fpng&fsize=2613&hid=3865f30a2fc9f97acc6b6b10696311ed&media_type=image&tknv=v2&etag=3b7fa32f8bab86a673cb5587ba63bf0c)

Исходные данные:
* l = 10см (Длина стержня)
* R = 0.5см (Радиус стержня)
* Tenv = 300К (Температура окружающей среды)
* F0 = 100 Вт / (см^2 * К) (Плотность теплового потока)
* k0 = 0.1 Вт / (см * К) (Коэффициент теплопроводности в начале стержня)
* kN = 0.2 Вт / (см * К) (Коэффициент теплопроводности в конце стержня)
* a0 = 1e-2  Вт / (см^2 * К) (Коэффициент теплоотдачи в начале стержня)
* aN = 0.9e-2  Вт / (см^2 * К) (Коэффициент теплоотдачи в конце стержня)

Все параметры могут быть изменены из интерфейса

Рассчитывать T(x) будем при помощи подготовленного уравнения теплопроводности:

![](http://latex.codecogs.com/svg.latex?\frac{d}{dx}\left&space;(&space;k(x)\frac{dT}{dx}&space;\right&space;)&space;-&space;\frac{2\alpha(x)}{R}T(x)&space;&plus;&space;\frac{2\alpha(x)}{R}*Tenv&space;=&space;0)

И заданных граничных условий:

![](http://latex.codecogs.com/svg.latex?(x&space;=&space;0)&space;\Rightarrow&space;-k\frac{dT}{dx}&space;=&space;F_0)

![](http://latex.codecogs.com/svg.latex?(x&space;=&space;l)&space;\Rightarrow&space;-k\frac{dT}{dx}&space;=&space;\alpha(T(l)&space;-&space;T_{env}))
