## Welchův oboustranný t-test

V případě oboustranného testu řešíme pouze to, zda je mezi středními hodnotami rozdíl. Vraťme se k našemu příkladu s počty vyrobených výrobků ve dvou různých závodech. Nyní tedy rozhodneme *pouze* o tom, zda se průměrné počty mezi závody *liší*.

Naše hypotézy jsou nyní:

* ![](assets/formula-inline-de3fb8dd7878.png) (Střední hodnota obou souborů je stejná.)
* ![](assets/formula-inline-ecdaf6333ffb.png) (Střední hodnota prvního souboru je nižší.)

Soubor s daty i výpočty si můžete stáhnout [zde](assets/welchuv-test-oboustranny_welschuv-test.xlsx).

Test opět provedeme na hladině významnosti ![](assets/formula-inline-a20f4290bcdd.png).

::fig[]{src=assets/welchuv-test-oboustranny_oboustranny-welchuv-test-excel.png}

## Výpočet pomocí doplňku Analýza dat

Test spustíme pomocí stejného postupu jako v ostatních variantách. Hodnota statistiky testu je ![](assets/formula-inline-355d001c302c.png). p-hodnotu a hranici kritického oboru nyní najdeme v posledních dvou řádcích. p-hodnota testu je ![](assets/formula-inline-ee78fe499230.png). Kritický obor je v případě oboustranného testu rozdělen na dvě části. Analýza dat nám vrací dolní hranici pravé části, horní hranici levé získáme, když před danou hranici napíšeme 0. Kritický obor je tedy ![](assets/formula-inline-f1555a042c10.png).

::fig[]{src=assets/welchuv-test-oboustranny_oboustranny-welchuv-test-ad-vysledky.png}

Statistika tedy leží v kritickém oboru a p-hodnota je nižší než hladina významnosti, na ![](assets/formula-inline-a20f4290bcdd.png) tedy zamítáme ![](assets/formula-inline-2a20eef8c291.png). Tentokrát jsme však pouze prokázali, že mezi výkonností závodů existuje rozdíl, nelze však tvrdit, že v druhém závodě je výkonnost vyšší.

## Výpočet pomocí funkce T.TEST

V případě oboustranného testu zadáváme na třetí pozici číslo 2, díky čemuž nám funkce T.TEST vrátí p-hodnotu oboustranného testu. V našem případě to je tedy hodnota ![](assets/formula-inline-ee78fe499230.png).

```text
=T.TEST(A2:A21,B2:B26,2,3)
```

Python alternativa:

```python
from scipy.stats import ttest_ind

t_stat, p_value = ttest_ind(x, y, equal_var=False, alternative="two-sided")
```

V případě oboustranného testu je vrácená hodnota vždy správná a funkci již nemusíme nijak upravovat.
