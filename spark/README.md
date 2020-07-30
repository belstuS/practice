# Spark Введение
## Шпаргалки
- [Pyspark API](https://s3.amazonaws.com/assets.datacamp.com/blog_assets/PySpark_Cheat_Sheet_Python.pdf)
- [Dataframe API](https://s3.amazonaws.com/assets.datacamp.com/blog_assets/PySpark_SQL_Cheat_Sheet_Python.pdf)

## Старт Pyspark
После загрузки вашей виртуальной среды выполните следующую команду:
```bash
pyspark
```

## RDDs
Гибкие распределенные наборы данных (RDD) можно рассматривать как списки элементов, которые
будет управляться путем применения функций к каждому элементу. Более полный
документация доступна
[here](http://spark.apache.org/docs/latest/rdd-programming-guide.html).

```python
filename = "trees2020.csv"
lines = spark.sparkContext.textFile(filename)
data = [i for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
```
`lines` может рассматриваться как список независимых строк, а` rdd` - как список целых чисел в диапазоне от 0 до 9.
### Методы действия
- **collect**() : Эта функция будет собирать RDD в список Python.
- **take**(k) : Эта функция создает RDD из элемента `k` в RDD.
- **count**() : Эта функция возвращает номер элемента в RDD.

### Методы трансформации
- **map**(func) : Вернуть новый распределенный набор данных, сформированный путем передачи каждого элемента источника через функцию func.
```python
lines = lines.map(lambda l: l.split(','))
rdd.map(lambda x: x*x)
#Output [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```
- **flatMap**(func) : Аналогично map, но каждый входной элемент может быть сопоставлен с 0 или более выходными элементами (поэтому func должен возвращать Seq, а не один элемент).
```python
rdd.flatMap(lambda x: [x*x, x*x*x] if x % 2 == 1 else [])
#Output [1, 1, 9, 27, 25, 125, 49, 343, 81, 729]
```

- **filter**(func) : Вернуть новый набор данных, сформированный путем выбора тех элементов источника, для которых func возвращает true.
```python
rdd.filter(lambda x: x % 3 == 1)
#Output [1, 4, 7]
```
- **reduceByKey**(func) : 	При вызове набора данных из пар (K, V) возвращает набор данных из пар (K, V), в которых значения для каждого ключа агрегируются с использованием заданной функции функции сокращения, которая должна иметь тип (V, V) => V.
```python
data = [(i%3, i) for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.reduceByKey(lambda x, y: x+y)
#Output [(0, 18), (1, 12), (2, 15)]
```
- **intersection**(dataset) : Вернуть новый RDD, который содержит пересечение элементов в наборе исходных данных и аргумента.
```python
data1 = [i for i in range(10)]
data2 = [i for i in range(5, 15)]
rdd1 = spark.sparkContext.parallelize(data1)
rdd2 = spark.sparkContext.parallelize(data2)
rdd1.intersection(rdd2)
#Output [5, 6, 7, 8, 9]
```

- **union**(funct): Вернуть новый набор данных, который содержит объединение элементов в исходном наборе данных и аргумент.
```python
data1 = [i for i in range(10)]
data2 = [i for i in range(5, 15)]
rdd1 = spark.sparkContext.parallelize(data1)
rdd2 = spark.sparkContext.parallelize(data2)
rdd1.union(rdd2)
#Output [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
```
- **distinct**() : Вернуть новый набор данных, который содержит различные элементы исходного набора данных.
```python
data = [i%3 for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.distinct()
#Output [0, 1, 2]
```

- **zipWithIndex**() : Присвойте индекс каждому элементу в СДР.
```python
data = [i for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.zipWithIndex()
#Output [(0, 0), (1, 1), (2, 2), (3, 3), (4, 4), (5, 5), (6, 6), (7, 7), (8, 8), (9, 9)]
```
- **groupByKey**() : При вызове набора данных из (K, V) пар возвращает набор данных из (K, Iterable <V>) пар.
```python
data = [(i%3, i) for i in range(10)]
rdd = spark.sparkContext.parallelize(data)
rdd.groupByKey()
```

## Dataframes
Структуры данных структурированы как таблицы в SQL или как электронные таблицы Excel.

| Tree | Park Name   | x  | y  |
|------|-------------|----|----|
| 1    | Canada Park | 2  | 3  |
| 2    | Otter Park  | 63 | 21 |
| 3    | Canada Park | 2  | 3  |

Чтобы загрузить файл CSV в кадр данных:
```python
from pyspark.sql import SparkSession
spark = SparkSession._create_shell_session()
df = spark.read.csv(filename, header=True, mode="DROPMALFORMED")
```
### Операторы

- **show**() : Отобразите первые строки dataframe.
- **select**(column name, another column name) : Вернуть фрейм данных только с выбранным столбцом.
```python
df = df.select("Park Name")
```
- **where**("action") : Удалите строки, которые не соответствуют действию.
```python
df = df.where("x = 2")
```
- **groupBy**(): Группируйте строки вместе, используя агрегатную функцию, такую как min, max или avg.
```python
df.groupBy("x").count()
df.groupBy("x").sum()
df.groupBy("x").mean()
```
- **orderBy**("column1", "column2", ...) : Получить фрейм данных с одним или несколькими столбцами.
```python
from pyspark.sql.functions import desc
df.orderBy(desc("Park Name"))
```
- **limit**(number) : Удалите все строки после номера строки.
```python
df.limit(7)
```
- **createDataFrame**(rdd) : Создайте кадр данных из RDD.
```python
from pyspark.sql import Row
parts = spark.sparkContext.textFile("frenepublicinjection2015.csv")
parts = parts.map(lambda x: x.split(","))
trees_rdd = parts.map(lambda p: Row(id=p[0], park_name=p[1], x=p[2], y=p[3]))
trees_df = spark.createDataFrame(trees_rdd)
```
- **join**() : Объедините фрейм данных на основе общего столбца.
```python
df = df1.join(df2, df2.park_name == df1.park_name)
```

- **toDF**(name1, name2, name3) : Переименуйте столбцы фрейма данных.
```python
df = df1.toDF("Otter", "Platypus", "Parrot", "Whale")
```

## Присваивание 1
### What to do
Цель этого задания - кодировать пустые функции в `answers / answer.py`.
После того, как вы написали функцию, вы можете запустить соответствующий тест следующим образом:
```
pytest tests/test_count.py 						#Run the test functions in test_count.py
pytest tests/test_count.py tests/test_park.py	#Run the test functions in test_count.py and test_park.py
pytest tests/test_count*						#Run the test functions in test_count.py, test_count_rdd.py and test_count_df.py
pytest tests									#Run all tests
```
### CSV
Формат CSV - это формат, в котором данные разделяются запятыми и новыми строками.
```
name,age,address
Martin,22,"40 Oakland street, Montréal"
Tristan,37,"1024 Ford street, Kuujjuaq"
Ali,26,"23 St-Catherine street, Québec"
```
### Dataset
Набор данных для назначения 1 представляет собой файл CSV, который содержит одну инъекцию на строку.
Дерево лечится один раз в год.

Поскольку заголовок и его документация на французском языке, вот перевод значения каждого столбца на английском языке:
- **Nom_arrond** :  Neighborhood name.
- **Invent** : Type of tree injected (H = out of street, R = in street)
- **No_Civiq** : Number of the building in front of the tree.
- **Rue** : Street name if the tree is in the street (see **Invent**).
- **Rue_De** : Street name of the street that delimit the area where the tree is planted.
- **Rue_a** : Street name of the street that delimit the area where the tree is planted. (Combined with **Rue_De**.)
- **Nom_parc** : Name of the park in which the tree is planted (see **Invent**).
- **Sigle** : The first two letter of the latin name of the tree.
- **Injections** : Year of the injection.
- **x** : The x coordinate based on the Mercator projection of Québec.
- **y** : The y coordinate based on the Mercator projection of Québec.
- **longitude** and **latitude**
