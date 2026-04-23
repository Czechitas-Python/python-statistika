---
title: Kosatce
demand: 2
---

Vrať se k práci s daty o kosatcích v souboru [IRIS.csv](assets/IRIS.csv). Tentokrát zkus, jak dobře dokážou algoritmy pracovat se všemi třemi druhy kosatců. Využij algoritmy `KNeighborsClassifier` a `SVC` ke klasifikaci každého vzorku do jednoho ze tří druhů kosatce.

Postup je stejný jako v lekci:

- Rozděl data na vstupní a výstupní proměnné.
- Využij `GridSearchCV` k nalezení nejlepšího parametru pro `KNeighborsClassifier` (počet sousedů) a `SVC` (strategie)
- Dále vyzkoušej, jestli by nebylo zajímavé převést data do více dimenzí. Porovnej výsledek lineárního kernelu (`kernel="linear"`) s kernelem (`kernel="rbf"`). Můžeš vyzkoušet obě strategie, tj. budeš mít ve slovníku `params` dva klíče a každý z klíčů bude mít seznam dvou možných hodnot. Jaká dvojice parametrů má nejlepší hodnotu metriky `accuracy`?

Video s řešením je [zde](https://www.youtube.com/watch?v=H9VQIXm_nkY).

:::solution

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.svm import SVC
from sklearn.metrics import accuracy_score
from sklearn.neighbors import KNeighborsClassifier

# Načtení dat z CSV souboru
data = pd.read_csv("IRIS.csv")
# Rozdělení dat na vstupní (X) a cílové (y) proměnné
X = data.drop(columns=["species"])
y = data["species"]

# Rozdělení dat na trénovací a testovací sadu (30% dat je testovací sada)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

# Standardizace vstupních proměnných
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

# Iterace přes různé hodnoty počtu sousedů (k) pro K Nearest Neighbors
k = range(1, 30)
accuracy_list = []
for n in k:
    clf = KNeighborsClassifier(n_neighbors=n)
    clf.fit(X_train, y_train)
    y_pred = clf.predict(X_test)
    accuracy = accuracy_score(y_test, y_pred)
    accuracy_list.append([n, accuracy])

# Vytvoření dataframe s hodnotami k a jejich přesností
accuracy_df = pd.DataFrame(accuracy_list, columns=["n", "accuracy"])
accuracy_df.sort_values("accuracy", ascending=False)
```

```python
# Definování jader a strategií rozhodování pro Support Vector Classifier (SVC)
kernels = ["linear", "rbf"]
strategies = ["ovo", "ovr"]

results = []
for kernel in kernels:
    for strategy in strategies:
        clf = SVC(kernel=kernel, decision_function_shape=strategy, random_state=42)
        clf.fit(X_train, y_train)
        y_pred = clf.predict(X_test)
        accuracy = accuracy_score(y_test, y_pred)
        results.append([kernel, strategy, accuracy])

results_df = pd.DataFrame(results, columns=["Kernel", "Strategy", "Accuracy"])
results_df = results_df.sort_values("Accuracy", ascending=False)
results_df
```

:::
