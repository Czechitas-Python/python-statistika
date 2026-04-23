---
title: Doporučování filmů
demand: 2
---

Náš systém se orientoval čistě podle popisu děje filmu. Pokud nemáme k dispozici nic jiného, než slovní popis, je často nutné pracovat s ním. V našem datasetu ale máme ale další strukturovaná data, například žánr filmu. Ten může být užitečný. Vraťme se třeba k filmu The Fugitive. Uživatel nebo uživatelka, kterému (které) se tento film líbil, může preferovat filmy s podobnými žánry. Hypotetická romantická komedie o uprchlém vězni, který se na útěku zamiluje, může být méně lákavá než jiný akční thriller s detektivní zápletkou.

Žánry filmu máme v datech uvedeny ve sloupečcích, které začínaji `gen_`. 

Načti si nejprve znovu data a ponech si v nich pouze sloupce se žánry pomocí kódu níže.
data = pd.read_csv("tmdb_5000_movies.csv")
# Nastavíme index
data = data.set_index("title")
# Ponecháme v datech pouze sloupce od gen_Action po gen_Western
data_genres = data.loc[:, "gen_Action":"gen_Western"]
data_genres.head()
Podívej se na žánry filmu The Fugitive s využitím `.loc`.

Žárny filmů vyzkoušíme porovnat nikoli pomocí cosinové vzdálenosti, ale vyzkoušíme Jaccardovovo skóre. Jaccardovo skóre je založeno na následujícím vzorečku: (počet žánrů, které mají filmy společné / počet žánrů, které se vyskytují alespoň u jednoho filmu).

Uvažuj třeba hypotetický film A, který má žánr Comedy a Romance, a film B, který má žárn Comedy a History. Oba filmy mají společný jeden žánr (Comedy). Celkem se u obou filmů objevují žárny Comedy, Romance a History, tj. tři žánry. Jaccardovo skóre pro tyto dva filmy je 1/3.

Proveď import funkce `jaccard_score` pomocí příkazu níže.
from sklearn.metrics import jaccard_score
Nyní zkus spočítat Jaccardovo skóre pro filmy The Fugitive a Witness. Uděláš to tak, že funkci `jaccard_score` dáš dva parametry - sérii s žánry filmu The Fugitive a sérii s žánry filmu Witness. Sérii s žánry The Fugitive už jsi získala v předchozím kroku a sérii s žánry Witness získáš stejným způsobem.

Měla by ti vyjít hodnota přibližně 0.2857.

Dále zkus to samé pro film The Fugitive a Titanic. Mělo by ti vyjít přibližně 0.142856.

Nyní zkus vypočítat Jaccardovy vzdálenosti pro všechny filmy. Proveď následující importy:
from scipy.spatial.distance import pdist, squareform
Nejprve použij funkci `pdist`, která vypočítá Jaccardovy vzdálenosti pro všechny dvojice filmů. Funkci dej jako první parametr matici `data` a jako druhý parametr `metric="jaccard"`. Výsledek ulož do proměnné `jaccard_distances`. Výsledkem je jednorozměrné pole.

Jako druhý krok použij funkci `squareform`, která převede jednorozměrné pole na dvourozměrnou matici. Funkci volej pouze s jedním parametrem - proměnnou `jaccard_distances`. Výsledek opět ulož do proměnné s názvem `jaccard_distances`. Nakonec je je potřeba spustit tento řádek

```py
jaccard_distances = 1 - jaccard_distances
```

Funkce totiž počítá vzdálenosti, nikoli Jaccardovo skóre. Pro Jaccardovo skóre a Jaccardovu vzdálenost platí vztah: Jaccardovo skóre = 1 - Jaccardova vzdálenost.

Výsledek poté převeď na pandas tabulku stejným způsobem, jako jsme to dělali v lekci. Jako index (parametr `index`) a názvy sloupců (parametr `columns`) použij indexy z původní tabulky data (tj. `data.index`).

Poté vyber sloupec s filmem The Fugitive a seřaď hodnoty v něm od největší po nejmenší. Filmy nahoře jsou nyní nejvíce podobné a algortimus postavený na žánru filmu by je doporučil.

### Bonus

Podívej se na kód níže. Obsahuje metody a funkce, které jsme si ukazovali dříve, především v prvních třech lekcích. Dokážeš si vzpomenout, co tyto funkce dělají, a odhadnout výsledek? Pokud ne, podívej se na výstup níže.
data['release_date'] = pd.to_datetime(data['release_date'])
data['release_year'] = data['release_date'].dt.year

bins = [1979, 1989, 1999, 2009, 2019, 2029]
labels = ['1980s', '1990s', '2000s', '2010s', '2020s']

data['decade'] = pd.cut(data['release_year'], bins=bins, labels=labels)
Níže je nápověda, která zobrazuje nově přidaný sloupec `decade`.
data[['release_date', 'release_year', 'decade']].head()
Níže jsou komentáře k jednotlivým řádkům
# Převod sloupce 'release_date' na datový typ datetime
data['release_date'] = pd.to_datetime(data['release_date'])

# Extrahování roku z 'release_date' a uložení do nového sloupce 'release_year'
data['release_year'] = data['release_date'].dt.year

# Definice rozmezí pro dekády
bins = [1979, 1989, 1999, 2009, 2019, 2029]

# Definice popisek pro jednotlivé dekády
labels = ['1980s', '1990s', '2000s', '2010s', '2020s']

# Kategorizace roku vydání filmu do dekád a přidání do nového sloupce 'decade'
data['decade'] = pd.cut(data['release_year'], bins=bins, labels=labels)
Dekádu jsme zjišťovali, protože každá dekáda měla z pohledu kinematografie jistá specifika a například v devadesátých letech se natáčely filmy jinak, než jak se natáčí dnes. A pokud se někomu líbí spíše starší filmy, pravděpodobně mu bude lepší doporučit nějaký starší film. Upravíme tedy náš systém tak, aby doporučoval pouze filmy ze stjené dekády.

Nejprve do tabulky `jaccard_distances` vlož sloupec `decade` z tabulky `data`. Pokud si nejsi jistý(á), jak to provést, použij řádek níže.

```py
jaccard_distances["decade"] = data["decade"]
```

Dále se vraťme k filmu The Fugitive. Z lekce víc, z jaké dekády tento film pochází, ale můžeš to zkusit zjistit pomocí `.loc`. Dále vytvoř tabulku `movies_from_decade`, která bude obsahovat pouze filmy ze stejné dekády jako The Fugitive. Nakonec z této tabulky vyber filmy, které jsou svými žánry nejvíce podobné filme The Fugitive. K tomu můžeš použít stejný kód jaký na závěru předchozího příkladu, pouze jej použij s tabulkou `movies_from_decade`.

:::solution

```python
import pandas as pd
from sklearn.metrics import jaccard_score
from scipy.spatial.distance import pdist, squareform

data = pd.read_csv("tmdb_5000_movies.csv")
data = data.set_index("title")
data.head()
```

```python
data_genres = data.loc[:, "gen_Action":"gen_Western"]
data_genres.head()
```

```python
data_genres.loc["The Fugitive"]
```

```python
data.loc["Witness"]
```

```python
data_genres.loc["Titanic"]
```

```python
jaccard_score(data_genres.loc["The Fugitive"], data_genres.loc["Witness"])
```

```python
jaccard_score(data_genres.loc["The Fugitive"], data_genres.loc["Titanic"])
```

```python
from scipy.spatial.distance import pdist, squareform

jaccard_distances = pdist(data_genres, metric="jaccard")
jaccard_distances = squareform(jaccard_distances)
jaccard_distances = 1 - jaccard_distances
```

```python
jaccard_distances = pd.DataFrame(jaccard_distances, index=data.index, columns=data.index)
jaccard_distances
```

```python
jaccard_distances["The Fugitive"].sort_values(ascending=False).head(20)
```

```python
import pandas as pd

data['release_date'] = pd.to_datetime(data['release_date'])
data['release_year'] = data['release_date'].dt.year

bins = [1979, 1989, 1999, 2009, 2019, 2029]
labels = ['1980s', '1990s', '2000s', '2010s', '2020s']

# Categorize the release years into decades
data['decade'] = pd.cut(data['release_year'], bins=bins, labels=labels)

# Display the DataFrame to verify the changes
data[['release_date', 'release_year', 'decade']].head(30)

```

```python
jaccard_distances["decade"] = data["decade"]
```

```python
decade = jaccard_distances.loc["The Fugitive", "decade"]
movies_from_decade = jaccard_distances[jaccard_distances["decade"] == decade]
movies_from_decade["The Fugitive"].sort_values(ascending=False).head(20)
```

:::
