## Zpracování přirozeného jazyka

```python
import pandas

from sklearn.svm import LinearSVC
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import CountVectorizer, TfidfVectorizer
from sklearn.metrics import ConfusionMatrixDisplay, accuracy_score
```

### Popis importů

* `LinearSVC` je klasifikátor používající lineární verzi algoritmu Support Vector Machine a One-to-Rest postup pro klasifikaci do více tříd, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.svm.LinearSVC.html)
* `KNeighborsClassifier` - klasifikátor, používá algoritmus K Nearest Neigbors, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsClassifier.html)
* `train_test_split` - funkce pro rozdělení dat na trénovací a testovací, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html)
* `CountVectorizer` provádí konverzi textů na číselné pole, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html)
* `TfidfVectorizer` provádí konverzi textů na číselné pole s využitím algoritmu TF-IDF, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html)
* `ConfusionMatrixDisplay` - vizualizace matice záměn, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.ConfusionMatrixDisplay.html)
* `accuracy_score` - funkce pro vyhodnocení výsledků modelu, dokumentace je [zde](https://scikit-learn.org/stable/modules/classes.html#classification-metrics)

### Co to je NLP

Práce s přirozeným jazykem je odlišná od práce s jinými daty, a proto si zaslouží vlastní skupinu technik a algoritmů - NLP (Natural Language Processing). Ne všechny úlohy NLP se musí řešit pomocí strojového učení, ale v dnešní době tomu tak většinou je. Příklady úloh, které spadají do NLP jsou například:

- Strojový překlad (text v angličtině => text v češtině)
- Rozpoznávání řeči (řeč v češtině => text v češtině)
- Syntéza řeči (text v češtině > řeč v češtině)
- Klasifikace textu

Tyto úlohy určitě znáte i z běžného života. Další úlohy, které řeší NLP, a nacházejí se často "pod kapotou" jiných systémů, jsou například:

- Rozpoznávání pojmenovaných entit (NER, Named Entity Recognition): Pojmenované entity jsou výrazy, které označují jména, místa, data, názvy, ... Jaké pojmenované entity bychom mohli označit například ve větě _Král Karel si v Londýně včera zašel do kavárny Starbucks._ ?
- Určování slovních druhů (POS tagging, Part-of-speech tagging)
- Zjednodušení textu, shrnutí textu
- Určení významu slova (WSD, Word Sense Disambiguation): Při dvou významech slova _kolej_ (studentské ubytování, železniční dráha), který z nich je zachycený větou _Včera proběhl na koleji večírek._ ?

### Reprezentace textových dat

Naše datasety doposud obsahovaly proměnné, a datové body (pozorování) reprezentované pomocí těchto proměnných. Například u vzorku vody jsme dostali proměnné na základě chemického rozboru. U textových dat většinou dostaneme syrovější podobu dat, nikoliv proměnné. Jako kdybychom dostali vzorky vody, a sami museli provést chemickou analýzu.

Obecně řečeno, i v případě textu budeme jednotlivá pozorování nebo datové body reprezentovat pomocí číselných hodnot vstupních proměnných. Co ale budou tyto proměnné, označené v obrázku jako barvy, reprezentovat?

K reprezentování dat můžeme využít `CountVectorizer`. Ten vytvoří matici, kde každé slovo je reprezentováno jedním sloupcem a každý text ve vstupních datech jedním řádkem. Jeho fungování si nejprve ukážeme na jednoduchých datech, kde 4 uživatelé a uživatelky diskusního fóra vyjádřili své názory na jazyk Python. Celkem máme 4 diskusní příspěvky. Naším úkolem by bylo provést analýzu textu, která zjistí, kolik uživatelů (uživatelek) má k Pythonu kladný vztah a kolik záporný. Tento typ úloh je často označován jako analýza sentimentu (*sentiment analysis*).

Níže vidíme pole, které má 8 sloupců (v datech je totiž 8 unikátních slov) a 4 řádky (v datech jsou 4 řetězce). Pomocí metody `vec.get_feature_names_out()` si můžeme zobrazit popisky sloupců.

```python
X = ["Python is great", "I like Python", "Python is the best language", "I hate Python"]
vec = CountVectorizer()
X = vec.fit_transform(X)
X = X.toarray()
X
```

```python
vec.get_feature_names_out()
```

Pro větší přehlednost si můžeme převést data do pandas tabulky. Vidíme například, že slovo `great` má hodnotu `1` pro řádek `0` (dokument na nulté pozici při počítání od 0). V datech vidíme, že slovo `great` se v něm skutečně vyskytuje. Ve všech ostatních příspěvcích se toto slovo nevyskytuje, proto mají ostatní řádky v tom sloupci hodnotu `0`.

```python
df = pandas.DataFrame(X, columns=vec.get_feature_names_out())
df
```

Pokud se na slova podíváme, dokázali bychom je rozdělit na několik skupin:

- slova, která naznačují kladný vztah (best, great, v tomto případě i slovo like, ale u něj to tak nemusí být vždy),
- slova, která naznačují negativní vztah (hate),
- slova, která nenaznačují ani jedno (is, language, python, the).

Obecně bychom pak mohli říci, že vysoký počet "pozitivní slov" vede spíše k tomu, že celý komentář je zamýšlen pozitivně, a vysoký počet "negativních slov" vede k tomu, že celý komentář je negativní. Tento postup určitě není dokonalý (např. nerozpozná sarkasmus), ale umožní nám využít algoritmy, které už známe.

Uvažujme dále, že v jazyce je obrovské množství slov a my je nechceme ručně třídit. Na internetu ale můžeme najít datové sady, které obsahují nějaký text a k tomu označení, zda je text celkově pozitivní nebo negativní. Například u recenzí často lidé vyplňují textový komentář i hodnocení na nějaké škále (např. 1 až 5 hvězd). Můžeme pak použít supervised learning (učení s učitelem) a "ohodnotit" jednotlivá slova (nebo skupiny slov) jako pozitivní nebo negativní.

Analýza sentimentu ale není jediná úloha, která funguje na tomto principu. Obecně můžeme třídit dokumenty do skupin například v systémech uživatelské podpory (stěžuje si uživatel na problémy s internetem, chce levnější tarif, chce si aktivovat roaming?), při třídění článků do rubrik, třídění zboží do kategorií atd.

Pojďme si nyní načíst dataset, se [kterým budeme dneska pracovat](movies.csv). Jedná se o databázi popisů filmů ze serveru IMDB. K dispozici máme název filmu, jeho žánr (to bude naše výstupní proměnná), a text popisku filmu. Text popisku budeme chtít převést na naše vstupní proměnné. Pokud bychom dobře ohodnotili jednotlivé slova, můžeme pak podle jejich počtu v dokumentu odhadnout, jaké je celkové vyznění zprávy.

Naším úkolem bude odhadnout žánr filmu, ke kterému popis patří. V datech máme název filmu, žánr a textový popis. Naším úkolem bude vytvořit model, který dokáže zařadit film do žánru, i když oficiální informaci od tvůrců filmu nemáme.

```python
data = pandas.read_csv("movies.csv")
data.head()
```

Rozdělíme data na vstupní a výstupní proměnnou a poté na trénovací a testovací data.

```python
X = data["text"]
y = data["genre"]

X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y, random_state=0)
```

Musíme myslet na to, že rozložení jednotlivých skupin je velmi nerovnoměrné, například komedií je mnohem více než sci-fi filmů. Parametry strafify nám zařizuje, že funkce `train_test_split` zachová poměr jednotlivých skupin v testovacích i trénovacích datech stejný.

```python
data.groupby("genre").size().plot(kind="bar")
```
::fig[]{src=assets/plot_01.png}

Jako první formu reprezentace popisků vyzkoušíme jednotlivá slova a jejich počty. V podstatě vytvoříme slovní zásobu, která bude obsahovat všechna slova, co se v našich trénovacích datech objeví. Jedno slovo bude jedna proměnná a hodnota proměnné bude počet, kolikrát se slovo v dokumentu (zde popisku filmu) objeví. Opět použijeme `CountVectorizer`.

```python
vec = CountVectorizer()
X_train = vec.fit_transform(X_train)
X_test = vec.transform(X_test)
```

Jak teď naše data vypadají?

```python
X_train.toarray()
```

Podívejme se na rozměry tabulky.

```python
X_train.toarray().shape
```

```python
vec.get_feature_names_out()
```

Vidíme, že data mají cca 49 tisíc unikátních slov. Bylo by pro nás jako pro lidi opravdu příliš pracné tato slova ručně projít a rozdělit na skupiny, jako jsme to provedli u imaginárních dat s hodnoceními jazyka Python. Můžeme ale využít některý z algoritmů supervised learning. Ten zařadí film do žánru v závislosti na tom, do jakých žánrů patří filmy s popisy, které obsahuje podobná slova.

Pojďme tedy zkusit tyto vstupní proměnné předat klasifikačnímu algoritmu K Nearest Neighbours. Uvažujme například 5 nejbližších sousedů, tj. výchozí hodnotu.

```python
clf = KNeighborsClassifier()
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)
```

```python
ConfusionMatrixDisplay.from_estimator(
    clf,
    X_test,
    y_test,
)
```
::fig[]{src=assets/plot_02.png}

```python
accuracy_score(y_test, y_pred)
```

::exc[excs/analyza-sentimentu]

```python
X = ["Python is great", "I like Python", "Python is the best language", "I hate Python"]
vec = TfidfVectorizer()
X = vec.fit_transform(X)
X = X.toarray()
X
```

```python
vec.get_feature_names_out()
```

```python
df = pandas.DataFrame(X, columns=vec.get_feature_names_out())
df
```

Nyní zkusíme přidat parametr `stop_words`. Která slova nám z dat zmizela?

```python
X = ["Python is great", "I like Python", "Python is the best language", "I hate Python"]
vec = TfidfVectorizer(stop_words="english")
X = vec.fit_transform(X)
X = X.toarray()
X
```

```python
vec.get_feature_names_out()
```

```python
df = pandas.DataFrame(X, columns=vec.get_feature_names_out())
df
```

#### Postup výpočtu

Podívejme se na způsob výpočtu. Vraťme se k tabulce, kterou vygeneruje `CountVectorizer`.

```python
X = ["Python is great", "I like Python", "Python is the best language", "I hate Python"]

vec = CountVectorizer(stop_words="english")
X = vec.fit_transform(X)
X = X.toarray()
X
```

Nyní porovnejme matici s tím, co vygeneruje `TfidfVectorizer`. Dále přibyl parametr `smooth_idf`, jehož funkci si ještě objasníme.

```python
X = ["Python is great", "I like Python", "Python is the best language", "I hate Python"]

vec = TfidfVectorizer(stop_words="english", smooth_idf=False)
X = vec.fit_transform(X)
X = X.toarray()
X
```

```python
vec.get_feature_names_out()
```

Pro přehlednost si zobrazíme výsledek jako pandas tabulku (`DataFrame`).

```python
df = pandas.DataFrame(X, columns=vec.get_feature_names_out())
df
```

Vypočítejme si nyní sami hodnoty v tabulce pro první řádek, abychom pochopili, jak `TfidfVectorizer` funguje. Začneme s výrazem great. Postupujeme podle vzorce.

$$
\textrm{idf}(t) = \log \frac{n}{1 + \textrm{df}(t)}
$$

kde $\textrm{df}$ je počlet dokumentů, které obsahují výraz $t$, $n$ je celkový počet dokumentů a výsledkem je $idf$ (inverse document-frequency).

```python
import numpy

n = 4
df_great = 1
idf_great = numpy.log(n / df_great) + 1
idf_great
```

Dále dopočítáme hodnotu ukazatel tf-idf s využitím vzorce

$$
\textrm{tf-idf}(t,d) = \textrm{tf}(t,d) * \textrm{tf}
$$

kde $\textrm{tf}(t,d)$ znamená term-frequency, tj. počet výskytů výrazu $t$, který vidíme v tabulce, kterou nám vygeneroval `CountVectorizer`.

```python
tf_idf_great = 1 * idf_great
tf_idf_great
```

Nyní postupujeme stejně pro výraz Python. Ten se objevuje ve všech 4 dokumentech.

```python
df_python = 4
idf_python = numpy.log(n / df_python) + 1
idf_python
```

```python
tf_idf_python = 1 * idf_python
tf_idf_python
```

Nakonec provedeme normalizaci, kterou získáme jako podíl tf-idf pro dané slovo a odmocninu druhých mocnin tf-idf pro všechna slova. Níže vidíme, že výsledek odpovídá hodnotě pro výraz great v dokumentu 0.

```python
tf_idf_great / numpy.sqrt(tf_idf_great ** 2 + tf_idf_python ** 2)
```

Dále si vyzkoušíme variantu bez parametru `smooth_idf`.

```python
X = ["Python is great", "I like Python", "Python is the best language", "I hate Python"]

vec = TfidfVectorizer(stop_words="english")
X = vec.fit_transform(X)
X = X.toarray()
X
```

```python
df = pandas.DataFrame(X, columns=vec.get_feature_names_out())
df
```

Rozdíl je v tom, že idf počítáme z mírně upraveného vzorce

$$
\textrm{idf}(t) = \log \frac{1 + n}{1 + \textrm{df}(t)} + 1
$$

```python
n = 4
df_great = 1
idf_great = numpy.log((1 + n) / (1 + df_great)) + 1
tf_idf_great = 1 * idf_great
df_python = 4
idf_python = numpy.log((1 + n) / (1 + df_python)) + 1
tf_idf_python = 1 * idf_python
tf_idf_great / numpy.sqrt(tf_idf_great ** 2 + tf_idf_python ** 2)
```

### Vlastní seznam stop words

Pokud nám nevyhovuje výchozí seznam stop words pro daný jazyk, můžeme si vytvořit svůj. To se může hodit pro češtinu nebo třeba v situaci, že se dané slovo vyskytuje často (např. v příspěvcích o Pythonu se bude často opakovat slovo Python).

```python
X = [
    "Python je nejlepší", 
    "Mám rád Python!", 
    "Python je nejlepší jazyk",
    "Nesnáším Python!"
    ]
vec = TfidfVectorizer(stop_words=["je", "mám", "Python"])
X = vec.fit_transform(X)
X = X.toarray()
X
```

```python
vec.get_feature_names_out()
```

```python
df = pandas.DataFrame(X, columns=vec.get_feature_names_out())
df
```

### Klasifikace dokumentů

Vyzoušejme nyní klasifikaci dokumentů (resp. filmů, o kterých dané dokumenty pojednávají). Využít můžeme například algoritmus `KNeighborsClassifier`.

```python
X = data["text"]
y = data["genre"]

X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y, random_state=0)

vec = TfidfVectorizer()
X_train = vec.fit_transform(X_train)
X_test = vec.transform(X_test)

clf = KNeighborsClassifier()
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)

accuracy_score(y_test, y_pred)
```

Porovnáme accuracy s výsledkem stejného algoritmu při využití parametru `stop_words`.

```python
X = data["text"]
y = data["genre"]

X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y, random_state=0)

# Uvažujeme stop_words v angličtině
vec = TfidfVectorizer(stop_words="english")
X_train = vec.fit_transform(X_train)
X_test = vec.transform(X_test)

clf = KNeighborsClassifier()
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)

accuracy_score(y_test, y_pred)
```

Níže si zobrazíme matici záměn. Vidíme například, že náš model označil 228 hororů za komedii a naopak 108 komedií za horor.

```python
ConfusionMatrixDisplay.from_estimator(
    clf,
    X_test,
    y_test,
)
```
::fig[]{src=assets/plot_03.png}

Zkusme nyní přidat i dvojice slov. K tomu slouží parametr `ngram_range`.

```python
X = data["text"]
y = data["genre"]

X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y, random_state=0)

# Uvažujeme stop words v angličtině, samostatná slova a dvojice slov
vec = TfidfVectorizer(stop_words="english", ngram_range=(1, 2))
X_train = vec.fit_transform(X_train)
X_test = vec.transform(X_test)

clf = KNeighborsClassifier()
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)

accuracy_score(y_test, y_pred)
```

Dále můžeme využít i lineární verzi algoritmu Support Vector Machine. Ten očividně dosahuje výrazně lepších výsledků.

Protože je počet filmů v různých žánrech různý, jedná se o další nevyvážený (*unballanced*) dataset. Aby to algoritmus SVM zohlednil, přidáme parametr `class_weight=balanced`.

```python
X = data["text"]
y = data["genre"]

X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y, random_state=0)

vec = TfidfVectorizer(stop_words="english", ngram_range=(1, 2))
X_train = vec.fit_transform(X_train)
X_test = vec.transform(X_test)

clf = LinearSVC(class_weight="balanced")
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)

accuracy_score(y_test, y_pred)
```

Zjistit můžeme seznam skupin, na které algoritmus třídí popisy (to jsou žárny filmů, které uvažujeme).

```python
clf.classes_
```

Pro každé slovo a každý žánr vygeneruje algoritmus SVM koeficient. Čím je koeficient vyšší, patří spíše koeficient k danému žánru.

```python
df_coef = pandas.DataFrame(clf.coef_.T, columns=clf.classes_)
df_coef["words"] = vec.get_feature_names_out()
df_coef = df_coef.set_index("words")
df_coef
```

Můžeme si to vyzkoušet na žánru sci-fi. Níže jsou slova, která jsou typická pro sci-fi (mají nejvyšší hodnoty koeficientů).

```python
df_coef.sort_values("sci-fi", ascending=False).head(20)
```

Níže jsou slova, která která jsou typická pro jiné žárny než pro sci-fi (mají nejnižší hodnoty koeficientů).

```python
df_coef.sort_values("sci-fi", ascending=False).tail(20)
```

### Seznam stop words pro češtinu

I pro ostatní jazyky existují připravené seznamy stop words, pouze nejsou součástí modulu `scikit-learn`. Existuje například modul `stop-words`, který obsahuje stop words pro řadu jazyků (kromě češtiny třeba Ukrajinštinu, němčinu nebo katalánštinu). Modul je třeba nainstalovat.

```
pip install stop-word
```

Dále provedeme import funkce `get_stop_words`, které jako parametr `language` dáme hodnotu `czech`. Můžeme si to vyzkoušet na čtyřech větách, pro které jsme si seznam vytvořili sami.

```python
from stop_words import get_stop_words

X = [
    "Python je nejlepší", 
    "Mám rád Python!", 
    "Python je nejlepší jazyk",
    "Nesnáším Python!"
    ]
vec = TfidfVectorizer(stop_words=get_stop_words(language="czech"))
X = vec.fit_transform(X)
X = X.toarray()
X
```

Níže je seznam slov, která jsou v matici. Vidíme, že zůstala stejná slova jako v případě, kdy jsme si seznam tvořili sami. Použitím tohoto modulu si tedy můžeme ušetřit spoustu práce.

```python
vec.get_feature_names_out()
```

### Váhy tříd v algoritmu Support Vector Machine

Už jsme si říkali, že algoritmus Support Vector Machine funguje na principu vytváření nadrovin, které rozdělují nějaký prostor na dvě části. Současně víme, že algoritmus se snaží najít tzv. margin, tj. nějaké *ideální* rozdělení prostoru. Pokud máme data, která nelze lineárně oddělit, je třeba použít *soft margin*, tj. hranici, která body neoddělí úplně dokonale, ale ponechá některé body na nesprávné straně. Výsledný *soft margin* je tedy kompromisem mezi dvě kritérii:

* hledáme co nejširší margin, tj. takový margin, který vytvoří co největší prostor mezi oběma skupinami,
* chceme mít co nejméně bodů v "nesprávné" nadrovině.

Pokud bychom klasifikovali dvě různě velké skupiny a přikládali každému chybně zařazenému bodu stejnou váhu (bez ohledu na jeho skupinu), algortimus by se více přizpůsobil početnější skupině, protože z ní máme více dat a tím pádem je třeba větší úsilí, abychom snížili počet chyb. Pokud ale přidáme chybám z ménně početné skupiny větší váhu, algoritmus je nucen věnovat větší pozornost i chybám z druhé skupiny.

U parametru `class_weight` můžem zadat přímo váhy tříd jako slovník nebo použít parametr `balanced`, který nastaví váhy v závislosti na četnosti každé třídy v celém datasetu. Jinak řečeno, čím méně dat k nějaké třídě máme, tím větší váhu bude mít a tím pádem bude SVM více nucen při hledání ideálního margin věnovat pozornost chybám v rámci této třídy.

### Další zdroje

- [Using CountVectorizer to Extracting Features from Text](https://www.geeksforgeeks.org/using-countvectorizer-to-extracting-features-from-text/)
- [Cost-Sensitive SVM for Imbalanced Classification](https://machinelearningmastery.com/cost-sensitive-svm-for-imbalanced-classification/)
- [Intuition for the regularization parameter in SVM](https://datascience.stackexchange.com/questions/4943/intuition-for-the-regularization-parameter-in-svm)

::exc[excs/analyza-sentimentu]
