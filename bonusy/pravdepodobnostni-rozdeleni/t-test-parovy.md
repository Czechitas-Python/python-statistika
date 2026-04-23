## Párový t-test

Uveďme si nyní typické zadání párového testu: *Máme data o průměrném počtu vyrobených výrobků 20 pracovníky za jednu směnu. Vedení společnosti následně provedlo změnu výrobních procesů a **pro stejných 20 pracovníků** provedlo nová měření. Ověřte na hladině významnosti ![](assets/formula-inline-a20f4290bcdd.png), že došlo ke zvýšení průměrné produkce pracovníků.*

Klíčové v zadání je, že jsme provedli **2 sady měření pro 20 stejných pracovníků**. Můžeme tedy vždy spárovat dvě měření jednoho konkrétního pracovníka. Z toho důvodu můžeme zvolit párový t-test. Pokud bychom měli měření od různých pracovníků nebo pokud by měření byla anonymní, museli bychom zvolit jeden ze zbývajících dvou testů.

Označme si soubory jako ![](assets/formula-inline-8066c3026174.png) a ![](assets/formula-inline-5b0587e78315.png) a jejich střední hodnoty jako ![](assets/formula-inline-2c43685d3d54.png) a ![](assets/formula-inline-f434592d6390.png). Nyní můžeme formulovat nulovou a alternativní hypotézu:

* ![](assets/formula-inline-f9d788cd59b9.png) (Střední hodnota obou souborů je stejná.)
* ![](assets/formula-inline-f4babd2edb9b.png) (Střední hodnota prvního souboru je nižší.)

Alternativní hypotézu volíme takto, protože máme dle zadání prokázat *zvýšení* průměrné produkce pracovníka (a předpokládáme, že snížení produktivity je nepravděpodobné). Provedeme tedy **levostranný test**.

Uvažujme nyní náhodnou veličinu ![](assets/formula-inline-33ff90f695ba.png), kterou si definujeme jako **rozdíl náhodných veličin** ![](assets/formula-inline-8066c3026174.png) a ![](assets/formula-inline-5b0587e78315.png), tj. ![](assets/formula-inline-6f56da11d784.png). Pro každou dvojici pozorování můžeme určit ![](assets/formula-inline-45c9b58c74fd.png), které je dáno jako rozdíl pozorování, tj.

::fig[]{src=assets/formula-block-464962123d9d.png}

Mají-li oba soubory stejnou střední hodnotu, pak by měla mít náhodná veličina ![](assets/formula-inline-33ff90f695ba.png) nulovou střední hodnotu, tj. ![](assets/formula-inline-92b11db8b327.png). Jestliže má druhý soubor větší střední hodnotu, pak má i veličina ![](assets/formula-inline-33ff90f695ba.png) ostře zápornou střední hodnotu, tj. ![](assets/formula-inline-381b53a2ff02.png). Náš případ dvouvýběrového testu tedy můžeme jednoduše převést na [jednovýběrový t-test](t-test.md) provedený nad veličinou ![](assets/formula-inline-33ff90f695ba.png).

Definujme si průměr pozorování veličiny ![](assets/formula-inline-33ff90f695ba.png) jako ![](assets/formula-inline-debb6f230cbc.png) a výběrovou směrodatnou odchylku jako  ![](assets/formula-inline-cf485edcbc5e.png). Nyní můžeme definovat statistiku testu ![](assets/formula-inline-92b4b25b88d2.png) jako

::fig[]{src=assets/formula-block-c2d7f9c44b0a.png}

a tato veličina má ![](assets/formula-inline-15ad67d2e649.png) stupňů volnosti. Analogicky určíme i kritický obor pro statistiku:

::fig[]{src=assets/formula-block-13850a0ef69f.png}

kde ![](assets/formula-inline-c74b7df15e3f.png) je kvantil Studentova rozdělení s ![](assets/formula-inline-d8bebef4d05f.png) stupni volnosti.

Párový t-test můžeme v Excelu provést několika způsoby:

* použitím doplňku Analýza dat,
* použitím funkce T.TEST (nebo TTEST),
* použitím funkcí pro kvantilovou a distribuční funkci Studentova rozdělení.

Soubor s ukázkovými daty i všemi výpočty si [můžete stáhnout zde](assets/t-test-parovy_parovy-t-test.xlsx).

### Výpočet s využitím doplňku Analýza dat

Tento doplněk jsme již používali ke generování náhodných souborů dat. Spustíte ho kliknutím na **tlačítko Analýza dat na panelu Data**. Pokud tam tlačítko nevidíte, musíte si doplněk [nainstalovat](https://support.office.com/cs-cz/article/Zaveden%C3%AD-dopl%C5%88ku-Analytick%C3%A9-v-aplikaci-Excel-6a63e598-cd6d-42e3-9317-6b40ba1a66b4). Po kliknutí na tlačítko se vám zobrazí seznam analytických nástrojů. Vyberte možnost Dvouvýběrový párový t-test na střední hodnotu. Analýza dat zvládá oboustranný i jednostranný párový t-test.

::fig[]{src=assets/t-test-parovy_parovy-t-test-analyza-dat-1.png}

V dialogovém okně označíme oblast se dvěma soubory, a to včetně záhlaví. Pak ale nesmíme zapomenout zaškrtnout pole **Popisky**, čímž dáváme Excelu najevo, že v prvním řádku najde názvy datových souborů. Dále vyplníme výstupní oblast, pole Hypotetický rozdíl středních hodnot můžeme nechat prázdné.

::fig[]{src=assets/t-test-parovy_parovy-t-test-analyza-dat-22.png}

Níže vidíme výsledky. Excel určil statistiku ![](assets/formula-inline-8879146567f8.png) a p-hodnotu jako ![](assets/formula-inline-7c4f3031e6f4.png). Na hodnotu na třetím řádku si ale musíme dát pozor. Obsahuje hranici kritické hodnoty, ta je ale vždy kladná. Protože my provádíme levostranný test, bude se kritický obor nacházet **nalevo od nuly**. K hodnotě stačí dopsat minus, protože hustota Studentova rozdělení je sudá funkce. Kritický obor vyjádřený intervalem je tedy:

::fig[]{src=assets/formula-block-4dd1aa6357af.png}

::fig[]{src=assets/t-test-parovy_parovy-t-test-analyza-dat-31.png}

Doplňku Analýza dat jsme **nespecifikovali alternativní hypotézu**. Excel u jednostranného testu vždy vrací p-hodnotu, která je menší než ![](assets/formula-inline-5e7cf72318c1.png), tj. v našem případě vrací správně hodnotu pro levostranný test. Pokud by však průměr dat druhého souboru byl menší než průměr dat prvního, byla by statistika kladná a v tom případě by p-hodnota odpovídající této hodnotě musela být větší než ![](assets/formula-inline-5e7cf72318c1.png). V takovém případě by bylo třeba správnou p-hodnotu dopočítat.

### Využití funkce T.TEST (TTEST)

Excel obsahuje funkci T.TEST (v případě verze 2007 a starších funkci TTEST), která je další rychlou cestou k provedení párového t-testu. Její nevýhodou je, že vrací pouze p-hodnotu, nikoli však hodnotu statistiky a kritický obor.

V novějších verzích Excelu zadáme vzorec:

```text
=T.TEST(A2:A21;B2:B21;1;1)
```

Python alternativa:

```python
from scipy.stats import ttest_rel

t_stat, p_value = ttest_rel(x, y, alternative="less")
```

První dva parametry jsou odkazy na datové soubory, tentokrát již bez záhlaví. Třetí parametr zadáváme 1, protože požadujeme jednostranný test, pro oboustranný test bychom zvolili 2. Třetím parametrem volíme typ testu. Typy testů jsou specifikované na začátku článku. Pro párový test volíme 1, pro nepárový test se shodnými rozptyly 2 a pro nepárový test s různými rozptyly 3.

Tato funkce funguje podobně jako Analýza dat, tj. **nezadáváme mu typ jednostranného testu** a funkce vybírá tu variantu, pro kterou je p-hodnota menší než ![](assets/formula-inline-5e7cf72318c1.png). Chceme-li si pojistit, že výsledek bude vždy správný, můžeme provést následující úpravu:

```text
=KDYŽ(F12<0;T.TEST(A2:A21;B2:B21;1;1);1-T.TEST(A2:A21;B2:B21;1;1))
```

Python alternativa:

```python
from scipy.stats import ttest_rel

t_stat, p_value = ttest_rel(x, y, alternative="less")
```

Pokud by tedy hodnota statistiky byla kladná, použije se jednotkový doplněk k výsledku funkce T.TEST, což je správný výsledek.

Ve starších verzích Excelu použijeme funkce TTEST, parametry volíme stejné jako u novější varianty funkce:

```text
=TTEST(A2:A21;B2:B21;1;1)
```

Python alternativa:

```python
from scipy.stats import ttest_rel

t_stat, p_value = ttest_rel(x, y, alternative="less")
```

Opět můžeme provést úpravu zaručující správný výsledek i při kladné hodnotě statistiky:

```text
=KDYŽ(G12<0;TTEST(A2:A21;B2:B21;1;1);1-TTEST(A2:A21;B2:B21;1;1))
```

Python alternativa:

```python
from scipy.stats import ttest_rel

t_stat, p_value = ttest_rel(x, y, alternative="less")
```

### Manuální výpočet

Poslední variantou je manuální výpočet. Nejprve vytvoříme sloupec s rozdíly hodnot, tj. sloupec s pozorovanými hodnotami veličiny ![](assets/formula-inline-33ff90f695ba.png). Dále dopočítáme průměrný rozdíl, směrodatnou odchylku, hodnoty statistiky a určíme počet dat.

::fig[]{src=assets/t-test-parovy_parovy-t-test-data-a-vysledky3.png}

Hranice kritického oboru určíme pomocí funkce kvantilové funkce Studentova rozdělení T.INV. Protože kritický obor určujeme zleva, zadáváme rovnou kvantil a počet stupňů volnosti:

```text
=T.INV(F7;F2-1)
```

Python alternativa:

```python
from scipy.stats import t

critical_value = t.ppf(alpha, df=len(x) - 1)
```

p-hodnotu testu určíme pomocí funkce distribuční funkce Studentova rozdělení T.DIST, které jako parametry zadáme hodnotu statistiky, počet stupňů volnosti a parametr PRAVDA, který říká, že chceme hodnotu distribuční funkce a nikoli funkce hustoty.

```text
=T.DIST(F12;F2-1;PRAVDA)
```

Python alternativa:

```python
from scipy.stats import t

p_value = t.cdf(t_stat, df=len(x) - 1)
```

V literatuře se uvádí, že pro větší objemy dat (zpravidla ![](assets/formula-inline-b49b7e2e7f96.png)) je možné nahradit Studentovo rozdělení normálním rozdělení. Není však chybou, pokud i pro vyšší objemy dat použijeme Studentovo rozdělení.
