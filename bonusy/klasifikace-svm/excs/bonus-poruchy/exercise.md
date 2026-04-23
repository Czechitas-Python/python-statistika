---
title: Bonus – Poruchy
demand: 3
---

Stáhni si data o poruchách ze souboru [predictive_maintenance.csv](assets/predictive_maintenance.csv). Význam sloupců je následující:

- UID: jedinečný identifikátor v rozsahu 1 až 10000,
- ID produktu: skládá se z písmene L, M nebo H pro nízkou (50 % všech výrobků), střední (30 %) a vysokou (20 %) variantu kvality výrobku a sériového čísla specifického pro danou variantu,
- teplota vzduchu (K),
- teplota procesu (K),
- otáčky (ot/min),
- točivý moment (Nm),
- opotřebení nástroje (min),
- označení "selhání stroje" (pokud k němu došlo).

Tvým úkolem je vytvořit model, který bude predikovat poruchu stroje.

Proveď následující postup:

- Vyřaď z datasetu sloupce (jsou dva), které nemají pro analýzu význam.
- Podívej se, kolik typů poruch bylo objeveno.
- Rozděl data na vstupní proměnné a výstupní proměnnou.
- Vyzkoušej algoritmy Support Vector Machine a K Nearest Neighbors ke klasifikaci poruchy stroje. Porovnej, který algoritmus dosáhl lepších výsledků. Pozor na to, že dat je nyní opravdu hodně, takže program může běžet trochu déle. Můžeš ho zrychlit tím, že pro K Nearest Neighbors budeš uvažovat pouze čtveřici možných počtů sousedů (např. 3, 5, 11, 15).

:::solution

```python
data = pd.read_csv("predictive_maintenance.csv")
data.head()
```

```python
data.groupby("Failure Type").size()
```

```python
X = data.drop(columns=["Failure Type"])
y = data["Failure Type"]
X_type = pd.get_dummies(X["Type"])
X = pd.merge(X, X_type, left_index=True, right_index=True)
X = X.drop(columns=["Type", "UDI", "Product ID"])
X
```

:::
