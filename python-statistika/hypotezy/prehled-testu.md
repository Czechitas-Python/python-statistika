## Přehled statistických testů v Pythonu

Tato část vám pomůže s výběrem vhodného testu pro vaše projekty.

Při výběru testu je možné použít i rozhodovací stromy. Níže je příklad jednoho z nich.

::fig[]{src=assets/strom_1.png}

Druhý strom už je trochu komplikovanější.

::fig[]{src=assets/strom_2.png}

Přehled testů najdeš níže. Testy jsou rozděleny podle:

1. počtu souborů (skupin),
2. testovanému statistickému ukazateli (např. průměru, rozptylu atd.),
3. předpoklady testu (především to, zda test vyžaduje normální rozdělení dat).

### Testy s jedním souborem

Tyto testy porovnávají jednu skupinu (jeden sloupec tabulky) oproti nějaké skutečnosti.

#### Testy na průměr

Testy na průměr porovnávají průměr s nějakou námi definovanou hodnotou. U testů na průměr můžeme alternativní hypotézu formulovat pomocí znaménka není rovno, menší než nebo větší než.

Níže jsou příklady dvojic hypotéz. Pro test hypotézy můžeme využít následující testy:

* [t-test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.ttest_1samp.html#scipy.stats.ttest_1samp), který předpokládá, že data mají normální rozdělení.

* H0: Průměrná výška basketbalistek v České republice je 180 cm
* H1: Průměrná výška basketbalistek v České republice není 180 cm


```python
import pandas as pd
from scipy import stats

data = [170, 180, 175, 183, 178, 182, 185, 176, 179, 181]
data = pd.DataFrame(data, columns=["sloupec_1"])
res = stats.ttest_1samp(data["sloupec_1"], 180)
print(res)
```

* H0: Průměrná chyba při výrobě součástky do motoru je 0.1 mm
* H1: Průměrná chyba při výrobě součástky do motoru je méně než 0.1 mm

Zde v alternativní hypotéze říkáme to, že průměrná chyba je menší, proto využíváme parametr `alternative` (do něj píšeme stejné znaménko jako v alternativní hypotéze, tj. `less`).


```python
data = [0.12, 0.10, 0.11, 0.13, 0.09, 0.11, 0.12, 0.10, 0.11, 0.12]
data = pd.DataFrame(data, columns=["sloupec_1"])
res = stats.ttest_1samp(data["sloupec_1"], 0.1, alternative="less")
print(res)
```


* H0: Průměrné zpoždění vlaku z Prahy do Plzně s odjezdem v 18:38 je 5 minut
* H1: Průměrné zpoždění vlaku z Prahy do Plzně s odjezdem v 18:38 je více než 5 minut.

Zde v alternativní hypotéze říkáme to, že průměrná chyba je větší, proto využíváme parametr `alternative` (do něj píšeme stejné znaménko jako v alternativní hypotéze, tj. `greater`).


```python
data = [5.1, 4.9, 5.8, 7.5, 5.0, 5.1, 4.9, 5.2, 4.8, 5.0]
data = pd.DataFrame(data, columns=["sloupec_1"])
res = stats.ttest_1samp(data["sloupec_1"], 5, alternative="greater")
print(res)
```

#### Testy na rozdělení

Příklad hypotéz:

* H0: Ceny domů mají normální rozdělení
* H1: Ceny domů nemají normální rozdělení

Pro test hypotézy můžeme využít následující testy:

* [Shapiro-Wilk test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.shapiro.html#scipy.stats.shapiro)
* Kombinace D'Agostino‑Pearson testu, který provádí funkce [normaltest](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.normaltest.html#scipy.stats.normaltest).


```python
data = [0.74590569, 1.74565776, 0.58570378, 0.95159044, 0.58572699, 1.20722768, 0.38527559, 1.70051498, 1.10369079, 1.18765377, 1.7503144, 
        0.40093026, 1.2216318,  1.45744714, 1.95942974, 1.08444009, 1.07266436, 0.88722675, 0.48954167, 1.50261749, 1.27005193, 1.026523, 1.44374599, 1.54176153, 0.51657773]
data = pd.DataFrame(data, columns=["sloupec_1"])
res = stats.shapiro(data["sloupec_1"])
print(res)
res = stats.normaltest(data["sloupec_1"])
print(res)
```

### Testy se dvěma statistickými soubory

Tyto testy porovnávají dva různé statistické soubory.

#### Testy na průměr

U testu na průměr máme k dispozici poměrně hodně testů.

Uvažujme nejprve párová pozorování. Párovými pozorování myslíme, že **každému pozorování z jednoho souboru** můžeme **přiřadit jiné pozorování podle nějakého logického klíče**. Například uvažujme školení pracovníků pracující u výrobní linky. Pokud máme data o rychlosti montáže pracovníků před školením (tj. počet smontovaných výrobků za jednotku času) a po školení, můžeme použít párování, protože rychlost před školením a po školení pro jednoho pracovníka tvoří párové pozorování. Pokud bychom chtěli porovnat rychlost pracovníků v jiných směnách nebo jiných závodech, nejedná se o párová pozorování.

Příklad hypotéz:

* H0: Rychlost montáže pracovníků před školením byla stejná jako po školení
* H1: Rychlost montáže pracovníků před školením byla jiná než je po školení

Pro test hypotézy můžeme použít [párový t-test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.ttest_rel.html#scipy.stats.ttest_rel). Test předpokládá, že data mají normální rozdělení.


```python
data_paired = [
    [10.5, 12.2],  # Pracovník 1
    [9.8, 11.4],   # Pracovník 2
    [10.2, 11.7],  # Pracovník 3
    [10.1, 12.3],  # Pracovník 4
    [9.9, 11.6],   # Pracovník 5
    [10.6, 12.1],  # Pracovník 6
    [9.7, 11.9],   # Pracovník 7
    [10.3, 12.0],  # Pracovník 8
    [9.6, 11.8],   # Pracovník 9
    [10.4, 12.4],  # Pracovník 10
    [10.0, 12.5],  # Pracovník 11
    [9.5, 11.3],   # Pracovník 12
    [10.7, 12.6],  # Pracovník 13
    [9.4, 11.2],   # Pracovník 14
    [10.8, 12.7]   # Pracovník 15
]
data_paired = pd.DataFrame(data_paired, columns=["sloupec_1", "sloupec_2"])
res = stats.ttest_rel(data_paired["sloupec_1"], data_paired["sloupec_2"])
print(res)
```

Pro nepárové testy můžeme mít následující hypotézy:

* H0: Rychlost montáže pracovníků v obou sledovaných směnách je stejná
* H1: Rychlost montáže pracovníků v obou sledovaných směnách je různá

Pro test hypotézy můžeme použít [t-test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.ttest_ind.html#scipy.stats.ttest_ind). Test předpokládám, že data mají normální rozdělení. U testu existují dvě varianty - jedna předpokládá, že data mají stejný rozptyl, druhá uvažuje, že soubory mají různé rozptyly.


```python
# rychlosti montáže ve směně 1  (15 nezávislých pracovníků)
shift1 = [12.2, 11.4, 11.7, 12.3, 11.6, 12.1, 11.9, 12.0, 11.8,
          12.4, 11.9, 12.7, 10.3, 9.1, 12.5]
# rychlosti montáže ve směně 2  (jiných 15 pracovníků)
shift2 = [12.4, 11.5, 11.8, 12.2, 11.7, 12.3, 11.8, 12.1, 11.7,
          12.5, 12.4, 11.4, 12.7, 11.3, 12.6]
# Předpokládáme, že rozptyly můžou být různé
res = stats.ttest_ind(shift1, shift2, equal_var=False)
print(res)
```

#### Testy na rozdělení

Testy na rozdělení umožňují porovnat, zda mají dva statistické soubory stejné rozdělení, tj. zda mají stejnou distribuční funkci. Opět rozlišujeme párový a nepárový test.

Pro párový test můžeme formulovat hypotézy:

* H0: Rozdělení rychlosti montáže pracovníků po školení je stejná jako před školením
* H1: Rozdělení rychlosti montáže pracovníků po školení je jiná než před školením

Pro test můžeme použít [Wilcoxonův test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.wilcoxon.html#scipy.stats.wilcoxon). Test je neparametrický, tj. nevyžaduje normální rozdělení.


```python
data_paired = [
    [18.4, 11.7], # Pracovník 1
    [11.8, 14.0], # Pracovník 2
    [14.8, 8.7], # Pracovník 3
    [13.2, 12.5], # Pracovník 4
    [16.3, 12.3], # Pracovník 5
    [9.6, 11.9], # Pracovník 6
    [14.5, 8.4], # Pracovník 7
    [13.0, 10.4], # Pracovník 8
    [11.8, 15.7], # Pracovník 9
    [11.4, 13.6], # Pracovník 10
    [9.2, 9.8], # Pracovník 11
    [13.2, 11.5], # Pracovník 12
    [12.0, 9.9], # Pracovník 13
    [11.7, 12.9], # Pracovník 14
    [13.1, 11.2] # Pracovník 15
]

data_paired = pd.DataFrame(data_paired, columns=["sloupec_1", "sloupec_2"])
res = stats.wilcoxon(data_paired["sloupec_1"], data_paired["sloupec_2"])
print(res)
```

Pro nepárová pozorování můžeme formulovat hypotézy:

* H0: Rozdělení rychlosti montáže v obou sledovaných směnách jsou stejná
* H1: Rozdělení rychlosti montáže v obou sledovaných směnách jsou různá

Pro otestování můžeme použít [Mann–Whitney test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mannwhitneyu.html#scipy.stats.mannwhitneyu). Test je neparametrický, tj. nevyžaduje normální rozdělení.


```python
# rychlosti montáže ve směně 1  (15 nezávislých pracovníků)
shift1 = [12.2, 11.4, 11.7, 12.3, 11.6, 12.1, 11.9, 12.0, 11.8,
          12.4, 11.9, 12.7, 10.3, 9.1, 12.5]
# rychlosti montáže ve směně 2  (jiných 15 pracovníků)
shift2 = [12.4, 11.5, 11.8, 12.2, 11.7, 12.3, 11.8, 12.1, 11.7,
          12.5, 12.4, 11.4, 12.7, 11.3, 12.6]
res = stats.mannwhitneyu(shift1, shift2)
print(res)
```

#### Testy závislosti kategoriálních dat

Kategoriální data jsou taková, která obecně není číslo, ale text (v řeči programování řetězec). Kategoriální proměnnou tedy může být oblíbený programovací jazyk, předmět na škole, nápoj, nejvyšší dosažené vzdělání, zda je člověk kuřák atd. Kategoriální proměnné můžeme porovnat mezi sebou a rozhodnout, zda je mezi nimi závislost.

Hypotézy mohou být například následující:

* H0: Oblíbený předmět nezávisí na pohlaví
* H1: Oblíbený předmět závisí na pohlaví

Pro test hypotézy můžeme použít [chí-kvadrát test nezávislosti](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.chi2_contingency.html). Test je založený na použití kontingenční (pivot) tabulky.


```python
data = [    
    ['female', 'coffee'],
    ['female', 'juice'],
    ['male', 'juice'],
    ['female', 'tea'],
    ['female', 'coffee'],
    ['male', 'juice'],
    ['female', 'coffee'],
    ['male', 'coffee'],
    ['male', 'tea'],
    ['female', 'coffee'],
    ['male', 'coffee'],
    ['male', 'coffee'],
    ['female', 'juice'],
    ['female', 'tea'],
    ['female', 'coffee'],
    ['female', 'tea'],
    ['female', 'juice'],
    ['male', 'juice'],
    ['male', 'tea'],
    ['female', 'coffee'],
    ['male', 'coffee'],
    ['male', 'coffee'],
    ['female', 'coffee'],
    ['male', 'coffee'],
    ['female', 'coffee'],
    ['female', 'coffee'],
    ['male', 'tea'],
    ['male', 'juice'],
    ['male', 'juice'],
    ['female', 'coffee'],
    ['female', 'juice'],
    ['male', 'coffee'],
    ['female', 'juice'],
    ['male', 'juice'],
    ['female', 'coffee'],
    ['female', 'tea'],
    ['male', 'juice'],
    ['female', 'juice'],
    ['female', 'tea'],
    ['male', 'juice'],
    ['male', 'coffee'],
    ['male', 'juice']
]
data = pd.DataFrame(data, columns=["sloupec_1", "sloupec_2"])
data = pd.crosstab(data["sloupec_1"], data["sloupec_2"])
res = stats.chi2_contingency(data)
print(res)
```

#### Test statistické významnosti korelace

Test řeší, zda je zjištěná korelace statisticky významná. 

Uvažujme následující hypotézy:

- H0: Cena domu a obytná plocha domu nejsou statisticky závislé
- H1: Cena domu a obytná plocha domu jsou statisticky závislé

Pokud mají data normální rozdělení, lze využít test založený na [Pearsonově korelačním koeficientu](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.pearsonr.html#scipy.stats.pearsonr). Pokud data nemají normální rozdělení, můžeme využít test s využitím [Spearmanova koeficientu](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.spearmanr.html#scipy.stats.spearmanr) nebo [Kendallova tau](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.kendalltau.html#scipy.stats.kendalltau). (Pozn.: Samotný Pearsonův koeficient normální rozdělení nevyžaduje, test statistické významnosti korelace ale ano.)

Data použitá v ukázce jsou v souboru [house_prices.csv](assets/house_prices.csv).


```python
# Pearson 
# Takto zjistíme výsledek, i když na tato konkrétní data bychom test založení na Pearsonově koeficientu používat neměli, data nemají normální rozdělení.

house_prices = pd.read_csv("house_prices.csv")
res = stats.pearsonr(house_prices["SalePrice"], house_prices["GrLivArea"])
print(res)

# Spearman
res = stats.spearmanr(house_prices["SalePrice"], house_prices["GrLivArea"])
print(res)

# Kendall tau
res = stats.kendalltau(house_prices["SalePrice"], house_prices["GrLivArea"])
print(res)
```

### Testy se třemi a více statistickými soubory

Testy jsou použitelné pro tři a více souborů (skupin) dat. Pozor na to, že tyto testy vám většinou pouze řeknou, že mezi některými skupinami je rozdíl, ale neřeknou přesně mezi kterými.

#### Test na průměr

Test na průměr umožňuje porovnat, zda jsou průměry hodnot různé u tří a více souborů.

Pro test můžeme formulovat hypotézy:

* H0: Průměrný čas montáže je stejný u pracovníků všech tří směn
* H1: Průměrný čas montáže je různý alespoň dvě směny

Uvažujme, že máme ranní, odpolední a noční směnu. Test nám pouze řekne, zda je mezi směnami nějaký rozdíl, ale nevíme přesně jaký. Může tedy být například stejný průměr ranní a odpolední směny a noční se od nich liší, může být stejný průměr ranní a noční směny a odpolední se od nich liší nebo může mít každá směna průměr odlišný od ostatních.

Pokud mají všechny soubory normální rozdělení a stejný rozptyl, můžeme použít [ANOVA test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.f_oneway.html#scipy.stats.f_oneway). Pokud data nemají normální rozdělení, je možné využít neparametrický [Kruskal-Wallis test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.kruskal.html#scipy.stats.kruskal).

### Test na rozptyl

Test na variabilitu umožňují porovnat variabilitu tří a více souborů. Lze je použít i k rozhodnutí, zda je vhodné použití testu ANOVA.

Pro test můžeme formulovat hypotézy:

- H0: Rozptyl času montáže je stejný u pracovníků všech tří směn
- H1: Rozptyl času montáže se liší alespoň pro dvě směny

Pokud mají všechny soubory normální rozdělení, můžeme použít Bartlettův test. Pokud data nemají normální rozdělení, je možné použít Levenův test.
