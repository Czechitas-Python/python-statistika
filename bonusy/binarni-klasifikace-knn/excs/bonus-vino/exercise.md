---
title: Bonus – Víno
demand: 3
---

Vrať se k experimentu z lekce, který se zabýval kvalitou vína. Tentokrát vyber hodnotu parametru `n_neighbors` na základě metriky `precision`.

Nejprve se zamysli nad tím, co by nás k tomu vedlo. Která z těchto možností je správná?

1. Je lepší na základě modelu nekoupit dobré víno, než koupit špatné víno a to pak vyhodit.
1. Je lepší koupit omylem špatné víno a to pak vyhodit, než promeškat nákup dobrého víno.

Na základě grafu vyber tu hodnotu parametru, která dává nejlepší výsledek (nejvyšší hodnotu při volání `precision_score()`). Liší se tvůj zvolený parametr od parametru, který jsme jako závěrečný zvolili v lekci? 


Dále vytvoř graf, který bude pro několik parametrů `n_neighbors` obsahovat všechny čtyři výsledné metriky, které jsme si v kurzu ukázali: `accuracy_score`, `precision_score`, `recall_score`, `f1_score`. 

:::solution

```python
data = pandas.read_csv("wine.csv")
X = data.drop(columns=["quality"])
y = data["quality"]
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.3, random_state=42
)
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

clf = KNeighborsClassifier()
clf.fit(X_train, y_train)
```

```python
# Je lepší nekoupit dobré víno, než plýtvat a vyhazovat špatné.

# Proto budeme vybírat na základě metriky PRECISION místo ACCURACY.

# Tato metrika penalizuje označení špatného vína za dobré.
# Kdežto Accuracy určuje poměr správně určených záznamů oproti celku.
```

Na základě grafu vyber tu hodnotu parametru, která dává nejlepší výsledek (nejvyšší hodnotu při volání `precision_score()`). Liší se tvůj zvolený parametr od parametru, který jsme jako závěrečný zvolili v lekci? 


```python
ks = range(1, 31, 2)
precision_scores = []

for k in ks:
    clf = KNeighborsClassifier(n_neighbors=k)
    clf.fit(X_train, y_train)
    y_pred = clf.predict(X_test)
    precision_scores.append(precision_score(y_test, y_pred, pos_label="good"))
plt.plot(ks, precision_scores)
```

Dále vytvoř graf, který bude pro několik parametrů `n_neighbors` obsahovat všechny čtyři výsledné metriky, které jsme si v kurzu ukázali: `accuracy_score`, `precision_score`, `recall_score`, `f1_score`. 

```python
accuracy_scores = []
recall_scores = []
f1_scores = []

for k in ks:
    clf = KNeighborsClassifier(n_neighbors=k)
    clf.fit(X_train, y_train)
    y_pred = clf.predict(X_test)
    accuracy_scores.append(accuracy_score(y_test, y_pred))
    recall_scores.append(recall_score(y_test, y_pred, pos_label="good"))
    f1_scores.append(f1_score(y_test, y_pred, pos_label="good"))
plt.plot(ks, recall_scores, label="recall")
plt.plot(ks, precision_scores, label="precision")
plt.plot(ks, accuracy_scores, label="accuracy")
plt.plot(ks, f1_scores, label="f1")
plt.legend()
```

:::
