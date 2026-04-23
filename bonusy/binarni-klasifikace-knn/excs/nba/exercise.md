---
title: NBA
demand: 2
---

Stáhni si dataset [nba_logreg.csv](assets/nba_logreg.csv), který obsahuje informace o začínajících hráčích americké basketbalové ligy NBA. Jako vstupní proměnnou pro předpověď použij sloupec `TARGET_5Yrs`, který o daném hráči říká, zda byl aktivní v lize i po pěti letech.

- Načti si data do proměnných `X` a `y`
- Rozděl data na trénovací a testovací (velikost testovacích dat nastav na 30% a nezapomeň nastavit proměnnou `random_state`, aby tvoje výsledky byly reprodukovatelné)
- Pokud použijeme stejný algoritmus jako v prvním úkolu, tj. `KNeighborsClassifier`.
- Vytvoř si matici záměn a na základě poskytnutých čísel zkus vypočítat metriky _Precision_ a _Recall_. Následně si výsledky svého výpočtu ověř s využitím funkcí modulu `scikit-learn`.
- Uvažuj, že jsi v roli pracovníka agentury, která pomáhá nadějným začínajícím hráčům. Pro tebe jako pro pracovníka agentury je velmi důležité nepropásnout nějaký nový talent. Pokud nabídneš spolupráci někomu, kdo pak neuspěje, tolik to nevadí. Zkus najít hodnotu parametru `n_neighbors`, která maximalizuje ideání metriku pro pracovníka agentury. K nalezení optimální metriky použij graf.

Pokud by tě zajímal význam jednotlivých sloupců, pak je níže:

- GP (Games Played): odehrané hry,
- MIN (MinutesPlayed): odehrané minuty,
- PTS (PointsPerGame): body za hru,
- FGM (FieldGoalsMade): úspšné střelecké pokusy z pole,
- FGA (FieldGoalsAttempts): střelba z pole,
- FG% (FieldGoalsPercent): úspěšnost střelby z pole,
- 3P Made (3PointsMade): úspšné střelecké pokusy z tříbodového území,
- 3PA (3PointsAttempts): střelba z tříbodového území,
- 3G% (3PointPercent): úspěšnost střelby z tříbodového území,
- FTM (FreeGoalsMade): vhozené pokutové koše,
- FTA (FreeGoalsAttempts): trestné hody,
- FG% (FreeGoalsPercent): úspěšnost trestných hodů,
- OREB (OffensiveRebounds): útočné doskoky,
- DREB (DefensiveRebounds): obranné doskoky,
- REB (Rebounds): odrazy,
- AST (Assists): asistence,
- STL (Steals): zisky míče,
- TOV (Turnovers): ztráta.

Zdroje překladů: [Anglicko-český basketballový slovník](https://cbf.cz.basketball/upload/docs/391MGY.pdf)

:::solution

```python
import pandas
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsClassifier
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import (
    ConfusionMatrixDisplay,
    precision_score,
    recall_score,
    f1_score,
    accuracy_score,
)
```

```python
data = pandas.read_csv("nba_logreg.csv")
data = data.drop(columns="Name")
data.head()
```

- Načti si data do proměnných `X` a `y`


```python
X = data.drop(columns=["TARGET_5Yrs"])
y = data["TARGET_5Yrs"]
```

- Rozděl data na trénovací a testovací (velikost testovacích dat nastav na 30% a nezapomeň nastavit proměnnou `random_state`, aby tvoje výsledky byly reprodukovatelné)


```python
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.3, random_state=42
)
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)
```

- Pokud použijeme stejný algoritmus jako v prvním úkolu, tj. `KNeighborsClassifier`.


```python
clf = KNeighborsClassifier()
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)
```

- Vytvoř si matici záměn a na základě poskytnutých čísel zkus vypočítat metriky _Precision_ a _Recall_. Následně si výsledky svého výpočtu ověř s využitím funkcí modulu `scikit-learn`.


```python
ConfusionMatrixDisplay.from_estimator(
    clf,
    X_test,
    y_test,
    display_labels=clf.classes_,
)
```

```python
print(190 / (190 + 84))
print(precision_score(y_test, y_pred))

print(190 / (190 + 56))
print(recall_score(y_test, y_pred))
```

- Uvažuj, že jsi v roli pracovníka agentury, která pomáhá nadějným začínajícím hráčům. Pro tebe jako pro pracovníka agentury je velmi důležité nepropásnout nějaký nový talent. Pokud nabídneš spolupráci někomu, kdo pak neuspěje, tolik to nevadí. Zkus najít hodnotu parametru `n_neighbors`, která maximalizuje ideání metriku pro pracovníka agentury. K nalezení optimální metriky použij graf.

```python
ks = range(1, 31, 2)
recall_scores = []

for k in ks:
    clf = KNeighborsClassifier(n_neighbors=k)
    clf.fit(X_train, y_train)
    y_pred = clf.predict(X_test)
    recall_scores.append(recall_score(y_test, y_pred))
plt.plot(ks, recall_scores)
```

:::
