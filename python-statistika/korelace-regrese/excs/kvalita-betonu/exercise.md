---
title: Kvalita betonu
---

V souboru [Concrete_Data_Yeh.csv](assets/Concrete_Data_Yeh.csv) najdeš informace o kvalitě betonu. Sloupce 1-7 udávají množství jednotlivých složek v kg, které byly přimíchány do krychlového metru betonu (např. cement, voda, kamenivo, písek atd.). Ve sloupci 8 je stáří betonu a ve sloupci 9 kompresní síla betonu v megapascalech. Vytvoř regresní model, který bude predikovat kompresní sílu betonu na základě všech množství jednotlivých složek a jeho stáří. Zhodnoť kvalitu modelu.

Která ze složek betonu ovlivňuje sílu betonu negativně (tj. má záporný regresní koeficient)?


Složky jsou blíže popsány [zde](https://www.ebeton.cz/pojmy/cement-a-jeho-slozky/).

- Cement (cement), kg na m3
- Blast Furnace Slag (cgranulovaná vysokopecní struska), kg na m3
- Fly Ash (popílek), kg na m3
- Water (voda), kg na m3
- Superplasticizer (superplastifikátor), kg na m3, bližší info např. [zde](https://www.chemieprostavbu.cz/sika--superplastifikator-1l/)
- Coarse Aggregate (hrubé kamenivo), kg na m3
- Fine Aggregate (jemné kamenivo), kg na m3
- Age (stáří) ve dnech
- Concrete compressive strength -- quantitative -- MPa -- Output Variable

:::solution
Video s řešením příkladu je [zde](https://youtu.be/iGXlEpf0yb4).

```py
formula = "csMPa ~ cement + slag  + flyash + water + superplasticizer + coarseaggregate + fineaggregate + age"
mod = smf.ols(formula=formula, data=data)
res = mod.fit()
res.summary()
```

R-squared (R²) = 0.616 a Adjusted R-squared = 0.613: Tyto hodnoty ukazují, že model vysvětluje zhruba 61.6% variability pevnosti betonu, což je slušný výsledek pro real-world data v oblasti stavebnictví. Přestože model je užitečný, existují pravděpodobně i další faktory nezahrnuté v modelu, které mohou pevnost betonu ovlivňovat.

Koeficienty: Většina koeficientů má pozitivní hodnoty, což ukazuje, že zvyšují pevnost betonu. Výjimkou je voda, jejíž koeficient je záporný.

Složka betonu, která ovlivňuje sílu betonu negativně:

- Voda (water): Koeficient pro vodu je -0.1499, což znamená, že s každým přidaným litrem vody na kubický metr betonu se pevnost betonu snižuje o 0.1499 MPa. Tento negativní vliv vody je logický, neboť nadměrné množství vody může snížit pevnost betonu tím, že zvyšuje pórovitost a snižuje celkovou hustotu betonové směsi.

:::
