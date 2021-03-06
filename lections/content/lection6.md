# Лекция №6. Метод Орлочи

- метод понижения размерности пространства.

Рассмотрим пример на плоскости. У нас есть пять точек, мы находим две наиболее удаленные друг от друга, их координаты - $(x_i, x_j)$. Из всех точек, которые у нас есть вычитаем первую, то есть $x_i$. В таком случае первая координата будет равна $x_j-x_i$ - это $e_1$ или, другим словами, первая ось Орлочи. Имя полученную координат, проектируем эти точки. Заметим, что мы получили одномерное распределение вдоль оси. Для него мы можем построить гистограмму и проверить есть ли какие-нибудь особенности или группировки. 

Проведем вторую ось Орлочи, чтобы получить плоскость. Вторая ось $e_2$ проводится через точку, которая максимально удалена от первой оси, а именно через точку $x_k$ и перпендикулярно первой оси.

![Метод Орлочи](/Users/roman/PycharmProjects/machine_learning/lections/images/lection6/1.png) 

Отметим точки $k, i, j$. Одна лежит практически на оси $e_1$, другая имеет отрицательную координату. Повернем данное распределение на плоскости. 

![Метод Орлочи](/Users/roman/PycharmProjects/machine_learning/lections/images/lection6/2.png) 

## Многомерный случай

Цель методов многомерного шкалирования — представление исходных данных в пространстве небольшой размерности с сохранением структуры выборки (взаимного расположения точек). Наибольшее изменение выборки должно быть вдоль оси, которая соединяет две наиболее удаленные точки. 

### Описание алгоритм

Пусть в $n$-мерном пространстве признаков заданы $m$ образов:
$$
x_1,x_2,...x_m
$$
Вычислим евклидовы расстояния между всеми точками (образами):
$$
R^2_{ij}=\sum_{k=1}^n(x_{ik}-x_{jk})^2
$$
где $x_{ik}$ - $k$-ая координата образа $x_i$:
$$
R_{ij}=\underset{p,q}{argmax}({R_{pq}})
$$

$$
p,q=1,2,...,m,p\neq q
$$

![Иллюстрации к выводу проекций Орлочи](/Users/roman/PycharmProjects/machine_learning/lections/images/lection6/3.png)

Проведём первую ось $e_1$ искомого подпространства через эти наиболее удаленные точки, перенеся начало координат в точку $x_i$. Это соответствует переходу $y_k=x_k-x_i$, где ось $e_1$ совпадает с вектором $y_j$. 

## Понижение размерности данных

Рассчитаем расстояние всех точек выборки до оси $e_j$. Пусть перпендикуляр $y$, который опущен из конца вектора $x_k$ на ось $e_1$ опирается на конец вектора $\alpha e_1$, где $\alpha$ - неизвестный множитель. Из условия ортогональности:
$$
\alpha e'_1y=\alpha e'_1(x_k-\alpha e_1)=0
$$

$$
\alpha=\frac{e'_1x_k}{e'_1e_1}
$$

Определим расстояние от точки $x_k$ до оси $e_1$:
$$
R_k=\left\|x_k-\alpha e_1\right\|
$$
Определим наиболее удаленную от оси $e_1$ точку и проведем вторую ось $e_2$ искомого подпространства через эту точку перпендикулярно к оси $e_1$. Если таковой оказалась точка $x_k$, то необходимо перенести начало координат в точку $\alpha e_1$, что приведёт к осям:
$$
e_1=e_1-\alpha e_1
$$

$$
e_2=x_k-\alpha e_1
$$

## Программная реализация

![Проекция на оси Орлочи](/Users/roman/PycharmProjects/machine_learning/lections/images/lection6/4.png)

## Линейное шкалирование

```apl
    x←⊂[2]s ⍝ преобразование матрицы спектов акустических шумов 40х200 в вектор векторов 
    r←+/¨(x∘.-x)*2 ⍝ евклидово расстояние между всеми точками
20 10
    1+(⍴r)⊤¯1+(,r)⍳⌈/,r ⍝ координаты наиболее удаленных точек $x_i=20,x_j=103$
    y←x-x[20] ⍝ подсчёт $y$ и вычитанием значения 20 точки
    y←+/¨y×y[103] ⍝ подсчёт расстояние проекции на вторую координату
    21 hist y ⍝ гистограмма
```
![Гистограмма](/Users/roman/PycharmProjects/machine_learning/lections/images/lection6/5.png)

### Проекция на плоскость
![Листинг метода](/Users/roman/PycharmProjects/machine_learning/lections/images/lection6/6.png)

![Проекция на плоскость](/Users/roman/PycharmProjects/machine_learning/lections/images/lection6/7.png)

На распределении видим, что все данные группируется в два кластера, которые надо интерпретировать.

## Пример
### Ручное исполнение
Вариант №5
$$
(4,2,6,3)(5,5,9,2)(2,3,6,2)
$$

$$
R^2_{12}=20,R^2_{13}=6,R^2_{23}=22
$$

$$
R^2_{23}=22 - max
$$

- вычитаем вторую точку:
  $$
  (-1,-3,-3,-1)(0,0,0,0)(-3,-2,-3,0)
  $$

- умножаем на третью точку:
  $$
  (3+6+9+0)(0+0+0+0)(9+4+9+0)
  $$

- Ответ:
  $$
  [18, 0, 2]
  $$
### Программное исполнение
```apl
    x←(4 2 6 3)(5 5 9 2)(2 3 6 2)
    +/¨x
15 21 13
    R←+/¨(x ∘.- x)*2
    R
 0 20  6
20  0 22
 6 22  0
    ⍸R=⌈/,R
 2 3  3 2 
    ⌈/,R
22
    y←x-x[2]
      y
 ¯1 ¯3 ¯3 1  0 0 0 0  ¯3 ¯2 ¯3 0 
    y×y[3]
 3 6 9 0  0 0 0 0  9 4 9 0 
    +/¨y×y[3]
18 0 22
    Right
```

