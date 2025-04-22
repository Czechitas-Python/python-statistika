## Čtení na doma: Výpočet korelace

V této části si ukážeme, na jaké myšlence jsou postaveny oba koeficienty pro výpočet korelace.

### Pearsonův korelační koeficient

Pearsonův korelační koeficient měří sílu a směr lineární závislosti mezi dvěma veličinami. Nabývá hodnot od -1 do +1, přičemž hodnota +1 znamená dokonalou pozitivní lineární závislost, hodnota -1 značí dokonalou negativní lineární závislost a 0 znamená žádnou lineární závislost. 

Výpočet se provádí podle vzorce:

::fig[]{src=assets/pearson_correlation_formula.png}

Vzorec ověřuje, jestli s rostoucím počtem hodin studia roste počet získaných bodů. Protože vidíme, že počet získaných bodů postupně roste, je koeficient vysoký. Do hry ale vstupuje i náhoda, např. někdo měl větší štěstí na otázky atd.

Příklad s vlastním datasetem (8 hodnot):

| Hodiny studia | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---------------|---|---|---|---|---|---|---|---|
| Skóre testu   | 0.87 | 7.47 | 4.23 | 6.54 | 11.56 | 14.52 | 12.63 | 21.53 |

- Průměry: *x = 4.5*, *y = 9.42*
- Po výpočtu dle vzorce výše získáme korelační koeficient 0.92. To ukazuje silnou pozitivní lineární závislost mezi počtem hodin studia a skóre testu.

```py
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd

data = pd.DataFrame({
    'Hodiny_studia': [1, 2, 3, 4, 5, 6, 7, 8],
    'Skore_testu': [0.87, 7.47, 4.23, 6.54, 11.56, 14.52, 12.63, 21.53]
})

data.corr()
```

### Kendallovo Tau

Kendallovo Tau měří sílu a směr monotónní závislosti mezi dvěma veličinami. Hodnota koeficientu se pohybuje od -1 do +1 a jeho význam je stejný, koeficient má ale jiný způsob výpočtu. Kendallovo Tau se zaměřuje na pořadí hodnot. Porovnávají se všechny možné **dvojice** pozorování. Pár je označen jako:

- *konkordantní (shodný)*, pokud pořadí obou proměnných je stejné,
- *diskordantní (neshodný)*, pokud pořadí obou proměnných je opačné.

Obecný vzorec pro výpočet Kendallova Tau:

::fig[]{src=assets/kendall_tau_formula.png}

kde:
- *C* je počet konkordantních párů,
- *D* je počet diskordantních párů,
- *n* je počet pozorování.

Příklad s vlastním datasetem (8 hodnot):

| Hodiny studia | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---------------|---|---|---|---|---|---|---|---|
| Skóre testu   | 0.87 | 7.47 | 4.23 | 6.54 | 11.56 | 14.52 | 12.63 | 21.53 |

- Po výpočtu Kendallova Tau získáme 0.79. To ukazuje silnou pozitivní monotónní závislost mezi počtem hodin studia a skóre testu.

#### Příklad vyhodnocení dvou párů

Zvažme dvojici hodnot:
- (1 hodina, skóre 0.87)
- (3 hodiny, skóre 4.23)

Vyhodnocení:
- Hodiny studia: 1 < 3
- Skóre testu: 0.87 < 4.23

Pořadí je shodné, proto jde o *konkordantní pár*

Dále zvažme dvojici:
- (1 hodina, skóre 0.87)
- (2 hodiny, skóre 7.47)

Vyhodnocení:
- Hodiny studia: 1 < 2
- Skóre testu: 0.87 < 7.47

Opět pořadí souhlasí, jde tedy o *konkordantní pár*.

Dále zvažme dvojici:

- (2 hodiny, skóre 7.47)
- (3 hodiny, skóre 4.23)

Vyhodnocení:

- Hodiny studia: 2 < 3
- Skóre testu: 7.47 > 4.23

Pořadí je opačné, proto jde o *diskordantní pár*.

Stejným způsobem se vyhodnotí všechny páry a vloží se do vzorce.

```py
from scipy.stats import kendalltau
import pandas as pd

data = pd.DataFrame({
    'Hodiny_studia': [1, 2, 3, 4, 5, 6, 7, 8],
    'Skore_testu': [0.87, 7.47, 4.23, 6.54, 11.56, 14.52, 12.63, 21.53]
})

kendalltau(data['Hodiny_studia'], data['Skore_testu'])
```
