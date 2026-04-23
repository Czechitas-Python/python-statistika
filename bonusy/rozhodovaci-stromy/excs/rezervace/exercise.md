---
title: Rezervace hotelů
demand: 2
---

Vyzkoušej rozhodovací strom na datové sadě o rezervací hotelů ze serveru [Booking.com](https://booking.com) ([hotel_bookings.csv](hotel_bookings.csv)), kterou jsme používali na začátku kurzu. 

Pokud pracuješ s rozhodovacím stromem, neprováděj u číselných hodnot standardizaci. Není totiž nutná, rozhodovací strom uvažuje vždy v každém rozhodovacím uzlu pouze jednou proměnnou, tím pádem ho nikdy nemůže splést 

- Podívej se, kolik je v datech zrušených rezervací a kolik nezrušených.
- V prvním kroku zkus sestavit pouze na základě proměnné `lead_time`, u které bychom (na základě grafů z první lekce) se mohli domnívat, že bude mít vliv na to, jestli bude rezervace zrušená. Omez strop na `max_depth=2`. Nech si graficky zobrazit strom a na základě obrázku odhadni, jestli bude zrušená rezervace udělaná 5, 20, 100 a 300 dní.
- Vytvoř si pro tento strom matici záměn a urči hodnotu metriky `accuracy`.
- Na rozdíl od například datasetu o kostatcích není tento dataset perfektně vyvážený. V realitě se často setkáváme s tzv. nevyváženými datasety (*imbalanced dataset*), kde je dat z jedné skupiny výrazně víc než dat z druhé. Příkladem jsou například podvodné transakce (podvodných transakcí je mnohem méně než těch běžných), kybernetické útoky (útoků je mnohem méně než běžného provozu v síti) atd. V takových případech nemusí být metrika `accuracy` úplně vhodná. Vyzkoušej proto metriku `balanced_accuracy_score`. Níže je popsaný import.

```py
from sklearn.metrics import balanced_accuracy_score
```

- Problém s nevyváženými datasety je, že modelu stačí správně zařadit dostatečné množství hodnot z početnější skupiny. Protože dat v menší skupině je celkově méně, malá úspěšnost v jejich správné identifikaci sníží metriku `accuracy` jen málo. Naopak `balanced_accuracy_score` uvažuje množství dat v jednotlivých skupinách. Méně početné skupiny mají větší váhu, model je tím pádem nucen soustředit se i na ně. Zkus zjistit hodnotu metriky `balanced_accuracy_score`. Funkce se používá stejně jako `accuracy`.

:::solution

### Rezervace

```python
import numpy as np
import pandas as pd
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import ConfusionMatrixDisplay, accuracy_score, precision_score, recall_score, balanced_accuracy_score
from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.preprocessing import OneHotEncoder
from sklearn.tree import export_graphviz
from six import StringIO
from IPython.display import Image  
import pydotplus
from pydotplus import graph_from_dot_data

import os
os.environ["PATH"] += os.pathsep + r'C:\Program Files\Graphviz\bin'

data = pd.read_csv("hotel_bookings.csv")
data.head()

data = data.dropna(subset=["children"])
```

```python
data["is_canceled"].value_counts()
```

```python
X = data[["lead_time"]]
y = data["is_canceled"]

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)
```

```python
clf = DecisionTreeClassifier(max_depth=2)
clf = clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)
```

```python
dot_data = StringIO()
export_graphviz(clf, out_file=dot_data, filled=True, class_names=["Not canceled", "Canceled"])
graph = pydotplus.graph_from_dot_data(dot_data.getvalue())
Image(graph.create_png())
```

```python
ConfusionMatrixDisplay.from_estimator(clf, X_test, y_test)
```

```python
accuracy_score(y_test, y_pred)
```

```python
balanced_accuracy_score(y_test, y_pred)
```

```python
categorical_columns = ["country", "market_segment", "distribution_channel", "arrival_date_month", "meal"]
numeric_columns = ["lead_time", "stays_in_weekend_nights", "stays_in_week_nights", "adults", "children", "babies", "is_repeated_guest", "previous_cancellations", "previous_bookings_not_canceled"]
```

```python
data[numeric_columns].head()
```

```python
others = data[numeric_columns].to_numpy()
ohe = OneHotEncoder()
encoded_columns = ohe.fit_transform(data[categorical_columns])
encoded_columns = encoded_columns.toarray()
X = np.concatenate([encoded_columns, others], axis=1)
```

```python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)
```

```python
model = DecisionTreeClassifier()
params = {"min_samples_split": [10, 20, 30, 40]}

clf = GridSearchCV(model, params, scoring="accuracy")
clf.fit(X, y)

print(clf.best_params_)
print(clf.best_score_)
```

```python
model = DecisionTreeClassifier()
params = {"min_samples_split": [10, 20, 30, 40]}

clf = GridSearchCV(model, params, scoring="accuracy")
clf.fit(X, y)

print(clf.best_params_)
print(clf.best_score_)
```

:::
