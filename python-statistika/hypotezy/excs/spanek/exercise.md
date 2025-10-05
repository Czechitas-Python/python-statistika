---
title: Spánek
demand: 2
---

Do království královny Ady mezitím přišel další obchodník, který tentokrát nabízí přípravek, který zvyšuje kvalitu spánku. Ada otestovala přípravek podobným postupem jako dříve, tj. skupině jednotlivců dala skutečný přípravek a části placebo. Poté pomocí chytrých hodinek změřila délku jejich spánku. Data jsou v souboru [spanek.csv](assets/spanek.csv).

1. Definuj testové hypotézy.
1. Proveď test hypotézy a vyhodnoť výsledky.

:::solution

**1. Testové hypotézy:**

- **Nulová hypotéza (H0):** Jednotlivci, kteří dostali přípravek na zlepšení spánku, mají stejnou průměrnou délku spánku jako ti, kteří dostali placebo.
- **Alternativní hypotéza (H1):** Jednotlivci, kteří dostali přípravek na zlepšení spánku, mají větší průměrnou délku spánku než ti, kteří dostali placebo.

**2. Test hypotézy:**

```python
import pandas as pd
from scipy import stats

data = pd.read_csv("spanek.csv")

statistic, p_value = stats.ttest_ind(data['Pripravek'], data['Placebo'], alternative='greater', equal_var=False)

print(f"P-hodnota: {p_value}")
```

**Interpretace:**

P-hodnota je přibližně 0.12, což je více než hladina významnosti 0.05. Proto **nezamítáme nulovou hypotézu**. Nemáme dostatečný důkaz pro tvrzení, že přípravek skutečně zvyšuje délku spánku. Pozorovaný rozdíl v průměrech může být způsoben náhodou.

:::
