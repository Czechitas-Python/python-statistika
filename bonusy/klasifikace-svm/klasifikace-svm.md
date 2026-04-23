## Klasifikace s využitím SVM

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns

from sklearn.metrics import (
    accuracy_score,
    confusion_matrix,
    ConfusionMatrixDisplay,
    precision_score,
    recall_score,
)
from sklearn.inspection import DecisionBoundaryDisplay
from sklearn.preprocessing import OneHotEncoder, LabelEncoder
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.svm import LinearSVC, SVC
from sklearn.preprocessing import StandardScaler

import warnings

warnings.filterwarnings("ignore")
```

### Popis importů

* `ConfusionMatrixDisplay` - vizualizace matice záměn, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.ConfusionMatrixDisplay.html)
* `accuracy_score`, `precision_score` a `recall_score` - funkce pro vyhodnocení výsledků modelu, dokumentace je [zde](https://scikit-learn.org/stable/modules/classes.html#classification-metrics)
* `OneHotEncoder` provádí One Hot Encoding zadaných dat, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html)
* `LabelEncoder` provádí Label Encoding zadaných dat, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.LabelEncoder.html)
* `train_test_split` - funkce pro rozdělení dat na trénovací a testovací, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html)
* `GridSearchCV` hledá nejlepší parametry klasifikátoru ze zadaného rozsahu podle zadané metriky, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html)
* `LinearSVC` je klasifikátor používající lineární verzi algoritmu Support Vector Machine a One-to-Rest postup pro klasifikaci do více tříd, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.svm.LinearSVC.html)
* `SVC` je klasifikátor, který umožňuje využívat lineární i nelineární verzi algoritmu Support Vector machine a přístupy One-to-Rest i One-to-One, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html#sklearn.svm.SVC)
* `StandardScaler` - objekt pro normalizaci dat, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)

### Support Vector Machine

V této lekci budeme používat jeden z legendárních datasetů ve světě Data Science - [dataset o kostatcích z roku 1936](https://en.wikipedia.org/wiki/Iris_flower_data_set). Datovou sadu s daty o kosetcích (Iris) představil britský statistik a biolog Ronald Fisher ve svém článku z roku 1936 o využití mnohonásobných měření v taxonomických problémech. Někdy se mu říká Andersonův kosatcový datový soubor, protože Edgar Anderson shromáždil data k měření morfologické variace květin kosatce tří souvisejících druhů. Datová sada obsahuje 50 vzorků od každého ze tří druhů kosatce (Iris Setosa, Iris virginica a Iris versicolor). Od každého vzorku byly změřeny čtyři vlastnosti: délka a šířka kališních lístků a korunních lístků, v centimetrech.

Data jsou ke stažení [zde](IRIS.csv)

::fig[]{src=assets/iris-machinelearning.png}

```python
data = pd.read_csv("IRIS.csv")
data["species"].unique()
```

Naším úkolem bude prozkoumat, jestli je možné čistě na základě rozměrů uhádnout, o který druh kosatce se jedná. Abychom se drželi binární klasifikace, necháme se vi datech pouze dva druhý - Iris Versicolor a Iris Setosa. Abychom si data mohli zobrazit pomocí obrázku, využijeme navíc pouze dva číselné údaje - výšku a šířku kališního lístku.

```python
data = data[data["species"].isin(['Iris-setosa', 'Iris-versicolor'])]
data = data[["sepal_length", "sepal_width", "species"]]
data.head()
```

Data si zobrazíme jako bodový graf. Když se podíváme na graf níže, bylo by teoreticky možné oba druhy kostatců oddělit. Navíc by toto mělo být možné s využitím lineární funkce (přímky). Právě v tom spočívá princip metody Support Vector Machine (SVM) - rozdělí prostor na dvě části s využitím lineární funkce. Metoda nakreslí hranici tak, aby vzdálenost od nejbližších bodů z k hranici byla z obou stran co největší.

Jestliže je možné data kompletně oddělit, označujeme je jako lineárně oddělitelná (*linearly separable*).

```python
sns.scatterplot(data, x="sepal_length", y="sepal_width", hue="species")
```
::fig[]{src=assets/plot_01.png}

Opět použijeme modul `scikit-learn`. V tomto případě jako klasifikátor využijeme `LinearSVC()`. Pouze s tím rozdílem, že jako klasifikátor využijeme `LinearSVC`.

```python
X = data.drop(columns=["species"])
y = data["species"]

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

clf = LinearSVC()
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)
```

S využitím `DecisionBoundaryDisplay` můžeme do našeho grafu vložit hranici, která obě skupiny rozděluje. V tom spočívá princip metody Support Vector Machine (SVM, metoda podpůrných vektorů, SVC je zkratka Support Vector Classifier). SVM je další z algoritmů strojového učení, který můžeme použít pro klasifikaci a který je založený na vzdálenosti v nějakém prostoru. Algoritmus je založený na rozdělení možných hodnot vstupních proměnných do různých nadrovin (hyperplane). Nadroviny máme dvě - pozitivní nadrovinu a negativní nadrovinu. Následně klasifikujeme data podle toho, jestli se budou nacházet v pozitivní či negativní nadrovině. 

Algoritmus nakreslí hranici mezi nadrovinami tak, aby hranice mezi nimi byla co nejširší, snaží se tedy maximalizovat vzdálenost mezi nejbližšími dvěma body. Hranice mezi skupinami dat označujeme jako *margin*. SVM se tedy snaží nají optimální nadrovinu (*optimal hyperplane*), která zajišťuje nejširší margin. Přímky, které jsou rovnoběžné s hranici a procházejí nejbližšími body k hranici jsou označeny jako *support vector*.

```python
ax = sns.scatterplot(x=X_train[:, 0], y=X_train[:, 1], hue=y_train)
DecisionBoundaryDisplay.from_estimator(clf, X_train, ax=ax, plot_method="contour", levels=[0])
```
::fig[]{src=assets/plot_02.png}

::fig[]{src=assets/svm_popis.png}

Opět si můžeme vytvořit matici záměn.

Vidíme, že oproti KNN (s výchozí hodnotou 5 uvažovaných sousedů) máme více True Negatives, ale za cenu méně True Positives.

```python
ConfusionMatrixDisplay.from_estimator(
    clf,
    X_test,
    y_test,
)
```
::fig[]{src=assets/plot_03.png}

Můžeme si spočítat i metriky `accuracy_score`, které jsme si ukazovali v minulé lekci. Vidíme, že hodnoty metrik jsou srovnatelné s hodnotami metrik KNN.

```python
accuracy_score(y_test, y_pred)
```

Jestliže jsou data lineárně oddělitelná, označujeme hranici mezi nimi jako *hard margin*. Pokud data není možné lineárně oddělit, hranice mezi nimi se označuje jako *soft margin*. V takovém případě se algoritmus snaží nakreslit hranici tak, aby co nejvíce bodů bylo ve "správné" nadrovině a aby body ve "špatné" nadrovině byly co nejblíže hranici. Funkce, která toto měří, se označuje jako *hinge loss*. Při kreslení soft margin jsou vyvažována dvě kritéria - šířka marginu a množství nesprávně klasifikovaných bodů. Funkce jsou vyvačovány pomocí deep parameter `C`.

### Klasifikace do více než dvou tříd

V řadě případů potřebujeme rozdělovat data do více než dvou tříd. Například pacienty můžeme rozdělovat dle stádia jejich nemoci (tj. nerozlišujeme jen zdravé a nemocné pacienty, ale pacienty zdravé, v počátečním a pokročilém stádiu nemoci), v dopravě můžeme klasifikovat několik typů vozidel (např. osobní automobil, nákladní automobil, autobus, traktor, motocykl atd.), zákaznické recence můžeme rozlišovat na pozitivní, neutrální a negativní atd.

Klasifikaci do více tříd můžeme provádět více způsoby. Jedním z nich je "přímé" řešení, tj. pracujeme od začátku do konce s klasifikací do více tříd. Takový přístup využívá například algoritmus K Nearest Neighbors (KNN) nebo rozhodovací strom.

Druhým z přístupů je převed úlohy s více třídami na problém dvou tříd, tedy binární klasifikaci. Takto postupuje algoritmus SVM. Tento postup je dále možné provádět dvěma různými způsoby:

- Prvním z nich je varianta "jeden proti všem" (**One-to-Rest**). Při jeho použití algoritmus vytvoří tolik klasifikátorů, kolik máme tříd, a trénuje každý jako **binární klasifikaci jedné třidy oproti všem ostatním**. Při predikci každý z klasifikátorů předpoví buď "svojí" třídu, nebo "ostatní třídy". Pokud bychom například rozdělovali data do tří skupin, vytvoří tento postup tři klasifikátory.
- Druhou z možností je "jeden proti jednomu" (**One-to-One**). Při jeho použití algoritmus vytvoří klasifikátor pro každou dvojici tříd. Každý klasifikátor porovnává dvě třídy a data z ostatních tříd jsou ignorována.

Data najdete [zde](Dry_Bean_Dataset.csv)

Zdroj dat: https://data.world/makeovermonday/2021w14

```python
data = pd.read_csv("Dry_Bean_Dataset.csv")
data.head()
```

```python
X = data.drop(columns=["Class"])
y = data["Class"]
y.value_counts()
```

Rozdělíme data na trénovací a testovací sadu.

Parametr `stratify` určuje, podle jakého sloupce chceme zachovat poměr hodnot. V našem případě chceme zachovat poměr tříd (aby v trénovacích i testovacích datech byly třídy podobně zastoupené).

```python
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.3, random_state=42
)
```

```python
clf = SVC(kernel="linear", decision_function_shape="ovr", random_state=42)
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)
```

#### Jak vyhodnocujeme klasifikaci do více tříd?
Podíváme se, jak vypadá chybová matice pro více tříd. Funguje na stejném principu, jen je větší, a není na první pohled jasné, jak spočítat metriky jako precision nebo recall. Všimněme si ale, že základní metriky accuracy je stejná: Součet hodnot na diagonále (součet správně určených bodů) oproti velikosti datasetu.


```python
ConfusionMatrixDisplay.from_estimator(
    clf,
    X_test,
    y_test,
    cmap=plt.cm.Blues,
)
```
::fig[]{src=assets/plot_04.png}

Nyní můžeme vyhodnotit přesnost. Využijeme metriku `accuracy_score`, která poměřuje počet správně označených testovacích dat oproti celkovému množství testovacích dat.

```python
print(accuracy_score(y_test, y_pred))
```
Výstup: `0.9222222222222223`

### Nelineární SVM

Na obrázku je případ, kdy jsou obě skupiny **lineárně oddělitelné**. To ale nemusí platit vždy. V některých případech se mohou obě skupiny prolínat a není pak možné vytvořit dvě nadroviny. V takovém případě máme více možností. 

Jednou z nich je nakreslení nadrovin tak, abychom minimalizovali celkový součet špatně umístěných bodů od hranice. Jinými slovy, snažíme se, aby bodů, které jsou na špatné straně, bylo co nejméně a aby jejich vzdálenost od hranice byla co nejmenší. Takovou hranici pak označujeme jako *soft margin*.

Další z možností je použití nelineárního klasifikátoru. To spočívá v převedení dat do vyšších rozměrů. Máme-li například dvourozměrná data, můžeme data přenést do trojrozměrného prostoru. Ve trojrozměrném prostoru už pak může být možné vytvoření nadrovin tak, aby byly všechny vstupní hodnoty oddělené. Níže je příklad převedení dat z dvourozměrného prostoru do trojrozměrného. Třetí dimenze (výška) je daná vzdáleností od středu. Čím je bod v dvourozměrném prostoru blíže ke středu, tím výše ho umístíme ve dvourozměrném prostoru. Ve trojrozměrném prostoru již můžeme data rozdělit na dvě nadroviny.

::fig[]{src=assets/1_vjYjGdVTetHvm7s9XcidBQ.webp}

My se budeme držet lineárního klasifikátoru, tj. takového, který nezvyšuje počet dimenzí našich dat. Využijeme ho pro rozdělení vín na dobrá a špatná a porovnáme si výkon SVC s algoritmem K Neares Neighbors.

```python
data = pd.read_csv("IRIS.csv")
data = data[data["species"].isin(['Iris-virginica', 'Iris-versicolor'])]
data = data[["sepal_length", "sepal_width", "species"]]

X = data.drop(columns=["species"])
y = data["species"]

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

clf = SVC(kernel="rbf")
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)

ax = sns.scatterplot(x=X_train[:, 0], y=X_train[:, 1], hue=y_train)
DecisionBoundaryDisplay.from_estimator(clf, X_train, ax=ax, plot_method="contour", levels=[0])
```
::fig[]{src=assets/plot_05.png}

### Zdroje

* [Machine Learning Algorithms(16) — Support Vector Machine(SVM)](https://towardsdev.com/machine-learning-algorithms-16-support-vector-machine-svm-878c2e1d024f)
* [Support Vector Machine Algorithm](https://www.javatpoint.com/machine-learning-support-vector-machine-algorithm)
* [Multiclass Classification Using Support Vector Machines](https://www.baeldung.com/cs/svm-multiclass-classification)
* [Support Vector Machines (SVM) and the Multi-Dimensional Wizardry](https://towardsdatascience.com/support-vector-machine-svm-and-the-multi-dimensional-wizardry-b1563ccbc127)
* [Support Vector Machine(SVM): A Complete guide for beginners](https://www.analyticsvidhya.com/blog/2021/10/support-vector-machinessvm-a-complete-guide-for-beginners/)
* [Support Vector Machine — Introduction to Machine Learning Algorithms](https://towardsdatascience.com/support-vector-machine-introduction-to-machine-learning-algorithms-934a444fca47)
* [What is underfitting and overfitting in machine learning and how to deal with it.](https://medium.com/greyatom/what-is-underfitting-and-overfitting-in-machine-learning-and-how-to-deal-with-it-6803a989c76)

::exc[excs/kosatce]

::exc[excs/bonus-poruchy]
