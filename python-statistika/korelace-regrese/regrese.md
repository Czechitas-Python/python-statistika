## Regrese

Samotná informace o tom, že existuje statisticky významný vztah mezi množstvím srážek a velikostí úrody sice může být zajímavá, ale můžeme zjistit více. K tomu můžeme využít regresi. Regrese je nástroj, který umí vztah mezi dvěma proměnnými popsat. Abychom si pod slovem "popsat" dokázali něco představit, využijeme graf. Využijeme opět modul `seaborn`, tentokrát vygenerujeme graf pomocí funkce `regplot()`. U regrese vždy rozlušujeme mezi **závislou** (**vysvětlovanou**) a **nezávislou** (**vysvětlující**) proměnnou. Závislou proměnnou umísťujeme na svislou osu (*y*) a nezávislou vodorovnou osu (*x*). V našem případě je nezávislou proměnnou množství srážek a závislou velikost úrody. Tvrdíme totiž, že množství srážek ovlivňuje velikost úrody avokád.

::fig[]{src=assets/ada_05.png}

Musíme si uvědomit, že výslednou úrodu ovlivňují i další vlivy - například kvalita půdy, množství slunečního svitu, péče farmářů atd. Z toho důvodu neleží všechny body na regresní křivce, ale pohybují se kolem ní.

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
g = sns.regplot(data=data, x="rainfall", y="avocado_yield", line_kws={"color": "red"}, ci=None)
```

Pomocí této funkce dokážeme predikovat, kolik jaká bude úroda avokád podle množství srážek, a můžeme tedy předem plánovat dovoz nebo naopak plánovat vývoz.

::fig[]{src=assets/ada_10.png}

Tato funkce je označovaná jako "lineární" a k jejímu vykreslení potřebujeme znát dvě hodnoty. Tyto hodnoty často nemají praktický význam, jak uvidíme níže, pro sestavení funkce jsou ale důležité.

- První je hodnota, která určuje, kde leží průsečík regresní funkce se svislou osou (osou *y*). V našem případě jde o hodnotu, která udává množství avokád, která by se teoreticky urodila při nulových srážkách.
- Druhá je hodnota, která udává sklon funkce. Čím bude hodnota vyšší, tím více skloněná funkce bude. V našem případě toto číslo určí, o kolik se v průměru zvýší úroda avokád díky dalšímu milimetru srážek.

K zobrazení těchto hodnot můžeme použít modul *statmodels*. Ten zobrazí velkou tabulku se spoustou čísel, nás však budou zajímat pouze některá.

```python
res = smf.ols(formula="avocado_yield  ~ rainfall", data=data).fit()
res.summary()
```

Výsledkem je obří tabulka, ze které nás ale bude zajímat jenom část hodnot.

::fig[]{src=assets/regression_summary.png}

Podívejme se nejprve na dvě čísla (koeficienty - *coefficients*), která potřebujeme k nakreslení naší funkce:

- V řádku `intercept` máme hodnotu, která určuje, kde funkce protne se svislou osou. Pro náš případ to znamená, jakou úrodu by model odhadl pro teoretickou situaci, kdy by byla 0 srážek.
- V řádku `rainfall` máme hodnotu, která udává sklon funkce. V našem případě udává, o kolik be se v průměru zvýšila úroda, pokud by spadlo o 1 mm více srážek. Jinak řečeno vyšší číslo zde máme, tím více avokád nám "zařídí" 1 mm srážek navíc.

Pokud bychom chtěli odhadnout úrodu avokád na základě množství srážek, můžeme použít metodu `predict()`.

```python
new_data = pd.DataFrame({"rainfall": [500]})
predicted_yield = res.predict(new_data)
```

Metoda `predict` provede výpočet `500 * 0.2432 + 92.0281` a zobrazí nám výsledek.

### Vyhodnocení kvality modelu

Regresní model nevysvětlí data dokonale. Jak jsme si již řekli, na vysvětlovanou proměnnou působí další vlivy, které v datech nemáme. Regresní modely se mezi sebou liší podle toho, jak dobře vysvětlovanou proměnnou dokážou vysvětlit. Abychom tuto skutečnost dokázali vysvětlit, vznikl ukazatel označovaný jako koeficient determinace (*coefficient of determination*, *R-squared*). Ten říká, kolik procent variability (různorodosti) vysvětlované proměnné (v našem případě známky z testu) dokážeme pomocí našeho modelu vysvětlit. Koeficient determinace je číslo mezi 0 a 1 a platí, že čím vyšší koeficient determinace je, tím lépe náš model naše data popisuje.

Model se v matematických vzorcích často značí $R^2$, v naší tabulce je označen jako `R-squared`.

Chování regrese si můžeš vyzkoušet například [v této aplikaci](https://observablehq.com/@yizhe-ang/interactive-visualization-of-linear-regression).

### Přidání dalších proměnných

Zkusme nyní do modelu přidat další proměnné. Proměnné přidáme napravo od symbolu `~`.

Do regresního modelu můžeme přidat i tzv. kategoriální (`str`) proměnné. Ty musíme označit pomocí funkce `C()`.

```python
data = pd.read_csv("avocado_farming_data.csv")
res = smf.ols(formula="avocado_yield  ~ rainfall + sunlight + soil_moisture + pests + C(regions)", data=data).fit()
res.summary()
```

U množství škůdců (sloupec `pests`) vidíme zápornýkoeficient. To znamená, že čím vyšší hodnota je v tomto sloupci, tím nižší je výsledná úroda.

#### Jak statsmodels v modelu zachází s proměnnou regions

Protože `regions` je textové označení oblasti, jedná se o tzv. *kategoriální* proměnnou. `statsmodels` takovou proměnnou nemůže použít přímo. Funkce `C(regions)` proto spustí tzv. *one‑hot encoding* – pro každou oblast založí samostatný binární sloupec (tj. sloupec, který obsahuje pouze hodnoty 0 a 1). Máme tři regiony, vzniknou tedy proměnné `C(regions)[T.South]` a `C(regions)[T.West]`. Jeden z regionů (konkrétně `North`) vynechá. Model každému regionu přiřadí pomocí kterého odhaduje, o kolik je v něm úroda větší v porovnání s vynechaným regionem, tj. regionem `North`. Protože jsou oba koeficienty kladné, znamená to, že v obou z nich je úrada větší než v regionu `North`. 

Díky one‑hot encodingu tedy dokážeme do lineární regrese začlenit i textové údaje o původu sklizně.

::fig[]{src=assets/ada_11.png}

### Cvičení

::exc[excs/kvalita-betonu]

#### Bonusy

::exc[excs/pojistovna]
