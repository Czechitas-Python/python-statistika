---
title: Hodnocení hotelů
demand: 2
---

Stáhni si soubor [global_hotel_reviews.csv](assets/global_hotel_reviews.csv). Naším úkolem je porovnat průměrné hodnocení hotelů ve Francii a Švýcarsku. Číselné hodnocení je ve sloupci `Rating` a obsahuje pouze hodnoty 2, 4, 6, 8 a 10. V tomto případě použij Mann-Whitney test, protože data nemají normální rozdělení. Země, kde se hotel nachází, je ve sloupci `Country`.

1. Definuj testové hypotézy.
1. Proveď test hypotézy a vyhodnoť výsledky.

Níže máš kód k načtení dat. Vytvoří ti dvě série (`data_fr` a `data_sw`) a ty můžeš použít ve svém testu.


```python
import pandas as pd

# Načtení dat z CSV souboru do DataFrame objektu
# Ty pravděpodobně podadresář statistika-1-assets nemáš, tak ho smaž
data = pd.read_csv("global_hotel_reviews.csv")

# Přetvoření tabulky tak, aby se hodnoty "Rating" seskupily pod jediný sloupec "variable"
data = data.melt("Country", "Rating")

# Vyfiltrování dat pouze pro zemi "France" a získání hodnot sloupce "value"
data_fr = data[data["Country"] == "France"]["value"]

# Vyfiltrování dat pouze pro zemi "Switzerland" a získání hodnot sloupce "value"
data_sw = data[data["Country"] == "Switzerland"]["value"]

```

:::solution

**1. Testové hypotézy:**

- **Nulová hypotéza (H0):** Průměrné hodnocení hotelů ve Francii je stejné jako průměrné hodnocení hotelů ve Švýcarsku.
- **Alternativní hypotéza (H1):** Průměrné hodnocení hotelů ve Francii je různé od průměrného hodnocení hotelů ve Švýcarsku.

**2. Test hypotézy:**

```python
from scipy import stats

# Mann-Whitney U test (oboustranný test)
statistic, p_value = stats.mannwhitneyu(data_fr, data_sw)

print(f"P-hodnota: {p_value:.6f}")
```

**Interpretace:**

P-hodnota je prakticky 0, což je výrazně méně než 0.05. Proto **zamítáme nulovou hypotézu** a přijímáme alternativní hypotézu. Průměrné hodnocení hotelů ve Francii (8.85) je statisticky významně různé od průměrného hodnocení hotelů ve Švýcarsku (8.44). Hotely ve Francii mají v průměru vyšší hodnocení.

:::
