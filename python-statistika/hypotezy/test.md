## Test statistické hypotézy

Ada dále potřebuje zvolit vhodný test pro ověření naší hypotézy. Cílem testu je ověřit, zda platí alternativní hypotéza. Na různé dvojice hypotéz ale existují různé testy a je potřeba použít vhodný test pro daný případ. Ada nejprve vyrazí do knihovny a začne studovat učebnice statistiky. Tam se dočte následující.

::fig[]{src=assets/ada_05.png}

Statistických testů existuje obrovské množství a výběr toho správného závisí obecně na několika faktech:

- **Počet souborů (skupin) dat**, se kterými chceme v testu pracovat. V tomto konkrétním případě chceme pracovat se **dvěma** soubory (*sample*), můžeme mít ale pouze jeden či naopak 3 a více. Soubory myslíme "skupiny", které v testu porovnáváme. V našem testu máme dvě skupiny - skupinu s kouzelným přípravkem a skupinu s placebem. Nejde o to, kolik dat máme pro každou ze skupin. Mohli bychom mít například milion studentů a studentek v každé skupině, pořád by to ale byly dvě skupiny.
- **Statistický ukazatel** nebo skutečnost, kterou chceme ověřit. V našem případě půjde o průměr, ale je možné testovat i jiné skutečnosti.
- **Předpoklady testu.** Předpoklad je nějaká podmínka, která musí být splněna, aby test dával kvalitní výsledky.

Naše království je trochu modernější a učebnice už studovat nemusíme. Můžeme použít rádce, který se jmenuje ChatGPT.

::fig[]{src=assets/gpt_01.png}

Níže je text promptu.

> Máme dvě skupiny studentů - jedna skupina dostala přípravek na zlepšení výkonu mozkových funkcí a druhá skupina dostala placebo. Máme dvě hypotézy.
>
> Nulová hypotéza: Studenti a studentky, kteří dostanou kouzelný přípravek, mají stejný průměrný počet bodů jako ti, kteří přípravek nedostanou.
> Alternativní hypotéza: Studenti a studentky, kteří dostanou kouzelný přípravek, mají větší průměrný počet bodů jako ti, kteří přípravek nedostanou.
>
> Jaký je vhodný statistický test?

Níže je příklad možné odpovědi.

::fig[]{src=assets/gpt_02.png}

Co tento výsledek znamená?

- Máme **jednostrannou alternativní hypotézu**. Tím se odkazuje na znaménko "méně než" v alternativní hypotéze. Znaménko "ukazuje na jednu stranu", máme tedy jednostrannou alternativu. Pokud bychom tam měli znaménko nerovná se, šlo by o oboustrannou alternativu.
- Máme použít tvz. **t-test**. To je jeden z testů, které jsou vhodné.
- Tvrdí, že dvě **nezávislé skupiny**. Co by byla závislá skupina? Např. bychom mohli nechat studenty napsat test bez použití přípravku a poté dát všem studentům přípravek a dát jim další test. Poté bychom srovnávali výsledek každého ze studentů bez přípravku a s přípravkem. Protože bychom měli 2 hodnoty pro každého ze studentů, šlo by o závislé skupiny.
- Mluví o **předpokladech normality a homogenity rozptylu**. Každý test má nějaké předpoklady, které by měly být splněny, aby test dával co nejlepší výsledky. O tom si ještě řekneme později. V tomto případě budeme předpokládat, že předpoklady jsou splněny.

### Vyhodnocení testu

Je zřejmé, že obě hypotézy nemohou být pravidivé. Při testování hypotéz můžeme dojít k následujícím závěrům:

* zamítáme (*reject*) H0 (a tedy tvrdíme, že platí H1),
* nezamítáme (*do not reject*) H0 (a tedy jsme neprokázali, že platí H1).

Poněkud nepříjemnou zprávou pro vás může být informace, že výsledek našeho testu může být chybný, a to i v případě, že jsme postuovali správně. Může se totiž stát, že prostě máme smůlu na náš vzorek. Může se třeba stát, že jsme (čistě náhodou) dali přípravek studentům, kterým se test více povedl. Testování hypotéz nám řekne, jak je pravděpodobné, že lepší výsledek skupiny s přípravkem je náhoda.

Při testu hypotézy volíme tzv. hladinu významnosti. Tou říkáme, jak malá musí být pravděpodobnost toho, že námi zjištěný výsledek (nebo jakýkoli výraznější rozdíl) je náhoda. Např. obvykle je volená hladina významnosti 5 %. To znamená, že pokud je pravděpodobnost toho, že zjištěný rozdíl průměrů je čistě náhoda, méně než 5 %, zamítneme nulovou hypotézu a konstatujeme, že příspěvek je účinný.

### Výpočet

Nyní se Ada musí vrátit k počítači a pustit se do výpočtu.

::fig[]{src=assets/ada_09.png}

My zkusíme ChatGPT požádat, aby nám pomohl a připravil kód pro výpočet.

::fig[]{src=assets/gpt_03.png}

Výsledek zkusíme spustit.

ChatGPT nám radí použít funkci `ttest_ind` z modulu `scipy`. Ta provede test hypotézy. Jako parametry vkládáme sloupce z tabulky `data`. Na pořadí sloupců záleží, protože přidáváme parametr `alternative=greater`. To znamená, že alternativní hypotéza tvrdí, že průměr první skupiny je větší než průměr druhé skupiny.

Funkce nám vrátí dvě hodnoty - `statistics` a `p_value`. Nás bude zajímat pouze `p_value`. Tato hodnota nám říká, jak velká je pravděpodobnost, že zjištěný výsledek může být náhoda.

Platí následující **obecná** pravidla.

- Pokud je **p-hodnota menší než hladina významnosti, zamítáme nulovou hypotézu** (tj. platí alternativní hypotéza).
- Pokud je **p-hodnota větší než hladina významnosti, nezamítáme nulovou hypotézu.**

Pokud si zvolíme hladinu významnosti jako 5 %, což je nejčastější volba, můžeme zapsat pravidlo konkrétněji.

- Pokud je **p-hodnota < 0.05, zamítáme nulovou hypotézu** (tj. platí alternativní hypotéza).
- Pokud je **p-hodnota > 0.05, nezamítáme nulovou hypotézu.**


```python
from scipy import stats

statistic, p_value = stats.ttest_ind(data['Pripravek'], data['Placebo'], alternative='greater')

print(f"P-hodnota: {p_value}")
```

V našem případě tedy platí, že p-hodnota je přibližně 1 %. Pravděpodobnost, že je tento výsledek náhoda, je tedy velmi malá a my jsme dospěli k tomu, že přípravek funguje.


Ada se rozhodla, že zkusí výsledky prezentovat pomocí grafů. My jí s tím pomůžeme. Ale nejprve nás čeká cvučení.

::fig[]{src=assets/ada_08.png}

## Cvičení

::exc[excs/spanek]
