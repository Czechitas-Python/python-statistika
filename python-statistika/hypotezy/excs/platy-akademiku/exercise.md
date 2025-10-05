---
title: Platy akademiků
demand: 2
---

Stáhni si soubor [salaries-2022.csv](assets/salaries-2022.csv), který obsahuje data o platech akademiků na univerzitě v belgickém Waterloo. Tvým úkolem je ověřit tvrzení, že s kariérním postupem vzroste akademickým pracovníkům a pracovnicím jejich průměrný plat. Konkrétně nám půjde o dvě pozice (sloupec `Position title`) - `Professor` a `Associate Professor` (obdoba české pozice docent(ka)). Protože profesor je vyšší pozice než docent, předpokládáme, že profesoři a profesorky by měi mít vyšší platy než docenti a docentky.

Zobraz si data, tentokrát s využitím histogramu. Tipni si, jestli data budou mít normální rozdělení. Především se zamysli nad tím, jestli jsou data symetrická. Poté ověř normalitu dat s využitím testu normality.

Na základě testu normality vyber vhodný ten pro ověření hypotézy o střední hodnotě. Formuluj nulovou a alternativní hypotézu, přičemž v alternativní hypotéze uvažuj jednostrannou variantu, že průměrný plat na pozici Professor je vyšší než na pozici Associate Professor. Do funkce pro vyhodnocení testu vlož parametr `alternative`. Nakonec na základě p-hodnoty rozhodni o zamítnutí nulové hypotézy.


```python
import pandas as pd  # Import knihovny pandas pro práci s daty

# Načtení CSV souboru s daty o platech do datového rámce
# Ty pravděpodobně podadresář statistika-1-assets nemáš, tak ho smaž
data = pd.read_csv("salaries-2022.csv", thousands=',')

# Přetvoření tabulky tak, aby se hodnoty "Salary paid ($)" seskupily pod jediný sloupec "variable"
data = data.melt("Position title", "Salary paid ($)")

# Vyfiltrování dat pro pozici "Professor" a uložení do proměnné data_prof
data_prof = data[data["Position title"] == "Professor"]["value"]

# Vyfiltrování dat pro pozici "Associate Professor" a uložení do proměnné data_asoc_prof
data_asoc_prof = data[data["Position title"] == "Associate Professor"]["value"]

```

:::solution

**Testové hypotézy:**

- **Nulová hypotéza (H0):** Průměrný plat profesorů a profesorek je stejný jako průměrný plat docentů a docentek.
- **Alternativní hypotéza (H1):** Průměrný plat profesorů a profesorek je větší než průměrný plat docentů a docentek.

**Řešení:**

```python
from scipy import stats
import seaborn as sns
import matplotlib.pyplot as plt

ax = sns.histplot(data=data, x='value', hue='Position title', bins=30)
ax.set(xlabel='Plat ($)', title='Porovnání platů profesorů a docentů')
plt.show()

# Test normality
stat_prof, p_prof = stats.shapiro(data_prof)
stat_asoc, p_asoc = stats.shapiro(data_asoc_prof)

print(f"Test normality - Professor: p-hodnota = {p_prof:.4f}")
print(f"Test normality - Associate Professor: p-hodnota = {p_asoc:.4f}")

# Obě skupiny nemají normální rozdělení (p < 0.05)
# Použijeme Mann-Whitney U test (neparametrický test)


statistic, p_value = stats.mannwhitneyu(data_prof, data_asoc_prof, alternative='greater')

print(f"\nP-hodnota Mann-Whitney U testu: {p_value:.6f}")
```

**Interpretace:**

Data nemají normální rozdělení (histogramy nejsou symetrické, p-hodnoty testů normality jsou < 0.05). Proto používáme Mann-Whitney U test, což je neparametrický test, který nevyžaduje normální rozdělení dat.

P-hodnota je prakticky 0, což je výrazně méně než 0.05. Proto **zamítáme nulovou hypotézu** a přijímáme alternativní hypotézu. Průměrný plat profesorů a profesorek (208 225 $) je statisticky významně vyšší než průměrný plat docentů a docentek (169 241 $).

:::
