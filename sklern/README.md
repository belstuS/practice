# scikit-learn
Цель этой лаборатории - показать вам, что использование классификатора в Python просто.

## Install scikit-learn
Для установки scikit-learn вы можете использовать `pip`. Обратите внимание, что Numpy и SciPy являются зависимостями scikit-learn.
```
pip install -U numpy
pip install -U scipy
pip install -U scikit-learn
```
or `conda`:
```
conda install scikit-learn
```

## On orwell
Все пакеты установлены на Orwell, все, что вам нужно сделать, это загрузить модуль Python.
```
module load python/3.5.1
python
>>> import sklearn
```


## First Classification
Давайте настроим среду Python и посмотрим на данные:
```python
import numpy as np
from sklearn import datasets
iris = datasets.load_iris()
print(iris.feature_names)
print(iris.data)
print(iris.target)
print(iris.target_names)
```

Затем мы создаем тренировочный набор и тестовый набор:
```python
from sklearn.model_selection import train_test_split
X = iris.data
y = iris.target
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.4) # 60% training and 40% test
```

Классификатор инициируется, а затем обучается.
```python
from sklearn.ensemble import RandomForestClassifier
rf = RandomForestClassifier(n_estimators=10, random_state=123456)
rf = rf.fit(X_train, y_train)
```

Наконец мы тестируем нашу модель:
```python
from sklearn.metrics import accuracy_score
predicted = rf.predict(X_test)
accuracy = accuracy_score(y_test, predicted)
print(f'Mean accuracy score: {accuracy:.3}')
```

Используя вместо этого разделение KFold:
```python
from sklearn.model_selection import KFold
kf = KFold(n_splits=3)
for train, test in kf.split(X):
	print("%s %s" % (train, test))
```

## Second classification
Скачать  dataset [here](https://archive.ics.uci.edu/ml/machine-learning-databases/00240/UCI%20HAR%20Dataset.zip) then extract it.

Ваша цель - обучить модель на этом наборе данных.
- Загрузите комплекты (обучающие и тестирующие комплекты).
- Инициализируйте свой классификатор.
- Тренируй свой классификатор.
- Проверьте свой классификатор.
- Проверьте точность вашего классификатора.

Это решение:
```python
import numpy as np
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score
from io import StringIO 

X_train = np.loadtxt(StringIO(open("train/X_train.txt", "r").read()))
y_train = np.loadtxt(StringIO(open("train/y_train.txt", "r").read()))
X_test = np.loadtxt(StringIO(open("test/X_test.txt", "r").read()))
y_test = np.loadtxt(StringIO(open("test/y_test.txt", "r").read()))

rf = RandomForestClassifier(n_estimators=10, random_state=123456)
rf = rf.fit(X_train, y_train)

predicted = rf.predict(X_test)
accuracy = accuracy_score(y_test, predicted)
# accuracy -> 0.9107567017305734
```
## Источники
-  [tutorial](https://www.datacamp.com/community/tutorials/random-forests-classifier-python#building) с scikit-lean.
- Другой [tutorial](https://www.blopig.com/blog/2017/07/using-random-forests-in-python-with-scikit-learn/) с scikit-lean.
- [Scikit-Learn installation guide](https://scikit-learn.org/stable/install.html).
- [Scikit-Learn guide](https://scikit-learn.org/stable/tutorial/basic/tutorial.html#machine-learning-the-problem-setting) контролируемое обучение.
