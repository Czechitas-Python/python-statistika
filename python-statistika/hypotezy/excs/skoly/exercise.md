---
title: Školy
demand: 2
---

Nyní se Ada rozhodla porovnat [výsledek testů studentů dvou škol](assets/skoly.csv). Adu opět zajímají průměrné výsledky, tentokrát se ale v alternativní hypotéze nepřikláníme k tomu, že by jeden z výběrů měl větší hodnotu než druhý. Jde nám čistě o to, jestli je mezi školami rozdíl.

1. Definuj testové hypotézy. Pozor na použití správného výrazu (slova) v alternativní hypotéze.
1. Proveď test hypotézy a vyhodnoť výsledky.

Pozor na interpretaci. Na základě výsledků můžeš pouze říct, že výsledky jsou různé, nikoli že jedna škola je lepší než druhá (protože jsi použila oboustranný test).

:::solution

**1. Testové hypotézy:**

- **Nulová hypotéza (H0):** Průměrný výsledek testů studentů školy A je stejný jako průměrný výsledek testů studentů školy B.
- **Alternativní hypotéza (H1):** Průměrný výsledek testů studentů školy A je různý od průměrného výsledku testů studentů školy B.

**2. Test hypotézy:**

```python
import pandas as pd
from scipy import stats
import seaborn as sns
import matplotlib.pyplot as plt

data = pd.read_csv("skoly.csv")

statistic, p_value = stats.ttest_ind(data['School A'], data['School B'], equal_var=False)

print(f"P-hodnota: {p_value:.6f}")
```

**Interpretace:**

P-hodnota je přibližně 0.0045, což je méně než hladina významnosti 0.05. Proto **zamítáme nulovou hypotézu** a přijímáme alternativní hypotézu. Průměrné výsledky testů studentů mezi školami A a B jsou statisticky významně **různé**. Na základě oboustranného testu však nemůžeme říct, která škola má lepší výsledky - pouze že se výsledky liší.

:::
