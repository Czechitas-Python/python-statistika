## Čtení na doma: Výpočet korelace

Pearsonův korelační koeficient měří sílu a směr lineární závislosti mezi dvěma veličinami. Nabývá hodnot od -1 do +1, přičemž hodnota +1 znamená dokonalou pozitivní lineární závislost, hodnota -1 značí dokonalou negativní lineární závislost a 0 znamená žádnou lineární závislost. Výpočet se provádí podle vzorce:

::fig[]{src=assets/pearson_correlation_formula.png}

Příklad s vlastním datasetem (8 hodnot):

| x | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 |
|---|---|---|---|---|---|---|----|----|
| y | 2 | 3 | 5 | 4 | 6 | 5 | 7  | 6  |

- Průměry: \( \bar{x} = 7.5 \), \( \bar{y} = 4.75 \)
- Po výpočtu dle vzorce výše získáme korelační koeficient \( r \approx 0.87 \). To ukazuje středně silnou pozitivní lineární závislost mezi x a y.

```py
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd

data = pd.DataFrame({
    'x': [4, 5, 6, 7, 8, 9, 10, 11],
    'y': [2, 3, 5, 4, 6, 5, 7, 6]
})

data.corr()
```

Kdybys chtěl taky graf s regresní přímkou nebo přidat `r` přímo do vizualizace, dej vědět — můžu to rozšířit.
