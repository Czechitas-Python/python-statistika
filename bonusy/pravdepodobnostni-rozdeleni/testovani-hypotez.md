## Testování hypotéz

Vraťme se k příkladu s házením mincí. Vyhodíme-li minci do vzduchu, pravděpodobnost, že dopadne
na rub i líc, je 50 %. Uvažujme, že přijde člověk, který o sobě tvrdí, že díky svým nadpřirozeným schopnostem
dokáže zařídit, že mince dopadá častěji na líc. My mu nevěříme a rádi bychom provedli experiment, abychom
jeho "nadpřirozenou" schopnost ověřili.

Věrohodnost experimentu bude samozřejmě záviset na počtu provedených hodů.
Výsledkem našeho experimentu je, že bychom se měli přiklonit k jedné ze dvou následujících možností:

- Mince dopadá na líc s pravděpodobností 50 % (a tedy náš "kouzelník" je podvodník).
- Mince dopadá na líc častěji (a tedy náš "kouzelník" má skutečně schopnost ovlivnit dopad mince).

Tato tvrzení označujeme jako **hypotézy** a platí, že nikdy nemohou být pravdivé obě. První z těchto hypotéz
označujeme jako **nulovou hypotézu** a druhou jako **alternativní hypotézu**.

Uvažujme například, že by kouzelník hodil mincí dvakrát a v obou případech padla líc. Jaká je pravděpodobnost, že
se tak stalo náhodou? To můžeme zjistit pomocí pravděpodobnostní funkce binomického rozdělení.

```python
from scipy.stats import binom

print(binom.pmf(2, 2, 0.5))
```

Výsledek je 0.25. Pravděpodobnost, že šlo o náhodu, je tedy 25 %. To je poměrně vysoké číslo a dva hody by nás
pravděpodobně nepřesvědčily.

```python
print(binom.pmf(3, 3, 0.5))
```

Pro 3 hody je pravděpodobnost 12.5 %.

Pravděpodobnost, že při *n* hodech padne vždy líc, si můžeme zobrazit jako graf.

```python
import pandas
import matplotlib.pyplot as plt

p = 0.5
n = list(range(1, 10))

y = binom.pmf(k=n, n=n, p=p)
data = pandas.DataFrame(y)
data["Color"] = numpy.where(data[0] > 0.05, "blue", "red")
data[0].plot(kind="bar", color=data["Color"])
```

::fig[]{src=assets/plot_06.png}

Dilematem, kterému čelíme při testování hypotéz, je otázka: Může být výsledek našeho pozorování považován za důkaz,
že nulová hypotéza neplatí? K vyřešení tohoto dilematu si před provedením testu určíme mezní pravděpodobnost, kterou
označujeme jako **hladina významnosti**.

Pokud je, za předpokladu platnosti nulové hypotézy, pravděpodobnost výsledku experimentu
menší než hladina významnosti, uzavřeme testování se závěrem, že nulová hypotéza neplatí,
tedy **zamítáme ji**. Pokud je pravděpodobnost výsledku větší než hladina významnosti,
není to pro nás dostatečný důkaz pro zamítnutí nulové hypotézy a říkáme, že **nulovou hypotézu nezamítáme**.
Nikdy neříkáme, že nulovou hypotézu přijímáme!

Hladinu významnosti nejčastěji volíme jako 5 %. Pokud by tedy například kouzelník hodil mincí třikrát a ve všech
případech padla líc, není to pro nás dost přesvědčivé, protože pravděpodobnost takového výsledku,
při platnosti nulové hypotézy, je 12.5 %, což je více než 5 %.

Pokud by kouzelník hodil mincí pětkrát a ve všech případech padla líc, je to pro nás dostatečně přesvědčivý důkaz,
protože pravděpodobnost takového výsledku, pokud by platila nulová hypotéza, by byla jen 3.1 %. Přikloníme se
tedy k závěru, že nulová hypotéza ve skutečnosti neplatí. Jinak řečeno, takový výsledek je tak málo pravděpodobný,
že už nevěříme, že by šlo o náhodu.

Pravděpodobnost pozorovaného výsledku (nebo případně ještě více extrémního) označujeme jako p-hodnota. Obecně platí
pravidlo: **Je-li nižší než hladina významnosti, zamítáme nulovou hypotézu, v opačném případě ji nezamítáme.**

### p-hodnota

Klíčovým termínem pro testování je **p-hodnota**. Ta nám říká, jaká je pravděpodobnost *pozorovaného či ještě
více extrémního výsledku* za *předpokladu platnosti nulové hypotézy*.

p-hodnotami byly hodnoty, které jsme zobrazovali na posledním grafu. Ty nám říkaly, jaká je
pravděpodobnost pádu mince na jednu stranu za předpokladu, že kouzelník žádné nadpřirozené schopnosti nemá, tj.
za *předpokladu platnosti nulové hypotézy*.

V případě, že mince padla při všech hodech na líc, není žádný *více extrémní* výsledek možný. Uvažujme nyní například,
že kouzelník hodil mincí desetkrát a ta padla na líc devětkrát. To stále může být v souladu s alternativní hypotézou,
protože kouzelník tvrdí, že dokáže, aby líc padala *častěji*, nikoli aby padala *vždy*.

Zjistěme nejprve pravděpodobnost tohoto výsledku.

```python
print(binom.pmf(k=9, n=10, p=0.5))
```

V tomto případě se ale *nejedná* o p-hodnotu, protože existuje ještě jeden více extrémní výsledek, a tím je, že
by líc padla ve všech deseti hodech. Abychom získali p-hodnotu podle dané definice, musíme obě pravděpodobnosti
sečíst.

```python
print(binom.pmf(k=9, n=10, p=0.5) + binom.pmf(k=10, n=10, p=0.5))
```

p-hodnota je tedy přibližně 1.07 %. Platí následující pravidlo: **Je-li p-hodnota nižší než hladina významnosti,
zamítáme nulovou hypotézu.** V opačném případě ji nezamítáme.

V našem konkrétním pokusu bychom na hladině významnosti 5 % nulovou hypotézu zamítli, na hladině významnosti
1 % však nikoli.

### Oboustranný test

Uvažujme nyní jiného kouzelníka, který tvrdí, že díky jeho schopnostem bude mince častěji dopadat na jednu stranu,
neví však, jestli to bude rub nebo líc. Nulová hypotéza zůstává beze změny, alternativní hypotéza je však jiná,
protože připouští možnost častějšího pádu na obě strany.

- Nulová hypotéza: Mince dopadá na líc s pravděpodobností 50 %.
- Alternativní hypotéza: Mince dopadá častěji na jednu ze stran.

Uvažujme, že kouzelník hodil mincí pětkrát a ta ve všech případech dopadla na rub. Existuje ještě jedna "stejně
extrémní" varianta — pád ve všech případech na líc. Abychom získali p-hodnotu, sečteme pravděpodobnosti obou
extrémních variant.

```python
print(binom.pmf(k=0, n=5, p=0.5) + binom.pmf(k=5, n=5, p=0.5))
```

Výsledek je 0.0625. V tomto případě se jednalo o **oboustranný test**, v předchozím případě o **jednostranný test**.

### Statistické testy

Statistických testů existuje obrovské množství. Správný test vybíráme podle toho, jaké chceme ověřit hypotézy
a jaká máme k dispozici data.

Přehled testů, které jsou k dispozici v modulu `scipy`, je k dispozici
[v dokumentaci](https://docs.scipy.org/doc/scipy/reference/stats.html#statistical-tests).

#### Počet datových souborů

Testy dělíme na jednovýběrové, dvouvýběrové a vícevýběrové. Výběrem rozumíme skupinu dat. Uvažujme, že máme data
o vzdálenosti, kterou uběhnou studenti a studentky za 12 minut (Cooperův test). Máme následující možnosti:

- Pokud považujeme všechna data za jeden soubor a testujeme hypotézu, že studenti v průměru uběhnou více
než 2.2 kilometru, použijeme jednovýběrový test.
- Pokud bychom testovali hypotézu, že studenti sportovních gymnázií v průměru uběhnou delší vzdálenost než studenti
ostatních škol, použijeme dvouvýběrový test.
- Pokud bychom měli data o čtyřech středních školách a chtěli rozhodnout, zda mezi nimi existují rozdíly,
použijeme vícevýběrový test.

#### Testovaný ukazatel

V předchozích příkladech jsme porovnávali průměrnou vzdálenost — použijeme testy orientované na průměr. Pokud bychom
chtěli porovnat různorodost skupin, použijeme test orientovaný na variabilitu. Pokud nás zajímá pravděpodobnostní
rozdělení dat, použijeme test shody s pravděpodobnostním rozdělením. Testovat můžeme i nezávislost mezi dvěma
veličinami, např. mezi pohlavím a preferovaným typem nápoje.

#### Předpoklady

Některé statistické testy mají striktní předpoklady, na jaké data je možné je použít. Například některé testy na
průměr vyžadují, aby data měla normální rozdělení. Takové testy obecně označujeme za **parametrické**. Naopak
**neparametrické** testy mají obecnější předpoklady.

### Příklady testů

V souboru `student-mat.csv` máme data o výsledcích testů z matematiky ze dvou portugalských středních škol
Gabriel Pereira ("GP") a Mousinho da Silveira ("MS"). Data jsou více popsaná
[zde](https://www.kaggle.com/uciml/student-alcohol-consumption).

```python
import pandas

data = pandas.read_csv("student-mat.csv")
data.head()
```

Školu, kterou student nebo studentka navštěvuje, vidíme ve sloupci `school`. Dále máme v testu počty bodů studentů z
matematiky na konci trimestru. Uvažujme, že chceme porovnat průměrný výsledek testu na konci prvního trimetru
u jednotlivých škol.

Hypotézy budou následující:
- H0: Průměrný počet bodů je stejný pro obě školy.
- H1: Průměrný počet bodů je různý pro obě školy.

- Nemáme důvod se domnívat, že by data měla normální rozdělení, použijeme tedy neparametrický test.
- Máme porovnat dva výběry, proto použijeme dvouvýběrový test.
- Chceme porovnat průměr dat, použijeme tedy test na porovnání průměru.

Testem, který by se k tomu hodil, je [Mann–Whitney U test](https://en.wikipedia.org/wiki/Mann%E2%80%93Whitney_U_test).

```python
from scipy.stats import mannwhitneyu

x = data[data["school"] == "GP"]["G1"]
y = data[data["school"] == "MS"]["G1"]
mannwhitneyu(x, y)
```

Funkce vrací p-hodnotu a hodnotu statistiky. Pokud bychom uvažovali hladinu významnosti 5 %, nulovou hypotézu bychom
nezamítli.

Zkusme nyní rozdělit studenty na skupiny podle toho, jestli mají zájem pokračovat ve studiu (sloupec `higher`).

Hypotézy budou následující:
- H0: Průměrný počet bodů je stejný pro obě skupiny.
- H1: Průměrný počet bodů je vyšší u studentů, kteří mají zájem se dále vzdělávat.

```python
from scipy.stats import mannwhitneyu

data = pandas.read_csv("student-mat.csv")
x = data[data["higher"] == "yes"]["G1"]
y = data[data["higher"] == "no"]["G1"]
mannwhitneyu(x, y, alternative="greater")
```

p-hodnota testu je velmi nízká a pro hladinu významnosti 5 % bychom nulovou hypotézu zamítli. Tvrdíme tedy, že
studenti, kteří mají zájem pokračovat ve studiu, získali v průměru vyšší počet bodů.

Jako poslední příklad si ukážeme chi-kvadrát test nezávislosti, který testuje, zda existuje vztah mezi dvěma
kategoriálními proměnnými. Zkusme zjistit, zda existuje závislost mezi důvodem výběru školy (`reason`) a
týdenním počtem hodin studia (`studytime`).

```python
from scipy.stats import chi2_contingency

data = pandas.read_csv("student-mat.csv")
obs = data.pivot_table(index="reason", columns="studytime", aggfunc=len, values="school", fill_value=0)
g, p, dof, expctd = chi2_contingency(obs)
print(p)
```

p-hodnota je přibližně 0.0012, což je výrazně méně než 0.05. Nulovou hypotézu o nezávislosti zamítáme — mezi
důvodem výběru školy a intenzitou studia tedy existuje statisticky významná závislost.
