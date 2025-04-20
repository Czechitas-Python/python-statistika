## Korelace

Královna Ada tentokrát řeší další otázku. V jejím království existuje velké množství avokádových plantáží.

::fig[]{src=assets/ada_01.png}

V různých letech a na různých místech její země roste každý rok jiné množství avokád. Ada si myslí, že velikost úrody souvisí s množstvím srážek, a tak zadá za úkol svým poradcům sesbírat data o **množství srážek** a **velikosti úrody** za posledních několik let v různých místech jejího království. V datech máme i další sloupečky, ale na ty se podíváme později.

::fig[]{src=assets/ada_02.png}

Pojďme se na data podívat. Soubor s daty najdeš [zde](assets/avocado_farming_data.csv).

```python
import pandas as pd
import seaborn as sns
from scipy import stats
import numpy as np
import statsmodels.api as sm
import statsmodels.formula.api as smf
import statsmodels.tools as tools
import matplotlib.pyplot as plt


data = pd.read_csv("avocado_farming_data.csv")
data = data[["rainfall", "avocado_yield"]]
```

K zobrazení použijeme :term{cs="bodový graf" en="scatter plot"}. Na vodorovné ose máme množství srážek a na svislé ose velikost úrody v každém čase a místě. Vidíme, že úroda má tendenci růst s tím, jak rostou srážky. Současně je patrný možný vliv dalších faktorů a také náhody. Takové závislosti se říká stochastická závislost (*stochastic dependence*). Dále platí, že závislost je lineární (*linear*), tj. kdybychom ho chtěly popsat pomocí matematické funkce, mohli bychom použít přímku.

Pozn.: Pozor na to, že v řadě případů je závislost lineární pouze v nějakém rozsahu. Např. pří extrémně vysokých srážkách by úroda mohla začít i klesat, protože kvůli extrémním dešťům může například úroda plesnivět. My ale takové situace v datech nemáme.

```python
sns.scatterplot(data=data, x="rainfall", y="avocado_yield")
```

![png](statistika-2_files/statistika-2_3_1.png)
    
Takové závislosti říkáme :term{cs="korelace" en="correlation"} a to, jak je závislost silná, můžeme popsat pomocí :term{cs="korelačního koeficientu" en="correlation coefficient"}. Pro jeho hodnoty platí následující:

- Hodnoty blízko +1 znamenají silnou **přímou** lineární závislost, tj. hodnoty v obou sloupcích rostou současně.
- Hodnoty blízko 0 znamenají lineární **nezávislost**.
- Hodnoty blízko -1 znamenají silnou **nepřímou** lineární závislost, tj. jedna hodnota roste a současně druhá klesá.

Přímá nezávislost by mohla být např. mezi množstvím extrémně horkých dnů a úrodou. Čím více dní v roce je extrémní horko, tím menší bude úroda. To samé může platit např. pro množství mrazivých nocí pozdě na jaře atd.

::fig[]{src=assets/ada_03.png}

Hodnotu korelace zjistíme pomocí metody `corr()` pro zvolenou tabulku.

```python
data.corr()
```

Korelace automaticky neznamená, že obě veličiny se vzájemně ovlivňují. Uvažujme například úrodu avokád a úrodu melounů v blízké oblasti. Oba druhy ovoce vyžadují dostečné srážky, takže úroda obou druhů ovoce bude někdy vysoká (pokud jsou dostatečné srážky) a současně nízká (pokud nejsou dostatečné srážky). To ale neznamená, že se ovlivňují vzájemně. Ve skutečnosti to způsobuje společný faktor (srážky).

::fig[]{src=assets/ada_04.png}

V případě korelace se nezabýváme tím, která proměnná ovlivňuje kterou proměnnou. To řeší až regrese.

### Statistický test významnosti korelace

Korelaci můžeme testovat i statisticky. Využít můžeme test založený na Personově korelačním koeficientu (pokud data mají normální rozdělení) nebo test založení na Kenallově Tau (pokud data nemají normlní rozdělení).

Hypotéy testu jsou:

* H0: Mezi dvěma proměnnými neexistuje korelace (tj. koeficient korelace pro *kompletní* dataset je 0).
* H1: Mezi dvěma proměnnými existuje korelace (tj. koeficient korelace pro *kompletní* dataset není 0).

Testování můžeme provádět např. při využití hladiny významnosti 0.05.

Níže je vyýpočet používající Pearsonův koeficient. Samotný Pearsonův korelační koeficient funguje pro jakákoli číselná data, test statistické významnosti korelace už ale předpokládá normalitu.

```python
stat, pval = stats.pearsonr(data["rainfall"], data["avocado_yield"])
pval
```

Pro Kendallovo Tau předpoklad normality dat nepotřebujeme.

```python
stat, pval = stats.kendalltau(data["rainfall"], data["avocado_yield"])
```
