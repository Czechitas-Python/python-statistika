---
title: Vzorky vína
demand: 2
---

### Vzorky vína

Pracuj se souborem [wine-regions.csv](wine-regions.csv). Každý záznam obsahuje informace o vzorku vína. Všechny vzorky pocházejí z jednoho regionu v Itálii, ale v datech se nachází několik různých odrůd. Naší úlohou bude data uspořádat do shluků, které by mohly odpovídat odrůdám. Na začátku uvažujme, že nevíme, ke které odrůdě jednotlivé záznamy patří, a nevíme ani to, kolik odrůd v datech je.

- Proveď redukci dimenzionality na 2 dimenze. Vykresli data pomocí grafu. Poznáš z grafu, kolik by byl nejlepší počet shluků? Následně zkus určit metriku *inertia* pro 2 až 10 shluků. Pro jaký počet shluků dochází k výraznému sníže tempa jejího poklesu? Následně pro tento počet vykresli graf včetně centroidů.
- Redokce dimenzionality není nutná. Zkus nyní znovu načíst data a vykresli graf metriky *inertia* pro 1 až 10 shluků na datech s původní dimenzí. Změnil se graf nějak výrazně? Změnilo by to tvé rozhodnutí o počtu shluků?
- Vypočítej průměrné hodnoty pro jednotlivé sloupce a pro jednotlivé shluky. Porovnej, jak se průměrné hodnoty mezi shluky liší.

#### Bonus

Další metrikou, kterou můžeme využít, je takzvaný _Silhouette_ koeficient. Ten měří průměrnou vzdálenost mezi bodem a všemi body, které jsou ve stejném shluku, a pak vzdálenost mezi bodem a všemi body v nejbližším jiném shluku.

Jeho hodnota se nachází mezi 1 a -1. Čím vyšší je tento koeficient, tím lépe definované jsou clustery (body se nachází blízko svému shluku a daleko od všech ostatních). Pokud má hodnotu kolem nuly, značí to, že se naše shluky překrývají (existují body na rozhraní dvou shluků). Záporná hodnota značí, že body z odlišných shluků jsou si blíže než body ze stejných shluků.

Hodnotu koeficientu zjisíš pomocí funkce `silhouette_score()`. Příklad volání funkce je níže.

```py
silhouette_score(X, labels)
```

Vytvoř graf hodnoty koeficientu pro 2 až 10 shluků. V jakém případě je hodnota koeficientu nejvyšší?

Řešení příkladu je [zde](reseni.ipynb).

:::solution

```python
# Cvičení

import matplotlib.cm as cm
import matplotlib.pyplot as plt
import numpy as np
import pandas
import requests
from sklearn.cluster import KMeans
from sklearn.manifold import TSNE
from sklearn.metrics import silhouette_samples, silhouette_score
from sklearn.preprocessing import StandardScaler

# Pracuj se souborem `wine-regions.csv`. Každý záznam obsahuje informace o vzorku vína.
# Všechny vzorky pocházejí z jednoho regionu v Itálii, ale v datech se nachází několik různých odrůd.
# Naší úlohou bude data uspořádat do shluků, které by mohly odpovídat odrůdám.

# Dejme tomu, že víme, že vzorky pocházejí ze tří odrůd, ale ztratili jsme informaci o tom,
# který vzorek je odkud. Máme tedy počet clusterů = 3.

# Nacteni dat
X = pandas.read_csv("wine-regions.csv")

# X.describe()
# X.isna().sum()

# Normalizace dat
scaler = StandardScaler()
X = scaler.fit_transform(X)
```

```python
distances = []
cluster_counts = range(1, 15)
 
for k in cluster_counts:
    model = KMeans(n_clusters=k, n_init="auto").fit(X)
    model.fit(X)
    distances.append(model.inertia_)

plt.plot(cluster_counts, distances,)
```

```python
tsne = TSNE(
    n_components=2,
    random_state=42,
)
X = tsne.fit_transform(X)
```

```python
plt.scatter(X[:, 0], X[:, 1], s=50)
```

```python
model = KMeans(n_clusters=3, random_state=42, n_init="auto")
labels = model.fit_predict(X)
```

```python
plt.scatter(X[:, 0], X[:, 1], c=labels, s=50, cmap="Set1")
centers = model.cluster_centers_
plt.scatter(centers[:, 0], centers[:, 1], c="black", s=200, alpha=0.5)
```

```python
X = pandas.read_csv("wine-regions.csv")
scaler = StandardScaler()
X = scaler.fit_transform(X)

tsne = TSNE(
    n_components=2,
    random_state=42,
)
X = tsne.fit_transform(X)

distances = []
cluster_counts = range(2, 15)
 
for k in cluster_counts:
    model = KMeans(n_clusters=k, n_init="auto")
    model.fit(X)
    distances.append(model.inertia_)

plt.plot(cluster_counts, distances,)
```

```python
scores = []
cluster_counts = range(2, 10)
 
for k in cluster_counts:
    model = KMeans(n_clusters=k, n_init="auto")
    labels = model.fit_predict(X)
    scores.append(silhouette_score(X, labels))

plt.plot(cluster_counts, scores,)
```

:::
