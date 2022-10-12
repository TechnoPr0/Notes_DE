# Notes_DE
Записки начинающего Инженера данных
___
</br> 

## Библиотека NumPy
NumPy (Numeric Python) - предоставляет базовые методы для работы с большими массивами и матрицами. 
SciPy (Scientific Python) - расширяет функционал NumPy огромной коллекцией полезных алгоритмов.

Установка Numpy:
```bash
pip install numpy
```

установка SciPy:
```bash
pip install scipy
```
Импортировать библиотеку NumPy:
```python
import numpy as np
```
Дописывать as np не обязательно, это нужно для облегчения доступа к numpy объектам.  Вместо ```numpy.x``` нужно будет написать ```np.x```
___
</br>

### Массивы
Главной особенностью numpy является объект array

___
</br>

## ***Библиотека Pandas.***
</br>
### DataFrame
Объект DataFrame представляет собою табличную структуру данных, состоящую из упорядоченной коллекции столбцов, причем типы значений (числовой, строковый, булевый и.д.) в разных столбцах могут различаться.
	В объекте DataFrame хранятся два индекса:
по строкам.
по столбцам.
Можно считать, что это словарь объектов Series.

Есть много способов сконструировать объект DataFrame, один из самых распространённых  – на основе словаря списков одинаковой длины или массивов NumPy

```python
data = {‘state’: [‘Ohio’, ‘Ohio’, ‘Ohio’, ‘Nevada’, ‘Nevada’, ‘Nevada’],
		‘year’: [2000, 2001, 2002, 2001, 2002, 2003],
		‘pop’: [1.5, 1.7, 3.6, 2.4, 2.9, 3.2]}
frame = pd.DataFrame(data)
```
Выглядеть это будет следующим образом:
```python
    state  year  pop
0    Ohio  2000  1.5
1    Ohio  2001  1.7
2    Ohio  2002  3.6
3  Nevada  2001  2.4
4  Nevada  2002  2.9
5  Nevada  2003  3.2
```

Если задать последовательность столбцов, то столбцы DataFrame расположатся в строго указанном порядке:

```python
frame = pd.DataFrame(data, columns = ['year', 'state', 'pop'])
   year   state  pop
0  2000    Ohio  1.5
1  2001    Ohio  1.7
2  2002    Ohio  3.6
3  2001  Nevada  2.4
4  2002  Nevada  2.9
5  2003  Nevada  3.2
```

Если запросить столбец которого нет, то он будет заполнятся значениями NaN:

```python
frame2 = pd.DataFrame(data, columns = ['year', 'state', 'pop', 'debt'],
                      index = ['one', 'two', 'three', 'four', 'five', 'six'])
```
```python
       year	state	pop	debt
one    2000	Ohio	1.5	NaN
two    2001	Ohio	1.7	NaN
three  2002	Ohio	3.6	NaN
four   2001	Nevada	2.4	NaN
five   2002	Nevada	2.9	NaN
six    2003	Nevada	3.2	NaN
```

Столбец DataFrame можно извлечь как объект Series, воспользовавшись нотацией словарей или с помощью атрибута.

Строки тоже можно извлечь, для этого есть метод loc:
```python
print (frame2.loc['three'])

year     2002
state    Ohio
pop       3.6
debt      NaN
Name: three, dtype: object
```
Столбцы можно модифицировать путём присваивания. Например, пустому столбцу можно ‘debt’ можно было бы присвоить скалярное значение или массив значений:

```python
frame2['debt']=16.5
print (frame2, '\n')
frame2['debt'] = [i for i in range(1,7)]
print (frame2)

      year   state  pop  debt
one    2000    Ohio  1.5  16.5
two    2001    Ohio  1.7  16.5
three  2002    Ohio  3.6  16.5
four   2001  Nevada  2.4  16.5
five   2002  Nevada  2.9  16.5
six    2003  Nevada  3.2  16.5 

       year   state  pop  debt
one    2000    Ohio  1.5     1
two    2001    Ohio  1.7     2
three  2002    Ohio  3.6     3
four   2001  Nevada  2.4     4
five   2002  Nevada  2.9     5
six    2003  Nevada  3.2     6
```

Когда столбцу присваивается список или массив, длина значения должна совпадать с длиной DataFrame

Если же присваивается объект Series, то он будет точно согласован с индексом DataFrame, а в пустые места будут вставлены значения NaN:

```python
val = pd.Series([-1.2, -1.5, -1.7], index = ['two', 'four', 'five'])
frame2['debt'] = val
print(frame2)


      year   state  pop  debt
one    2000    Ohio  1.5   NaN
two    2001    Ohio  1.7  -1.2
three  2002    Ohio  3.6   NaN
four   2001  Nevada  2.4  -1.5
five   2002  Nevada  2.9  -1.7
six    2003  Nevada  3.2   NaN
```

Присваивание несуществующему столбцу приводит к созданию нового столбца. Для удаления столбцов  служит ключевое слово del, как и в обычном словаре:

```python
frame2['eastern'] = frame2.state == 'Ohio'
print(frame2)

      year   state  pop  debt  eastern
one    2000    Ohio  1.5   NaN     True
two    2001    Ohio  1.7  -1.2     True
three  2002    Ohio  3.6   NaN     True
four   2001  Nevada  2.4  -1.5    False
five   2002  Nevada  2.9  -1.7    False
six    2003  Nevada  3.2   NaN    False

del frame2['eastern']
print(frame2.columns, '\n')
print(frame2)



Index(['year', 'state', 'pop', 'debt'], dtype='object') 



       year   state  pop  debt
one    2000    Ohio  1.5   NaN
two    2001    Ohio  1.7  -1.2
three  2002    Ohio  3.6   NaN
four   2001  Nevada  2.4  -1.5
five   2002  Nevada  2.9  -1.7
six    2003  Nevada  3.2   NaN
```


Ещё одна  распространённая форма данных – словарь словарей. Если передать его конструктору, то ключи внешнего словаря будут интерпретированы как столбцы, а ключи внутреннего словаря как индексы строк:

```python
pop = {'Nevada':{2001:2.4,2002:2.9}, 
       'Ohio':{2001:1.7, 2002:3.6, 2000:1.5}
       }
frame3 = pd.DataFrame(pop)
frame3


     Nevada  Ohio
2001     2.4   1.7
2002     2.9   3.6
2000     NaN   1.5
```

Словари объектов Series интерпретируются очень похоже:

```python
pdata = {'Ohio':frame3['Ohio'][:-1],
         'Nevada':frame3['Nevada'][:2]}
pd.DataFrame(pdata)



     Ohio  Nevada
2001   1.7     2.4
2002   3.6     2.9
```


Если у объектов, возвращаемых при обращении к атрибутам index и columns объекта DataFrame, устанавливается атрибут name, то он также выводится:
```python
frame3.index.name = 'year'; frame3.columns.name = 'state'
print(frame3)


state  Nevada  Ohio
year               
2001      2.4   1.7
2002      2.9   3.6
2000      NaN   1.5
```

Как и в случае с Series, атрибут values возвращает данные, хранящиеся в DataFrame, в виде двумерного массива ndarray:

```python
frame3.values

array([[2.4, 1.7],
       [2.9, 3.6],
       [nan, 1.5]])
```


Удаление нескольких строк с фильтром contains.
Для удаления строк можно воспользоваться методом .drop .
Довольно долго искал информацию как удалить строки с определённым условием, нашёл такой вариант:
```python
#В фильтре можно указывать несколько значений, разделять знаком “|”.
df_drop = df.loc[df["title"].str.contains("Фильтр" , case=False)]
prof = prof.drop(prof_drop.index)
```


Индексные объекты

В индексных объектах pandas хранятся метки вдоль осей и прочие метаданные (например имена осей)

Любой массив или иная последовательность меток, указанная при конструировании Series или DataFrame, преобразуется в объект index:

```python
obj = pd.Series(range(3), index=['a', 'b', 'c'])
index = obj.index
print(index)
print(index[:1])


Index(['a', 'b', 'c'], dtype='object')
Index(['a'], dtype='object')
```

Индексные объекты неизменяемы т.е. пользователь не может их модифицировать. Неизменяемость нужна для того что-бы несколько структур данных могли совместно использовать один и тот же индексный объект  не боясь его  повредить:

```python
labels = pd.Index(np.arange(3))
print(labels, '\n')
obj2 = pd.Series([1.5, -2.5, 0], index=labels)
print(obj2, '\n')
print(obj2.index == labels, '\n')
print(obj.index is labels)


Int64Index([0, 1, 2], dtype='int64') 

0    1.5
1   -2.5
2    0.0
dtype: float64 

[ True  True  True] 

False
```

Фундаментальные основы взаимодействия  с данными, хранящимися в объектах Series и DataFrame:
- Переиндексация
- Удаление элементов из оси
- Доступ по индексу, выборка и фильтрация
- Арифметические операции и выравнивание данных
- Операции между DataFrame и Series

Методы объектов pandas:
- Сортировка и ранжирование
- Индексы по осям с повторяющимися значениями
- Редукция и вычисление описательных статистик
- Уникальные значения, счётчики значений и членство



# Ссылки
* NumPy в Python - https://habr.com/ru/post/352678/
* Основные компоненты matplotlib - https://pyprog.pro/mpl/mpl_main_components.html
