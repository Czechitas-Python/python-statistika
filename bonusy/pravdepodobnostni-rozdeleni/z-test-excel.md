## z-test v Excelu

V tomto článku je popsáno, jak provést z-test v Excelu. Zadání příkladu a teoretický popis včetně vzorců najdete v článku o [z-testu](z-test.md).

Všechna data a výpočty si můžete stáhnout [zde](assets/z-test-excel_z-test_data.xlsx). Na ukázkovém listu máme data v buňkách A1 až A20. Do buňky D2 si umístíme počet pozorování. Můžeme tam přímo zadat hodnotu 20, případně použít funkci POČET. Průměr pozorování zjistíme vzorcem

```text
=PRŮMĚR(A1:A20)
```

Python alternativa:

```python
sample_mean = sample.mean()
```

a uložíme ho v buňce D3. Směrodatnou odchylku zapíšeme do D4, teoretickou střední hodnotu do D5 a hladinu významnosti do D6. Hranice kritického oboru, které jsme si určili výše, uložíme do D8 a D9.

Nyní můžeme určit hodnotu statistiky pomocí vzorce:

```text
=(PRŮMĚR(A1:A20)-D5)/D4*ODMOCNINA(D2)
```

Python alternativa:

```python
import numpy as np

z_stat = (sample.mean() - mu0) / sigma * np.sqrt(n)
```

Protože ještě budeme zadávat vzorec pro p-hodnotu, uložte hodnotu statistiky do buňky D10.

::fig[]{src=assets/z-test-excel_z-test-data-a-vc3bdsledky.png}

Hodnota statistiky ![](assets/formula-inline-8867b25c2c84.png). Tato hodnota je mimo kritický obor, proto nulovou hypotézu nezamítáme.

## Určení p-hodnoty

V současné době se při testování hypotéz často využívá **p-hodnota**. p-hodnota je **mezní hladina významnosti, pro kterou ještě nulovou hypotézu nezamítáme**. Pokud je **p-hodnota větší nebo rovna než hladina významnosti**, nulovou hypotézu **nezamítáme.**

Podívejme-se na následující obrázek. Na něm vidíme p-hodnotu jako modrou šrafovanou plochu, což je plocha od hodnoty statistiky směrem vlevo. V případě našeho oboustranného testu musíme uvažovat i stejně velkou plochu napravo.

::fig[]{src=assets/z-test-excel_p-hodnota.png}

p-hodnotu určíme pomocí distribuční funkce normálního rozdělení. Hodnotu statistiky máme v buňce D10. Pro hodnoty distribuční funkce v Excelu použijeme funkci NORM.DIST, přičemž jako čtvrtý parametr zadáváme hodnotu PRAVDA. Pokud bychom zadali hodnota NEPRAVDA, získáme hodnotu funkce hustoty. Hodnotu distribuční funkce potřebujeme pro hodnotu statistiky. Protože uvažujeme i stejně velkou plochu v pravé části, násobíme výsledek funkce dvěma:

```text
=NORM.DIST(D10;0;1;PRAVDA)*2
```

Python alternativa:

```python
from scipy.stats import norm

p_value = 2 * min(norm.cdf(z_stat), norm.sf(z_stat))
```
