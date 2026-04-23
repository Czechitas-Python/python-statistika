## Shlukování

```python
import pandas as pd

import matplotlib.pyplot as plt

from sklearn.manifold import TSNE
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.tree import DecisionTreeClassifier
from sklearn.model_selection import GridSearchCV
```

#### Popis importů

* `TSNE` redukce dimenzionality (počtu sloupců) s využitím algoritmu TSNE, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.manifold.TSNE.html)
* `KMeans` shlukování s využitím algoritmu K-Means, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html)
* `StandardScaler` - objekt pro normalizaci dat, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)
* `OneHotEncoder` provádí One Hot Encoding zadaných dat, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html)
* `DecisionTreeClassifier` - klasifikátor využívající algoritmus rozhodovacího strumu, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html)
* `GridSearchCV` hledá nejlepší parametry klasifikátoru ze zadaného rozsahu podle zadané metriky, dokumentace je [zde](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html)

### Unsupervised learning

Doposud jsme se zabývali klasifikací, jako jednou s podtříd supervized learning, tedy úloh, kde předem známe správné odpovědi. Model se pomocí algoritmu a trénovacích dat, které obsahují tyto správné odpovědi, "naučí" jak jednotlivé třídy vypadají, aby později mohl přiřazovat nová data do tříd. V případě regresní úlohy nová data nedostanou přiřazenou třídu, ale nějakou hodnotu jako reálné číslo. Obecný koncept je ale stejný.

V této lekci se budeme věnovat úlohám bez supervize, tedy takovým, kde **předem neznáme správné odpovědi,** nebo tyto odpovědi vůbec neexistují. 

Příklady takových úloh jsou: 

- rozdělování dat na shluky (clustering), na rozdíl od klasifikačních úloh **ale nevíme o žádném záznamu, do kterého shluku patří, ani nevíme, kolik by takových shluků mělo být,**
- hledání anomálií v datech (tj. hledáme něco neobvyklého, například neobvyklé datové toky v síti nebo podivné údaje z nějakého měřícího zařízení).

#### Shlukování dat (clustering)

Na obrázku například vidíme data, nad kterými by se dalo přemýšlet jako nad dvěma nebo třemi shluky. Důležité je, že o žádném údaji nevíme, do kterého shluku by měl patřit! Rozdělení je tedy čistě na nás. Je na nás, na kolik shluků data rozdělíme.

::fig[]{src=assets/attachment:clustering.png}

#### K-means

Ukážeme si shlukovací algoritmus K-means. _K_ proto, že očekává zadaný počet shluků od uživatele, a _means_ zde znamená průměr - střed shluku neboli _centroid_.

Algoritmus k-means se skládá z následujících kroků:

1. **Výběr počtu shluků (k)**: Algoritmus k-means vyžaduje, abyste předem určili počet shluků, které chcete vytvořit. Počet těchto shluků se označuje jako 'k'. Algoritmus můžeme spouštět opakovaně a různým počtem nastavených shluků a sledovat, jak dobře takový počet "sedí" na naše data.
1. **Náhodná inicializace středů shluků**: Poté, co jsme určili hodnotu 'k', algoritmus náhodně vybere 'k' bodů z datové sady. Tyto body se stávají prvními 'centroidy' - tedy středy shluků.
1. **Přiřazení bodů k nejbližším středům shluků**: Každý bod v datové sadě se nyní přiřadí k tomu centroidu, ke kterému má nejkratší vzdálenost. Tím se vytvoří dočasné shluky.
1. **Přesu středů shluků**: Poté, co jsou všechny body přiřazeny k shlukům, algoritmus spočítá nový střed každého shluku jako průměr všech bodů, které do tohoto shluku patří. Následně je *centroid* přesunutý do středu shluku.
1. **Opakování kroků 3 a 4**: Algoritmus nyní opakuje kroky 3 a 4, dokud se středy shluků nepřestanou posouvat nebo dokud se nevyčerpá předem stanovený počet iterací. Každá iterace upřesňuje středy shluků a přiřazuje body k novým středům.

::fig[]{src=assets/attachment:kmeans.png}

Úloha, která nás bude provázet lekcí, se klasifikace uživatelů kreditních karet, která jsou v souboru [CC_GENERAL.CSV](CC_GENERAL.CSV).

[Zdroj dat](https://www.kaggle.com/datasets/arjunbhasin2013/ccdata?resource=download)

Popisy sloupců (features):

* CUST_ID: Identifikace držitele kreditní karty (Kategorické)
* BALANCE: Zůstatek na účtu pro nákupy
* BALANCE_FREQUENCY: Jak často se aktualizuje zůstatek, skóre mezi 0 a 1 (1 = často aktualizováno, 0 = nečasto aktualizováno)
* PURCHASES: Částka nákupů provedených z účtu
* ONEOFF_PURCHASES: Maximální částka nákupu provedená najednou
* INSTALLMENTS_PURCHASES: Částka nákupu provedená na splátky
* CASH_ADVANCE: Hotovost předem poskytnutá uživatelem
* PURCHASES_FREQUENCY: Jak často se provádějí nákupy, skóre mezi 0 a 1 (1 = často nakupováno, 0 = nečasto nakupováno)
* ONEOFFPURCHASESFREQUENCY: Jak často se provádějí nákupy najednou (1 = často nakupováno, 0 = nečasto nakupováno)
* PURCHASESINSTALLMENTSFREQUENCY: Jak často se provádějí nákupy na splátky (1 = často prováděno, 0 = nečasto prováděno)
* CASHADVANCEFREQUENCY: Jak často se platí hotovost předem
* CASHADVANCETRX: Počet transakcí provedených s "Cash in Advanced"
* PURCHASES_TRX: Počet provedených nákupních transakcí
* CREDIT_LIMIT: Limit kreditní karty pro uživatele
* PAYMENTS: Částka platby provedená uživatelem
* MINIMUM_PAYMENTS: Minimální částka plateb provedených uživatelem
* PRCFULLPAYMENT: Procento úplné platby uhrané uživatelem
* TENURE: Doba trvání služby kreditní karty pro uživatele

```python
X = pd.read_csv("CC_GENERAL.csv")
X.head()
```

```python
X = X.head(1000)
```

Určitě musíme z dat odebrat sloupec `CUST_ID`.

```python
X = X.drop(columns=["CUST_ID"])
```

```python
X.shape
```

Data musíme určitě normalizovat s využitím `StandardScaler()`. U unsupervised learning nedělíme data na trénovací a testovací, takže normalizujeme všechna data najednou.

```python
scaler = StandardScaler()
X = scaler.fit_transform(X)
```

#### Snížení dimensionality

U shlukování je vždy dobré si data **vizualizovat**. Jak ale na to? Máme celkem 17 proměnných, to je jako 17-ti dimenzionální prostor. Vizualizovat umíme ve dvou dimenzích, možná i ve třech. V jedenácti ale těžko.

Modul `scikit-learn` má k dispozici nástroje na snížení dimenzionality ("sníže počtu proměnných"). Snížení počtu proměnných má několik výhod. Nejenom, že můžeme pak data snáz vizualizovat, ale zbavíme se problému kolinearity (korelace dvou nebo více proměnných), a navíc snížíme čas a výkon potřebný pro další modelování.

Redukce dimensionality je postup, který se snaží zachovat původní strukturu dat a minimalizovat ztrátu informace, ke které dojde při odebrání některých sloupců.

Jedna z metod, která nám sníží počet proměnných ("redukuje dimenzionalitu") se jmenuje t-SNE (t-distributed Stochastic Neighbor Embedding). Modul `scikit-learn` tuto metodu implementuje, pojďme jí vyzkoušet.

`TSNE` má několik důležitých parametrů:
- `n_components` říká, kolik dimenzí (proměnných) chceme. U t-SNE typicky volíme hodnotu 2 nebo 3.
- `perplexity` určuje, podle kolika sousedů se má metoda t-SNE řídit. Čím vyšší je náš dataset, tím vyšší hodnotu parametru nastavíme. Běžně se používají hodnoty mezi 5 a 50.

Princip metody spočívá v tom, že se snaží při redukci počtu dimenzí udržovat body, které jsou si v původní dimenzi blízké, blízko u sebe. 

```python
tsne = TSNE(
    n_components=2,
    random_state=42,
)
X = tsne.fit_transform(X)
```

Nyní jsou naše data redukovaná jen do dvou dimenzí:

```python
X.shape
```

```python
plt.scatter(X[:, 0], X[:, 1], s=50)
```
::fig[]{src=assets/plot_01.png}

Můžeme spustit algoritmus K-means. Uvažujme nyní, že chceme vytvořit 2 shluky.

```python
model = KMeans(n_clusters=2, random_state=42, n_init="auto")
labels = model.fit_predict(X)
```

Výsledek si můžeme zobrazit graficky. Každý bod obarvíme do barvy dle shluku, do kterého byl přiřazen, a doplníme i centroidy.

```python
# Zobrazení bodů
plt.scatter(X[:, 0], X[:, 1], c=labels, s=50, cmap="Set1")
# Zjištění centroidů
centers = model.cluster_centers_
# Zobrazení centroidů
plt.scatter(centers[:, 0], centers[:, 1], c="black", s=200, alpha=0.5)
```
::fig[]{src=assets/plot_02.png}

Dva shluky ale nemusejí být to správné číslo. Jak poznat, kolik shluků bychom měli mít? K tomu můžeme využít například metriku `inertia`.  Tato metrika je výpočet sumy kvadratických vzdáleností všech bodů v shluků ke středu shluku, neboli centroidu. Čím více máme shluků, tím spíše bude vzdálenost klesat. Ale je pravděpodobné, že tempo poklesu bude s růstem klastrů klesat. Není tedy naším cílem vytvořit model s obrovským množstvím shluků (takový by nám toho stejně moc neřekl). Ve skutečnosti chceme vybrat počet, ve kterém počet klastrů výrazně zpomaluje.

```python
distances = []
cluster_counts = range(1, 15)
 
for k in cluster_counts:
    model = KMeans(n_clusters=k, n_init="auto").fit(X)
    model.fit(X)
    distances.append(model.inertia_)

plt.plot(cluster_counts, distances,)
```
::fig[]{src=assets/plot_03.png}

```python
model = KMeans(n_clusters=4, random_state=42, n_init="auto")
labels = model.fit_predict(X)

# Zobrazení bodů
plt.scatter(X[:, 0], X[:, 1], c=labels, s=50, cmap="Set1")
# Zjištění centroidů
centers = model.cluster_centers_
# Zobrazení centroidů
plt.scatter(centers[:, 0], centers[:, 1], c="black", s=200, alpha=0.5)
```
::fig[]{src=assets/plot_04.png}

#### Připojení informace o shluku k datům

Cluster můžeme připojit k původním datům. Protože u našich dat jsme provedli redukci dimenzionality a tím pádem ztratili původní sloupce, načteme si data znovu ze souboru. Do pandas tabulky pak můžeme přidat `cluster` každé hodnoty jako sloupec.

```python
X = pd.read_csv("CC_GENERAL.csv")
X = X.drop(columns="CUST_ID")
X = X.head(1000)
X["cluster"] = labels
X.head()
```

Clustery, které jsme spočítali, jsou poměrně anonymní. Můžeme si ale vypočítat průměrné hodnoty jednotlivých sloupců (features) pro jednotlivé clustery, a to s využitím `pandas` agregace.

Níže třeba vidíme, že shluky se výrazně liší ve sloupci `BALANCE`, přičemž nejnižší hodnotu má shluk 0 a nejvyšší hodnotu shluk 3. Ve sloupcích `PURCHASES`, `ONEOFF_PURCHASES`, `INSTALLMENTS_PURCHASES` a `PURCHASES_FREQUENCY` má nejvyšší hodnotu shluk 2. Pro zákazníky a zákaznice ze shluku 2 by tedy mohly být nejzajímavější bankovní produkty související s nákupem, jako třeba výhodné spotřebitelské úvěry, slevu na platbu kartou atd. Zákazníci ve shluku 3 mají více volné hotovosti, ale méně nakupují, můžeme jim tedy nabídnout například spořící a investiční produkty. Zákazníkům a zákaznící ze shluku 0 bychom mohli nabídnout kontokorentní úvěr, protože u nich hrozí, že se jejich bankovní účet dostane do mínusu.

```python
X.groupby("cluster").mean()
```

Můžeme se podívat i na počet zákazníků a zákaznic v jednotlivých shlucích.

```python
X.groupby("cluster").size()
```

### Cvičení

::exc[excs/vzorky-vina]
