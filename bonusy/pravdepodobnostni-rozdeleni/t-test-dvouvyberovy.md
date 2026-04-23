## Dvouvýběrový t-test

Nyní se budeme zabývat situací, kdy máme dva soubory, přičemž pozorování z obou souborů **nelze spárovat**. Soubory tedy mohou mít i odlišný počet pozorování. Předpokládáme však, že soubory mají **shodné rozptyly**. V takovém případě použijeme dvouvýběrový t-test, někdy též označovaný jako dvouvýběrový Studentův test.

Soubor se všemi výpočty naleznete [zde](assets/t-test-dvouvyberovy_dvouvyberovy_t-test.xlsx).

Abychom si přesně ukázali odlišnost od párového t-testu, vyjdeme ze zadání podobného tomu minulému: *Máme data o průměrném počtu vyrobených výrobků pracovníky **ve dvou různých závodech**, přičemž v jednom ze závodů jsou testovány nové výrobní procesy. Vedení společnosti potřebuje ověřit, zda nové výrobní postupy zvýšily produktivitu práce, a v závislosti na tom implementuje tyto postupy v dalších závodech. Ověřte na ![](assets/formula-inline-a20f4290bcdd.png) hypotézu, že v závodě s novými výrobními postupy vyrobí pracovníci v průměru více výrobků, než v závodě s původními postupy, přičemž předpokládáme, že **rozptyl** průměrného počtu výrobků **je v obou závodech stejný**. Vedení v minulosti statisticky ověřilo, že před změnou procesů byli pracovníci v obou závodech v průměru stejně výkonní.*

Protože porovnáváme dva různé závody, nemůžeme pozorování nijak spárovat. Naopak předpokládáme shodný rozptyl hodnot, proto můžeme použít dvouvýběrový t-test.

Soubor ![](assets/formula-inline-76a7f60c3931.png) obsahuje pozorování ze závodu se starými postupy a soubor ![](assets/formula-inline-ea52428a8109.png) pozorování ze závodu s upravenými postupy. Příslušné střední hodnoty pak označíme ![](assets/formula-inline-a1b0faae6adb.png) a ![](assets/formula-inline-2c08d5eaa54d.png). Nyní můžeme formulovat nulovou a alternativní hypotézu:

* ![](assets/formula-inline-09c8897705b9.png) (Střední hodnota obou souborů je stejná.)
* ![](assets/formula-inline-45102e9e617b.png) (Střední hodnota prvního souboru je nižší.)

Alternativní hypotéza nám tedy říká, že pracovníci vyrábějící podle nových postupů jsou v průměru výkonnější.

Definujme si statistiku testu ![](assets/formula-inline-92b4b25b88d2.png) jako

::fig[]{src=assets/formula-block-bd411d33c34d.png}

kde ![](assets/formula-inline-a1facbb711ce.png) a ![](assets/formula-inline-b1750b0a4b7a.png) jsou rozsahy obou souborů a ![](assets/formula-inline-8ae75f16e4b3.png) určíme ze vzorce

::fig[]{src=assets/formula-block-14574def3782.png}

kde ![](assets/formula-inline-654a73120e37.png) a ![](assets/formula-inline-c8fe6904419f.png) jsou výběrové rozptyly obou souborů. Statistika ![](assets/formula-inline-92b4b25b88d2.png) má samozřejmě Studentovo rozdělení a kritický obor určíme ze vztahu

::fig[]{src=assets/formula-block-692dca29aea4.png}

Dvouvýběrový t-test můžeme v Excelu opět provést několika způsoby:

* použitím doplňku Analýza dat,
* použitím funkce T.TEST (nebo TTEST),
* použitím funkcí pro kvantilovou a distribuční funkci Studentova rozdělení.

Modelová data najdete na obrázku níže, rozsah dat je ![](assets/formula-inline-b521cc76570a.png) a ![](assets/formula-inline-d7930ccf0468.png).

::fig[]{src=assets/t-test-dvouvyberovy_dvouvc3bdbc49brovc3bd-t-test-data.png}

## Výpočet s využitím doplňku Analýza dat

Začneme s využitím doplňku Analýza dat. Ten spustíme kliknutím na tlačítko Analýza dat na panelu Data. Vybereme možnost Dvouvýběrový t-test s rovností rozptylů. Do políček 1. soubor a 2. soubor označíme umístění našich souborů. Pokud označíme i záhlaví tabulky, zaškrtneme možnost Popisky. V poli Alfa necháme výchozí hodnotu ![](assets/formula-inline-b7fe838b717e.png) a do pole Výstupní oblast vložíme hranici oblasti, do které budou vloženy výsledky.

::fig[]{src=assets/t-test-dvouvyberovy_dvouvc3bdbc49brovc3bd-t-test-analc3bdza-dat.png}

Výsledky pro naše data jsou na obrázku níže. Hodnota statistiky je pro oba typy testu stejná a najdeme ji v řádku t Stat, v našem případě tedy ![](assets/formula-inline-9f89afedf244.png). Při jednostranném testu nás dále zajímají řádky, které jsou označeny (1).

::fig[]{src=assets/t-test-dvouvyberovy_dvouvc3bdbc49brovc3bd-t-test-analc3bdza-dat-2.png}

Řádek P(T<=t) (1) obsahuje p-hodnotu testu. Opět ale platí, že na tuto hodnotu si musíme dát pozor, protože nemusí vždy odpovídat našemu zadání. V doplňku totiž neurčujeme alternativní hypotézu. Excel **vrací tu ze dvou možných p-hodnot, která je menší než 0,5**. V našem případě (a obecně v případě záporné hodnoty statistiky, resp. v případě vyšší hodnoty průměru prvního souboru) Excel vrací p-hodnotu pro levostranný t-test, což odpovídá našemu zadání. p-hodnota testu je tedy ![](assets/formula-inline-aae7e68a8416.png). V posledním označeném řádku nalezneme hranici kritického oboru. Opět platí, že hranice je zobrazena v absolutní hodnotě. V našem případě máme levostranný test, odsekáváme tedy rozdělení statistiky zleva. Protože Studentovo rozdělení je symetrické kolem nuly, stačí k zobrazené hodnotě připsat minus, tj. kritický obor se nachází v intervalu:

::fig[]{src=assets/formula-block-33da1ee87fc9.png}

## Využití funkce T.TEST (TTEST)

Výpočet s využitím funkce T.TEST (ve verzi 2007 a starších TTEST) je obdobný případu s párovým testem, měníme pouze poslední parametry. V případě nepárového testu se shodnými rozptyly souborů jako poslední parametr zadáváme 2. Jako první dva parametry zadáváme soubory s pozorováními a jako třeba parametr 1, protože provádíme jednostranný test. Výsledný vzorec je tedy:

```text
T.TEST(A2:A41;B2:B31;1;2)
```

Python alternativa:

```python
from scipy.stats import ttest_ind

t_stat, p_value = ttest_ind(x, y, equal_var=True, alternative="less")
```

Funkce nám vrátí hodnotu ![](assets/formula-inline-877e03c97205.png), což je, jak už víme, správný výsledek. Tato funkce se ale chová obdobně jako Analýza dat, tj. ze dvou možných p-hodnot vrací tu nižší. V případě **záporné statistiky** (tedy v případě, kdy je průměr druhého souboru vyšší než prvního) je tento výsledek **správný**, obecně to ale **platit nemusí**. Z tvaru vzorce statistiky vidíme, že statistika je záporná právě tehdy, pokud má druhý soubor vyšší průměr. Vzorec tedy můžeme zjednodušit tím, že se rozhodneme na základě porovnání průměrů obou souborů. Fuunkce podmínky:

```text
=KDYŽ(E3<F3;T.TEST(A2:A41;B2:B31;1;2);1-T.TEST(A2:A41;B2:B31;1;2))
```

Python alternativa:

```python
from scipy.stats import ttest_ind

t_stat, p_value = ttest_ind(x, y, equal_var=True, alternative="less")
```

V případě funkce TTEST postupujeme obdobně, tj. jednodušší varianta vzorce je

```text
TEST(A2:A41;B2:B31;1;2)
```

Python alternativa:

```python
from scipy.stats import ttest_ind

t_stat, p_value = ttest_ind(x, y, equal_var=True, alternative="less")
```

a obecně funguje vzorec

```text
=KDYŽ(E3<F3;TTEST(A2:A41;B2:B31;1;2);1-TTEST(A2:A41;B2:B31;1;2))
```

Python alternativa:

```python
from scipy.stats import ttest_ind

t_stat, p_value = ttest_ind(x, y, equal_var=True, alternative="less")
```

## Manuální výpočet

Poslední možností je provést výpočty "ručně", tj. pomocí funkcí pro distribuční a kvantilovou funkci Studentova rozdělení. Pro výpočet statistiky potřebujeme průměr a výběrový rozptyl, které snadno vypočítáme pomocí funkcí PRŮMĚR a VAR.S (u starších verzí VAR.VÝBĚR).

Uveďme si nejprve vzorec pro výpočet statistiky:

```text
=(E3-F3)/(E11*ODMOCNINA(1/E2+1/F2))
```

Python alternativa:

```python
import numpy as np

sp = pooled_std
t_stat = (x.mean() - y.mean()) / (sp * np.sqrt(1 / len(x) + 1 / len(y)))
```
Hranice kritického oboru určíme pomocí funkce T.INV, tj. pomocí kvantilové funkce Studentova rozdělení. Funkce T.INV se chová naprosto standardně. U levostranného testu odsekáváme kritický obor zleva, jako kvantil tedy zadáváme hladinu významnosti, tj. ![](assets/formula-inline-a20f4290bcdd.png). Počet stupňů volnosti je ![](assets/formula-inline-4ca9ffd9f432.png), tj. výsledný vzorece je:

```text
=T.INV(E7;E2+F2-2)
```

Python alternativa:

```python
from scipy.stats import t

critical_value = t.ppf(alpha, df=len(x) + len(y) - 2)
```

Kritický obor je tedy:

![](assets/formula-inline-0f60009d606b.png).

p-hodnotu určíme pomocí funkce T.DIST. U levostranného testu určujeme p-hodnotu zleva, použijeme tedy funkci Studentova rozdělení T.DIST. Tato funkce se opět chová zcela standardně, stačí tedy zadat hodnotu statistiky a počet stupňů volnosti a výsledek nijak neupravujeme:

```text
=T.DIST(E12;E2+F2-2;PRAVDA)
```

Python alternativa:

```python
from scipy.stats import t

p_value = t.cdf(t_stat, df=len(x) + len(y) - 2)
```
