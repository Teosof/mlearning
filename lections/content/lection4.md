# Лекция №4. Python и APL

## Сравнение способов обработки данных

Python:

```python
from numpy import (
    arange,
    add,
    vstack,
    hstack,
    multiply,
    shape
)

x = arange(1, 5)
print(x)
sum(x)
add(x, x)
add.reduce(x)
m = add.outer([2, 1])
print(m)
print(sum(m))
add.reduce(m)
add.reduce(m, axis=1)
multiply.reduce(m, axis=1)
print(shape(m))  # print(m.shape)
m1 = m
m1.shape = [4, 2]
print(m1)
print(vstack((m, m)))
mm = vstack((m, m))
print(mm.shape)
mm = hstack((m, m))
print(mm)
print(mm.shape)
```

APL:

```apl
    x←⍳4
    +/x
10
    x+x
2 4 6 8   
    m ← 2 1 ∘.+x
    +/m
18 14
    +/[1]m  ⍝+⌿m
5 7 9 11
    +/m
18 14
    ×/m
360 120
    ⍴m
2 4
    m1←4 2 ⍴m
    mm←m,[1]m
    mm
3 4 5 6
2 3 4 5
3 4 5 6
2 3 4 5
    ⍴mm
4 4
    mm←m,m
    mm
3 4 5 6 3 4 5 6
2 3 4 5 2 3 4 5
    ⍴x+.×m1
35 45
```

## Сравнение функций на Python и APL

**Сглаживание выборки** — это преобразование значений выборки таким образом, чтобы уменьшить их разброс относительно
соседних с целью выявления основной тенденции их поведения.

Крайние значения, не подвергшиеся обработке, заменяются ближайшим преобразованным значением.

**Ганнировние** – это сглаживание путём замены каждого значения выборки на взвешенное среднее между ним и двумя
соседними, причём вес при основном значении в 2 раза больше веса соседних. Взвешенное среднее (мера положения):
$$
y_{i}=\frac{x_{i-1}+x_{i+1}}{2}\times 0.5=0.25x_{i-1}+x_{i}+0.5x_{i}+0.25x_{i+1}
$$

$$
hann(x_{i})=0.5x_{i}+0.25(x_{i-1}+x_{i+1}))
$$

Python:

```python
from numpy import (
    vstack,
    hstack
)


def med3(x):
    y = vstack((x[:-2], x[1:-1], x[2:]))
    y = y.sum(axis=0) - (y.min(axis=0) + y.max(axis=0))
    return hstack((x[0], y, x[-1]))


def hann(x):
    y = 0.25 * x[:-2] + 0.5 * x[1:-1] + 0.25 * x[2:]
    return hstack((x[0], y, x[-1]))
```

APL:
```apl
    hann←{1⌽x[⌽1,⍴x],x←.25 .5 .25+.×⍉⊃3,/⍵}
    hann
{1⌽x[⌽1,⍴x],x←.25 .5 .25+.×⍉⊃3,/⍵}
    med3←{(+/m)-(⌈/m)+⌊/m←⊃3,/⍵}
    med3
{(+/m)-(⌈/m)+⌊/m←⊃3,/⍵}
```

## Пример сглаживания ганнированием

```apl
    ]load stat
#.stat
    ]load plt
#.plt
    ⎕path← 'plt stat' t←0,.1×⍳200
    s←1○0,.1×⍳200 sd←s+.3×stat.rnd ⍴s sd←sd-0.15
    1 0 1 plot t s sd (hann sd)
```

![Синусода с зашумлением](/Users/roman/PycharmProjects/machine_learning/lections/images/lection4/1.png)

## Сглаживание медианой по тройкам

1. Мы берём 3 последовательно расположенных значения выборки.
2. Находим медиану (это будет одно из этих значений).
3. Заменяем центральное значение этой медианой.

```apl
    1 0 1 plot t s sd (med3 sd)
```

![Сглаживание медианой по тройкам](/Users/roman/PycharmProjects/machine_learning/lections/images/lection4/2.png)

**Замечание**. В случае наличия выброса при ганнировании искажаются точки, соседние с выбросом, в то время как медиана
по тройкам просто отбрасывает его.