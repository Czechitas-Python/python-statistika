## TF-IDF a klasifikace dokumentů

### Metoda TF-IDF

Jak můžeme úspěšnost modelu zlepšit? V podstatě jsou dvě úrovně: úroveň dat a úroveň klasifikačního algoritmu. Pojďme začít u dat, protože když nebudeme mít čistá data, žádný algoritmus nás nezachrání (toto se dá také shrnout pořekadlem "garbage in, garbage out").

- Všimněme si, že nejčastější slova jsou taková, která se nacházejí skoro ve všech popiskách. Těmto častým slovům, která nenesou žádný význam, se říká _stop words_. Každý jazyk má svůj blacklist těchto slov, která se většinou z dat úplně vyfiltrují. `CountVectorizer` má parametr `stop_words`, který můžeme nastavit na hodnotu `"english"`.

- Populární metoda pro normalizaci četností slov je TF-IDF (Term Frequency Inverse Document Frequency). Tato normalizace zohledňuje jak četnost slova v dokumentu, tak i to, jak často se objevuje vůbec v celých vstupních datech. Takže slovo, které by se velmi často objevovalo jen v několika dokumentech, by mělo větší váhu, než jiné slovo, které se objevuje v mnoha dokumentech.

::fig[]{src=assets/tf-idf.png}

Můžeme tedy `CountVectorizer` vyměnit za `TfidfVectorizer`:

```python
X = ["Python is great", "I like Python", "Python is the best language", "I hate Python"]
vec = TfidfVectorizer()
X = vec.fit_transform(X)
X = X.toarray()
X
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
df = pandas.DataFrame(X, columns=vec.get_feature_names_out())
df
```

### Postup výpočtu

Podívejme se na způsob výpočtu. Vraťme se k tabulce, kterou vygeneruje `CountVectorizer`.

```python
X = ["Python is great", "I like Python", "Python is the best language", "I hate Python"]

vec = CountVectorizer(stop_words="english")
X = vec.fit_transform(X)
X = X.toarray()
X
```

Nyní porovnejme matici s tím, co vygeneruje `TfidfVectorizer`. Přibyl parametr `smooth_idf`, jehož funkci si ještě objasníme.

```python
X = ["Python is great", "I like Python", "Python is the best language", "I hate Python"]

vec = TfidfVectorizer(stop_words="english", smooth_idf=False)
X = vec.fit_transform(X)
X = X.toarray()
X
```

```python
df = pandas.DataFrame(X, columns=vec.get_feature_names_out())
df
```

Vypočítejme si nyní sami hodnoty v tabulce pro první řádek. Začneme s výrazem great. Postupujeme podle vzorce:

$$
\textrm{idf}(t) = \log \frac{n}{1 + \textrm{df}(t)}
$$

kde $\textrm{df}$ je počet dokumentů, které obsahují výraz $t$, $n$ je celkový počet dokumentů a výsledkem je $idf$ (inverse document-frequency).

```python
import numpy

n = 4
df_great = 1
idf_great = numpy.log(n / df_great) + 1
idf_great
```

Dále dopočítáme hodnotu ukazatele tf-idf s využitím vzorce:

$$
\textrm{tf-idf}(t,d) = \textrm{tf}(t,d) * \textrm{idf}(t)
$$

kde $\textrm{tf}(t,d)$ znamená term-frequency, tj. počet výskytů výrazu $t$ v dokumentu $d$.

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

Nakonec provedeme normalizaci, kterou získáme jako podíl tf-idf pro dané slovo a odmocninu druhých mocnin tf-idf pro všechna slova.

```python
tf_idf_great / numpy.sqrt(tf_idf_great ** 2 + tf_idf_python ** 2)
```

Dále si vyzkoušíme variantu bez parametru `smooth_idf`. Rozdíl je v tom, že idf počítáme z mírně upraveného vzorce:

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

Pokud nám nevyhovuje výchozí seznam stop words pro daný jazyk, můžeme si vytvořit svůj. To se může hodit pro češtinu nebo třeba v situaci, že se dané slovo vyskytuje příliš často.

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
df = pandas.DataFrame(X, columns=vec.get_feature_names_out())
df
```

### Klasifikace dokumentů

Vyzkoušejme nyní klasifikaci dokumentů (resp. filmů, o kterých dané dokumenty pojednávají). Využijeme `TfidfVectorizer` s parametry `stop_words` a `ngram_range`.

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

Dále můžeme využít i lineární verzi algoritmu Support Vector Machine. Ten dosahuje výrazně lepších výsledků.

Protože je počet filmů v různých žánrech různý, jedná se o nevyvážený (*unbalanced*) dataset. Aby to algoritmus SVM zohlednil, přidáme parametr `class_weight="balanced"`.

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

Zjistit můžeme seznam skupin, na které algoritmus třídí popisy (to jsou žánry filmů, které uvažujeme).

```python
clf.classes_
```

Pro každé slovo a každý žánr vygeneruje algoritmus SVM koeficient. Čím je koeficient vyšší, patří spíše dané slovo k danému žánru.

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

Níže jsou slova, která jsou typická pro jiné žánry než pro sci-fi (mají nejnižší hodnoty koeficientů).

```python
df_coef.sort_values("sci-fi", ascending=False).tail(20)
```

### Seznam stop words pro češtinu

I pro ostatní jazyky existují připravené seznamy stop words, pouze nejsou součástí modulu `scikit-learn`. Existuje například modul `stop-words`, který obsahuje stop words pro řadu jazyků. Modul je třeba nainstalovat:

```
pip install stop-words
```

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

```python
vec.get_feature_names_out()
```

### Váhy tříd v algoritmu Support Vector Machine

Algoritmus Support Vector Machine funguje na principu vytváření nadrovin, které rozdělují prostor na dvě části. Algoritmus se snaží najít tzv. margin, tj. ideální rozdělení prostoru.

Pokud bychom klasifikovali dvě různě velké skupiny a přikládali každému chybně zařazenému bodu stejnou váhu, algoritmus by se více přizpůsobil početnější skupině. Pokud ale přidáme chybám z méně početné skupiny větší váhu, algoritmus je nucen věnovat větší pozornost i chybám z druhé skupiny.

U parametru `class_weight` můžeme zadat přímo váhy tříd jako slovník nebo použít hodnotu `"balanced"`, která nastaví váhy v závislosti na četnosti každé třídy v celém datasetu.

## Další zdroje

- [Using CountVectorizer to Extracting Features from Text](https://www.geeksforgeeks.org/using-countvectorizer-to-extracting-features-from-text/)
- [Cost-Sensitive SVM for Imbalanced Classification](https://machinelearningmastery.com/cost-sensitive-svm-for-imbalanced-classification/)
- [Intuition for the regularization parameter in SVM](https://datascience.stackexchange.com/questions/4943/intuition-for-the-regularization-parameter-in-svm)
