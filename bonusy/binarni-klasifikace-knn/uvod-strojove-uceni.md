## Úvod do strojového učení a KNN

### Úvod do strojového učení

Strojové učení (angl. _machine learning_) pod sebou sdružuje všechny systémy, které se učí z dat bez toho, aby potřebovali explicitní instrukce nebo pravidla. Informace, které se tyto systémy naučí z dat, umí pak generalizovat na nová, neznámá data. 

Když z analýzy dat našeho e-shopu zjistíme, že mladí studenti z Brna nakupují často boty přes internet a nabídneme proto všem budoucím uživatelům z Brna s věkem pod 26 let slevu na online nákup bot, tak to není strojové učení. Jedná se o jednoduché pravidlo, které jsme odvodili sami (i když třeba s využitím nějakých nástrojů datové analýzy).

Když náš chatbot telefonního operátora při detekování slova "tarif" vždy odpoví tak, že pošle odkaz na přehled všech tarifů, tak to také není strojové učení. Zase se jedná jen o nějaké pravidlo. 

V oblasti strojového učení panuje mírné zmatení pojmů, které se v různých odvětvích vykládají různě. Neberte proto definice z tohoto kurzu jako úplně závazné :-)

Strojové učení je podoblast umělé inteligence. Pojem umělé inteligence je často zaměňován právě za strojové učení. Můžete se také setkat s pojmem _deep learning_ nebo s pojmem _hluboké neuronové sítě_. Ty označují podskupinu algoritmů strojového učení, které vzrostly v popularitě a zájmu veřejnosti i akademiků z důvodu výrazně lepších a zobecnitelnějších výsledků než takzvané klasické strojové učení. Vyžadují ale velký výpočetní výkon (větší než má běžný osobní počítač), a také větší objem dat.

V tomto kurzu se "hlubokému" strojovému učení věnovat nebudeme. Pro pochopení jak neuronová síť funguje bychom potřebovali i podstatnou část matematiky, konkrétně lineární algebru. Základní principy jsou ale stejné jako pro klasické strojové učení, a to je důležité si uvědomit. 

::fig[]{src=assets/ai-ml-dl-smaller.png}

#### Příklady využití strojového učení

- Detekce podvodů v bankovnictví: Strojové učení se používá k analýze transakcí a určení, zda jsou některé z nich podezřelé a mohou představovat podvod.
- Doporučovací systémy: Firmy jako Netflix a Amazon používají strojové učení k doporučení filmů, televizních pořadů nebo produktů založených na chování uživatelů.
- Rozpoznávání obličeje: Strojové učení se používá k vytvoření systémů rozpoznávání obličeje, které mohou být použity v mobilních telefonech pro odemykání nebo v bezpečnostních systémech.
- Autonomní vozidla: Strojové učení je klíčové pro vývoj autonomních vozidel, které se učí navigovat na základě analýzy obrazu a dalších senzorických dat.
- Prediktivní údržba: Výrobní společnosti mohou používat strojové učení k předvídání, kdy bude pravděpodobně potřeba údržba strojů a zařízení, což umožňuje minimalizovat dobu odstávky.
- Zdravotnictví a medicína: Strojové učení se využívá pro diagnostiku chorob, předvídání výsledků léčby a vývoj nových léků. Může také analyzovat obrazové data, například rentgenové snímky nebo snímky z magnetické rezonance.
- Zpracování přirozeného jazyka (NLP): Strojové učení se používá pro automatický překlad jazyků, generování textu, rozpoznávání hlasu a další úkoly spojené se zpracováním a generováním přirozeného jazyka.
- Bezpečnostní systémy: Strojové učení se také využívá k detekci neobvyklých aktivit nebo útoků na počítačové systémy a sítě.
- Energetika: Strojové učení může pomoci optimalizovat výrobu a distribuci energie, předvídat spotřebu energie a výkon solárních panelů.
- Marketing a prodej: Strojové učení se využívá pro segmentaci zákazníků, předpověď chování zákazníků a personalizaci reklamních kampaní.
- Zemědělství: Strojové učení může pomoci předpovědět úrodu, optimalizovat zavlažování a detekovat nemoci rostlin.
- Hudba a umění: Algoritmy strojového učení mohou generovat hudbu nebo umělecká díla a také doporučovat obsah na základě uživatelských preferencí.
- Meteorologie: Strojové učení se může využít k předpovědi počasí a klimatických změn.
- Social media: Strojové učení je hojně využíváno v social mediích pro filtraci a personalizaci obsahu, rozpoznávání a sledování trendů, detekci hate speech, fake news, a další.
- Realitní trh: Strojové učení může být využito k odhadu cen nemovitostí na základě různých faktorů, jako je umístění, velikost, stáří a další.
- Hry: V oblasti herního průmyslu se strojové učení používá k vytváření inteligentních NPC (non-player characters), k návrhu nových úrovní nebo k optimalizaci herních strategií.

#### Obecný postup při využití strojového učení
Pokud chceme nějakou otázku zodpovědět pomocí strojového učení, můžeme následovat obecný postup, který je společný pro všechny algoritmy. Podobný postup jsme měli i u testování hypotéz.

1. V první řadě je potřeba si **definovat, čím se zabýváme, a jaký je problém, který řešíme**. Typicky budeme chtít získat nějaké předpovědi nebo odhady na základě dat. Také je dobré si rozmyslet, jestli vůbec strojové učení potřebujeme. Není náš problém řešitelný nějakým jednodušším způsobem?
1. Následně potřebujeme **získat data**, s pomocí kterých budeme naší úlohu řešit. Data musíme takzvaně připravit. Je potřeba se rozhodnout, co budeme dělat v případě chybějících hodnot, zamyslet se nad případnou normalizací dat (co když jsou některé záznamy o vzdálenosti v kilometrech a jiné v mílích?) a také data náhodně rozdělit na trénovací a testovací sadu.
1. Když máme připravená data, můžeme si **vybrat jeden nebo více algoritmů** strojového učení. Algoritmus vybereme podle typu úlohy a charakteru dat.
1. Teď už můžeme takzvaně **"natrénovat"** (*train*) náš model. Spustíme algoritmus na sadu trénovacích dat a výsledkem je natrénovaný nebo naučený model.
1. Na základě validační sady dat **ověříme**, jak dobře umí náš model předvídat na datech, která nezná. 
1. Podle výsledků evaluace můžeme **upravit některé parametry modelu**. V tom případě jdeme zpět na krok trénování modelu, a takto postupujeme v cyklu, dokud nejsme s výsledky spokojení.
1. Na závěr spustíme náš model na **závěrečnou testovací sadu dat**. Tuto sadu algoritmus neviděl při trénování, ale ani jsme jí nepoužívali na upravování parametrů. Výsledky, které dostaneme pro tuto sadu dat by měly být ty, které napíšeme do akademického článku, do pracovní prezentace, nebo do zprávy o našem projektu.

Pro účely kurzu budeme často využívat jen dvě sady dat: trénovací a testovací.

::fig[]{src=assets/ml-schema-postupu.png}

#### Supervised learning

Existuje velká třída úloh, kterým se říká "supervised learning", nebo "s učitelem". Existují dvě velké podtřídy těchto úloh.

##### Klasifikační úlohy

Cílem klasifikační úlohy (*classification*) je rozdělit data do dvou či více skupin.

Příklady klasifikačních úloh jsou:

- Detekce spamu: Model strojového učení je natrénován na detekci spamových e-mailů na základě předchozích zkušeností. E-maily mohou být klasifikovány jako "spam" nebo "ne-spam".
- Diagnostika nemocí: Na základě různých diagnostických testů může být model strojového učení použit k diagnostice různých typů nemocí. Například na základě snímků z magnetické rezonance mohou být pacienti klasifikováni jako "má rakovinu" nebo "nemá rakovinu".
- Rozpoznávání ručně psaných číslic: Klasickým příkladem je MNIST dataset ručně psaných číslic, kde úkolem je klasifikovat obrázky do 10 tříd odpovídajících číslicím 0 až 9.
- Predikce úvěrové bonity: Model může být vytvořen pro klasifikaci jednotlivců do kategorií "dobrý úvěrový riziko" a "špatné úvěrové riziko" na základě finančních a osobních údajů.
- Klasifikace genů: V bioinformatice může být model použit k třídění genů do různých tříd na základě sekvence DNA.
- Detekce sociálních botů: Na sociálních médiích může být model použit k rozpoznání účtů, které jsou pravděpodobně ovládány boty, na základě jejich chování a vzorců aktivity.

##### Regresní úlohy

Cílem regresní (*regression*) úlohy je predikce číselné hodnoty. Strojové učení nabízí algoritmy podobné klasické regresi i řadu jiných.

Příklady regresních úloh jsou:

- Predikce spotřeby energie: Model může být natrénován na předpovědění spotřeby energie budov nebo celých měst na základě historických dat a různých vlivů, jako je počasí, doba dne, atd.
- Predikce akciových cen: I když je tato úloha velmi obtížná kvůli nestabilitě trhů, modely strojového učení mohou být použity pro předpovědění trendů akciových cen.
- Predikce délky hospitalizace: V zdravotnictví může být model použit k předpovědění doby, po kterou bude pacient potřebovat hospitalizaci.
- Predikce teploty: Modely mohou být využity pro předpovědění teploty v konkrétních časových úsecích nebo lokalitách na základě historických dat a vzorců.

```python
import pandas as pd
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsClassifier
import matplotlib.pyplot as plt
from sklearn.metrics import ConfusionMatrixDisplay
from sklearn.metrics import accuracy_score, precision_score, recall_score
```

```python
import warnings

warnings.filterwarnings("ignore")
```

#### Popis importů

* `StandardScaler` - objekt pro normalizaci dat, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)
* `train_test_split` - funkce pro rozdělení dat na trénovací a testovací, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html)
* `KNeighborsClassifier` - klasifikátor, používá algoritmus K Nearest Neigbors, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsClassifier.html)
* `ConfusionMatrixDisplay` - vizualizace matice záměn, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.ConfusionMatrixDisplay.html)
* `accuracy_score`, `precision_score` a `recall_score` - funkce pro vyhodnocení výsledků modelu, dokumentace je [zde](https://scikit-learn.org/stable/modules/classes.html#classification-metrics)

### Strojové učení v Pythonu

V našem kurzu budeme pracovat s modulem [`scikit-learn`](https://scikit-learn.org/stable/). Tento modul je velice často využíván v takzvaném klasickém strojovém učení (tj. strojové učení, které _nevyužívá_ hlubokých neuronových sítí). Používá se jak pro výuku (má k dispozici [velké množství tutoriálů a příkladů](https://scikit-learn.org/stable/auto_examples/index.html)), tak pro praktické využití v akademickém nebo soukromém sektoru.

Data si můžeš stáhnout [zde](assets/wine.csv).

Zdroj dat: [https://archive.ics.uci.edu/ml/datasets/wine+quality](https://archive.ics.uci.edu/ml/datasets/wine+quality)

#### Definice problému a data

Máme data o chemickém složení vína a ohodnocení vín do kategorií dobré a špatné. Naším úkolem je vytvořit model, který bude klasifikovat víno na základě jeho složení do jedné ze dvou těchto skupin.

```python
data = pd.read_csv("wine.csv")
data.head()
```

Konkrétně víme následující údaje:

- fixed acidity	(stálá kyselost),
- volatile acidity (těkavá kyselost),
- citric acid (kyselina citronová),
- residual sugar (zbytkový cukr),
- chlorides	(chloridy),
- free sulfur dioxide (volný oxid siřičitý),
- total sulfur dioxide (celkový oxid siřičitý),
- density (hustota),
- pH (pH),
- sulphates	(sírany),
- alcohol (alkohol),
- quality (kvalita).

```python
data.value_counts("quality")
```

Rozdělíme si vstupní proměnné a cílovou hodnotu:

```python
X = data.drop(columns=["quality"])
y = data["quality"]
```

Data si teď rozdělíme na trénovací a testovací sadu. Není to tak těžké udělat přímo pomocí pandasu (například mohli bychom data zpřeházet pomocí metody `.sample()`, a pak si vzít prvních x řádek pro testovací sadu a zbytek pro trénovací), ale modul `scikit-learn` má také pěknou metodu, kterou si ukážeme.

```python
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.3, random_state=42
)
```

Ještě nás čeká jeden důležitý krok. Všimněme si, že data nejsou normalizovaná. Například sloupec `total sulfur dioxide` se pohybuje řádově ve desítkách, zatímco `ph` se pohybuje v jednotkách.
Data normalizujeme metodou z-scores. Odečteme průměr a vydělíme standardní odchylkou:

$$z=\frac{x - μ}{σ}$$

Mohli bychom opět využít čistě pandas (například bychom si mohli napsat funkci, která data normalizuje, a pomocí `apply` jí uplatnit na každý sloupec). Ukážeme si ale jak postupovat pomocí knihovny `sklearn`.

```python
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)
```

Proč napřed voláme metodu `fit_transform` na trénovací data a pak jen `transform` na testovací? `fit_transform` vlastně dělá dvě věci: napřed spočítá pro každý sloupec průměr a směrodatnou odchylku. Pak trénovací data normalizuje. Pro testovací data už nepočítá nové hodnoty průměru a odchylky, ale použije ty z trénovacích dat.

Jedná se o standardní postup: snažíme se maximálně zachovat odstup trénovacích dat od testovacích. Kdybychom využili testovací data pro spočítání průměru, už tím zprostředkováváme nějaké informace, které při trénování modelu nemáme znát.

Naše data už teď nejsou ve formátu `pandas.DataFrame`, ale jsou uložené jako matice knihovny  `numpy`.

```python
X_train
```

#### Výběr algoritmu
Pro začátek zvolíme jednoduchý algoritmus, který pracuje jen s jedním parametrem. Jmenuje se "K Nearest Neighbors", tedy se jedná o algoritmus, který pracuje s K nejbližšími "sousedy".

Tento algoritmus využívá úvahy, že stačí uložit si trénovací data, a pak každý nový testovací záznam určitým způsobem porovnat s nejbližšími sousedy v trénovacích datech. U trénovacích dat známe jejich cílovou hodnotu, takže na základě té rozhodneme i o hodnotě testovacího záznamu. Algoritmus se dá dobře znázornit i vizuálně, když si představíme, že pracujeme se dvěma vstupními proměnnými (osy x a y), a barva znázorňuje cílovou hodnotu. Červená tečka je nový testovací záznam, u kterého se rozhodujeme, jaká bude jeho hodnota.

Pro hodnotu parametru `k = 1` se prostě podíváme na nejbližšího souseda a přiřadíme stejnou hodnotu (zelenou) i našemu testovacímu záznamu.

Pro hodnotu `k = 2` se dostaneme do složité situace - není možné určit, jaká má být výsledná hodnota, protože jeden soused je fialový a druhý zelený. Z tohoto důvodu se typicky nepoužívají sudé hodnoty pro parametr `k`.

Pro hodnotu`k = 7` hlasování sousedů doapdne v prospěch fialové cílové hodnoty.

::fig[]{src=assets/knn.png}

#### Trénování modelu

```python
clf = KNeighborsClassifier()
clf.fit(X_train, y_train)
```

#### Vyhodnocení modelu

Získáme predikce našeho modelu a porovnáme je se skutečností.

```python
y_pred = clf.predict(X_test)
```

Kvalitu modelu vyhodnotíme s využitím Matice záměn (Confusion Matrix).

```python
ConfusionMatrixDisplay.from_estimator(
    clf,
    X_test,
    y_test,
)
```
::fig[]{src=assets/plot_01.png}

Existuje mnoho metrik, podle kterých můžeme měřit úspěšnost našeho modelu. Většina se dá odvodit z výsledků, které nám znázorňuje barevná matice. Každý ze čtverců si můžeme označit zkratkou a interpretovat, co znamená.

| Terminologie      | Popis | Počet |
| ----------- | ----------- | -----------: |
| True Positives (**TP**)  | Vína, která jsme správně klasifikovali jako dobrá.      | 209 |
| True Negatives (**TN**)   | Vína, která jsme správně označili jako špatná.       | 139 |
| False Positives (**FP**) | Vína, která jsme chybně označili jako dobrá, ve skutečnosti jsou špatná.        | 74 |
| False Negatives (**FN**) | Vína, která jsme chybně označili jako špatná, ve skutečnosti jsou dobrá.        | 58 |

Jak teď spočítat úspěšnost modelu? Nabízí se několik metrik:

* _Accuracy_: Poměr správně určených záznamů oproti celku.
  * $A = \frac{TP+TN}{TP+TN+FP+FN}$
* _Precision_: Tato metrika **penalizuje** označení **špatného vína za dobré**. Čím více špatných vín označíme za dobrá, tím má metrika menší hodnotu. Metrika nepočítá s tím, kolik dobrých vín jsme označili za špatná.
  * $P = \frac{TP}{TP+FP}$
* _Recall_: Tato metrika **penalizuje** označení **dobrého vína za špatné**. Čím více dobrých vín označíme za špatná, tím má metrika menší hodnotu. Metrika nepočítá s tím, kolik špatných vín jsme označili za dobrá.
  * $R = \frac{TP}{TP+FN}$
* _F1 Score_: Metrika která zohlední jak Precision, tak Recall.

```python
accuracy_score(y_test, y_pred)
```

```python
precision_score(y_test, y_pred, pos_label="good")
```

```python
209 / (209 + 74)
```

```python
recall_score(y_test, y_pred, pos_label="good")
```

```python
209 / (209 + 58)
```

#### Úprava parametrů modelu

Máme tedy nějakou základní představu o tom, jak se chová náš model. Zkusíme upravit parametr `k`, který říká, na kolik "sousedů" se podíváme, abychom rozhodli o cílové hodnotě.

```python
ks = range(1, 31, 2)
accuracy_scores = []

for k in ks:
    clf = KNeighborsClassifier(n_neighbors=k)
    clf.fit(X_train, y_train)
    y_pred = clf.predict(X_test)
    accuracy_scores.append(accuracy_score(y_test, y_pred))
```

```python
plt.plot(ks, accuracy_scores)
```
::fig[]{src=assets/plot_02.png}

#### Závěrečná predikce

Nyní provedeme finální predikci

```python
clf = KNeighborsClassifier(n_neighbors=1)
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)
accuracy_score(y_test, y_pred)
```

#### Spojení predikce s původními daty

Predikci můžeme spojit s původními daty, a to například převodem dat na pandas tabulku.

```python
data_test = pd.DataFrame(X_test, columns=X.columns)
data_test["quality_prediction"] = y_pred
data_test["quality_actual"] = y_test.reset_index(drop=True)
data_test.head()
```

Pro každý řádek si můžeme určit, jestli byla hodnota predikována správně.

```python
def compare_values(row):
    if row["quality_prediction"] == row["quality_actual"]:
        return "correct"
    else:
        return "incorrect"


data_test["result"] = data_test.apply(compare_values, axis=1)
data_test.head()
```

Níže jsou vybraná vína, pro které predikce dopadla nesprávně.

```python
data_test_incorrect = data_test[data_test["result"] == "incorrect"]
data_test_incorrect.head()
```

#### Měření vzdálenosti

Jak je ale spočítána vzdálenost jednotlivých pozorování?

Ze školy si možná vzpomeneš na Euklidovskou vzdálenost, která počítá vzdálenost dvou bodů ve dvourozměrném prostoru.

::fig[]{src=assets/1_ZrwEraj9S-u_KOWdKWc8sQ.png}

Vzoreček, pomocí kterého je vzdálenost definována, je na obrázku. Tento vzoreček jde ale převést i do vícerozměrného prostoru. Napří pro třírozměrný prostor by byl:

$Vzdálenost (A, B) = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2 + (z_2 - z_1)^2}$

Podobně můžeme přidat čtvrtý rozdměr, pátý rozměr atd. Tímto způsobem počítá vzdálenosti `scikit-learn` ve výchozím nastavení. Konkrétně používá Minkowskiho vzdálenost, která je trochu obecnější než Euklidovská. Místo druhé mocniny a druhé odmocniny umožňuje použít i jinou mocninu a odmocninu (např. třetí, čtvrtou atd.). Mocninu, kterou chceme použít, nastavíme pomocí parametru `p`. Pokud parametr nezadáme, je použita výchozí hodnota, což je 2.

### Logistická regrese

Algoritmů na binární klasifikaci existuje velké množství. Dalším z nich je logistická regrese (*logistic regression*). Zatímco v lineární regresi jsme pracovali s vysvětlovanou proměnnou jako běžným číslem (neuvažovala jsme nějaké omezení rozsahu), logistická regrese predikuje pravděpodobnost, s jako pozorování patří do každých tříd.

Pro použití logistické regrese na naše data použijeme následující kód. Napříkald níže vidíme, že pro první z testovacích vín model predikuje, že je dobré a pravděpodobností 35.2 % a že je špatné s pravděpodobností 64.9 %. U druhého vína je pravděpodobnost, že je dobré, pouze 15.5 % a špatné je s pravděpodobností 84.5 %.

```python
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
y_pred = clf.predict_proba(X_test)
y_pred
```

Rozdělení do tříd získáme pomocí metody `predict()`. Je vždy použita třída, která má vyšší pravděpodobnost.

```python
y_pred = clf.predict(X_test)
y_pred
```

Níže je pak matice záměn.

```python
ConfusionMatrixDisplay.from_estimator(
    clf,
    X_test,
    y_test,
)
```
::fig[]{src=assets/plot_03.png}

Oba algoritmy můžeme poronvat s využitím libovolné metriky, využijme např. *accuracy*.

```python
accuracy_score(y_test, y_pred)
```

#### Další zdroje

- [Artificial Intelligence (AI) vs. Machine Learning](https://ai.engineering.columbia.edu/ai-vs-machine-learning/)
- [K-Nearest Neighbors (KNN) - Intro](https://prof-frenzel.medium.com/dear-friends-854ba66361d7)
- [K Nearest Neighbours (KNN): One of the Earliest ML Algorithm](https://medium.com/@naemazam/k-nearest-neighbours-knn-one-of-the-earliest-ml-algorithm-ab45467fa6fb)
- [What and why behind fit_transform() and transform() in scikit-learn!](https://towardsdatascience.com/what-and-why-behind-fit-transform-vs-transform-in-scikit-learn-78f915cf96fe)
- [kNN Classifier Tutorial](https://www.kaggle.com/code/prashant111/knn-classifier-tutorial)
- [Step by Step Diabetes Classification-KNN-detailed](https://www.kaggle.com/code/shrutimechlearn/step-by-step-diabetes-classification-knn-detailed)
- [Support Vector Classifiers (SVCs): A Comprehensive Guide](https://medium.com/ai-in-plain-english/support-vector-classifiers-svcs-a-comprehensive-guide-a9115a99a94f) (z placeného TowardsDataScience)

::exc[excs/nba]

::exc[excs/bonus-vino]
