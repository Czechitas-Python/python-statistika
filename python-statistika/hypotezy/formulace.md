## Formulace statistických hypotéz

Uvažujme příklad království, kde vládne královna Ada.

::fig[]{src=assets/ada_01.png}

Jednou za ní přijde obchodník s tím, že má zázračný přípravek, který zlepšuje paměť a soustředění. Ada si není jistá, že přípravek zkustečně funguje, a tak bude chtít ověřit jeho kvalitu pomocí experimentu. Experiment pak vyhodnotí a rozhodne, jestli přípravek pomáhá.

::fig[]{src=assets/ada_02.png}

U experimentu bychom měli využít jednoduché pravidlo: Nikdo ze studentů a studentek by neměl vědět, jestli dostal přípravek. To Ada vyřeší tak, že polovině dá přípravek a polovině placebo, které chutná a vypadá stejně.

Ada předpokládá, že pokud přípravek funguje, průměrný výsledek studentů a studentek, kteří ho dostanou, bude lepší, než výsledek těch, kteří ho nedostanou. Ada tedy provede test hypotézy, zda přípravek skutečně zvýší průměrný počet bodů, který studenti z testu dostanou. 

I Ada se v jejím království musí držet zákonů statistiky. 

Při testování hypotéz vždy nejprve definujeme dvě hypotézy - **nulovou** (*null hypothesis*) a **alternativní** (*alternative hypohesis*). Tyto dvě hypotézy musí být vždy **ve sporu**, tj. nemůže nastat situace, že by byly obě pravdivé. Nulová hypotéza v sobě má často znaménko *rovná se*, alternativní pak mívá znaménko *nerovná se*, *větší než* nebo *menší než*. Dále můžeme v nulové hypotéze tvrdit, že mezi dvěma sloupci v tabulce není závislost, a alternativní hypotéza bude říkat, že závislost existuje (to si ukážeme v dalších lekcích).

Uvažujme tedy následující dvojici hypotéz:

- Nulová hypotéza: Studenti a studentky, kteří dostanou kouzelný přípravek, mají **stejný** průměrný počet bodů jako ti, kteří přípravek nedostanou.
- Alternativní hypotéza: Studenti a studentky, kteří dostanou kouzelný přípravek, mají **větší** průměrný počet bodů jako ti, kteří přípravek nedostanou.

Výsledkem našeho testu by mělo být rozhodnutí, jestli platí alternativní hypotéza, tj. jestli skutečně přípravek pomáhá studentům v testu.

### Získání dat

Ada tedy připraví záhadný přípravek podle formule, kterou jí dal cizinec.

::fig[]{src=assets/ada_03.png}

Ada dále provede sběr dat. Vyrazí tedy do školy a v jedné třídě rozdělí studenty a studentky na dvě skupiny. Jedné dá přípravek a druhé placebo, přičemž nikdo ze studentů neví, co dostal.

::fig[]{src=assets/ada_04.png}

Data máme v souboru [student_results.csv](assets/student_results.csv). Stejně jako Ada použijeme modul `pandas`.

::fig[]{src=assets/ada_06.png}

Níže je kód, pomocí kterého data načteme z tabulky csv.


```python
import pandas as pd

data = pd.read_csv("student_results.csv")
data.head()
```

Zkusme zkontrolovat průměrné výsledky.


```python
data.mean()
```


Ada si tedy řekla, že přípravek musí fungovat. Pak ji ale napadla jedna zákeřná myšlenka: Co když je to náhoda? Kdybychom si rozdělil data na nějaké dvě různé skupiny, výsledky budou taky různé, protože každý ze studentů získal jiný počet bodů. Není tedy pouhou náhodou, že průměry jsou různé?

::fig[]{src=assets/ada_07.png}

K tomu právě slouží testování hypotéz. Řekne nám, **s jakou pravděpodobností** je takový výsledek **náhoda**.

