
# Сортировки. Derandomized Quick Sort. LSD radix sort

## Небольшое замечание по quick select

Quick Select можно реализовать с привлечением $O(1)$ доп. памяти. (Время $O(n)$ в среднем)

Требуется написать эффективный `partition` для разбиения массива на три части

Возможный алгоритм разбиения за линейное время:

<img src="../images/img7.png" alt="Рис. 4141" style="width:70%;">

Таким образом получаем следующий массив

<img src="../images/img8.png" alt="Рис. 1" style="width:70%;">

К тому же можно избавиться от рекурсии. Достаточно хранить индексы той части массива, где содержится $k$-ая статистика. для этого сдвигаем левый или правый указатель в массиве. (Что это такое и как это найти есть в прошлой лекции) Избавление от рекурсии ведет к освобождению памяти в стеке рекурсии (+ к скорости и эффективности)

## Derandomized q select.

Как взять настоящий случайный элемент? Как компьютер выбирает случайный элемент? Как вообще работает случайность в компьютере? Невозможно взять настоящую, честную случайность в математике. Поэтому полезно рассмотреть qs без этой случайности.

Хотелось бы чтобы `pivot` был как можно ближе к центральному элементу массива.

<ins> Опр.</ins> $a_1, a_2, ..., a_n$ - отсортированный массив. Его медианой называют $\displaystyle a_{\lceil \frac{n}{2} \rceil}$

Предъявим алгоритм, который сможет находить хорошую медиану и помогает выбирать хороший такой `pivot` 

### <ins> Алгоритм.</ins> Медиана медиан. $DQS (a_1, ... , a_n, k):$ Derandomized Quick Select


<img src="../images/img9.png" alt="Рис. 1" style="width:100%;"> 

После чего находить $k$-ую статистику можно и без случайности


<img src="../images/img10.png" alt="Рис. 1" style="width:70%;">

<ins> Утв.</ins>  Пусть $T(n)$ - время работы $DQS$ на массиве длины $n$. Тогда $T(n) = O(n)$ 


<ins> Док-во.</ins>  

$X$ является порядковой статистикой массива с номером $\in [\frac{3}{10}n, \frac{7}{10}n]$

<img src="../images/img11.png" alt="Рис. 1" style="width:70%;">

<img src="../images/img12.png" alt="Рис. 1" style="width:70%;">

$T(n)$ складывается из разбиение массива на пятерки (первое слагаемое), сортировку пятерок ($\frac{n}{5}$ раз) и нахождение $x$ (второе слагаемое), Partition (третье слагаемое) и рекурсивный спуск на одной из частей где находится $k$-ая статистика (там может быть максимум $\frac{7n}{10}$ элементов) (четвертое слагаемое)

$$T(n) = O(n) + T(\frac{n}{5}) + O(n) + T(\frac{7n}{10}) \leq T(\frac{n}{5}) + T(\frac{n}{5}) + Cn$$

Докажем что $T(n) \leq 10 \cdot Cn$, $\forall C$

 База $n \leq 5$ очев.
 Переход.
  $$T(n) \leq T(\frac{n}{5}) + T(\frac{7n}{10}) + Cn = 10Cn$$
  
 
  Так как $T(\frac{n}{5}) \leq 10 \cdot C \frac{n}{5}$ и $T(\frac{7n}{10}) \leq 10 \cdot C \frac{7}{10}$
## Derandomized q sort

Если в качестве `pivot` выбирать $DQS(A, \frac{n}{2})$, то при подстановки его в `Partition` то выйдет что массив разбивается на две части $\frac{n}{2}$ 


<img src="../images/img13.png" alt="Рис. 1" style="width:60%;">

## Сортировки чисел

До этого все сортировки были основаны лишь на том, что элементы можно сравнивать. В такой установке быстрее чем за $O(n \cdot logn)$ сортировать нельзя.

<ins> Задача.</ins> Пусть $a_1, a_2, ... , a_n$ - массив натуральных чисел $\in \{ 0, 1, 2, ... ,k \}$. Отсортировать $a$

<ins> Решение.</ins>

```cpp
cnt[k + 1] = { 0, 0, ... , 0 }
for i = 1 .. n
	++cnt[a[i]]
for x = 0 .. k
	for i = 1 ... cnt [x]
		print(x)
```

Если рассматривать задачу сортировки как нахождение перестановки индексов, то можно ввести понятие стабильности сортировки.

<ins> Опр.</ins>  Стабильная сортировка
$$a_1, a_2, ... , a_n \Rightarrow a_{\sigma(1)}, a_{\sigma(2)}, ..., a_{\sigma(n)}$$
При условии, что равные элементы сохраняют свой относительный порядок, т.е. если 

$a_{\sigma(i)} = a_{\sigma(j)}$ и $i < j$, то $\sigma(i) < \sigma(j)$

<ins> Задача.</ins> Реализовать стабильную сортировку подсчетом.

```c++
cnt[k + 1] = {0, ..., 0}
for i = 1 ... n
	++cnt[a[i]]
for x = 1 ... k
	cnt[x]+=cnt[x-1] // cnt[x] - кол-во элеметов <= x
```

Это значит, что известно куда положить самый правый $x$

<img src="../images/img14.png" alt="Рис. 1" style="width:60%;">

<ins> Пример.</ins>

<img src="../images/img15.png" alt="Рис. 1" style="width:60%;">

тогда дописать код можно таким образом
```c++
for i = n ... 1
	x = a[i]
	ans[cnt[x]] = x // sigma_cnt[x] = i
	--cnt[x]
```

Получился алгоритм, работающий за $O(n + k)$.

***

<ins> Задача.</ins> Дан массив пар чисел $(a_1, b_1), (a_2, b_2), ... , (a_n,b_n)$ Требуется их отсортировать 

Правило сортировки пар:

$$(x_1, y_1) < (x_2, y_2) \iff \begin{gathered} x_1 < x_2 \\\text{ или }\\ x_1 = x_2, y_1 < y_2 \\ \end{gathered}$$

Для решения этой задачи требуется устойчивая сортировка. 

<ins> Решение.</ins> 
1) Отсортируем массив по $b$.
2) Результат <ins>стабильно</ins> отсортируем по $a$ 

## Least Signigicant Digit Sort 

<ins> Задача.</ins> Отсортировать массив чисел $a_1 \in \{ 0, 1, ..., k\}$, НО k ооочень большое. 

В целом та же идея, только с парами чисел, только с числами, которые мы представили как строчки :)
