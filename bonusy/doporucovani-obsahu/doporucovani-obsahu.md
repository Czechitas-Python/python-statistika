## Doporučování obsahu

### Systémy na doporučování obsahu

Systémy na doporučování obsahu (*recommendation engine*) se snaží z dostupných možností vybrat tu nejzajímavější pro konkrétního uživatele. Systémy na doporučování obsahu jsou běžnou součástí řady služeb, například e-shopů, streamovacích služeb, sociálních sítí, pracovních portálů. Pomocí těchto systémů se poskytovatelé snaží udržet uživatele co nejdéle na jejich platformě (aby jim mohli zobrazit co nejvíce reklamy), nabídnout produkt, který si uživatel s největší pravděpodobností koupí atd.

Systémy na doporučování obsahu mohou fungovat na dvou principech.

Filtrování na základě obsahu (*content based filtering*) využívá data o jednotlivých položkách (např. filmech, knihách atd.). Jestliže se uživateli (uživatelce) líbí nějaký obsah, systém mu nabídne obsah, který je nejvíce podobný. Při vývoji tohoto systému tedy řešíme podobnost jednotlivých položek (např. na základě popisu).

::fig[]{src=assets/content-based.png}

K vývoji nám stačí data o obsahu, tento přístup je tedy možné použít pro začínající službu. V některých případech může ale vést k "očividným" výsledkům, například pokud se někomu líbí první díl Star Wars, je mu nabídnutý druhý díl atd.

Kolaborativní filtrování (*collaborative filtering*) využívá data z chování uživatelů. Systém nějakému uživateli (uživatelce) vybírá takový obsah, který se líbil jemu podobným uživatelům (uživatelkám). Při vývoji tohoto systému tedy řešíme podobnost preferencí uživatelů (uživatelek) dané služby.

Pro vývoj je potřeba mít k dispozici dostatek dat o chování uživatelů. To může být problém například u nově vytvořených služeb, nových příspěvků na sociálních sítích, nových nebo nepřihlášených uživatelů/uživatelek atd. Pokud ale data máme k dispozici, může tento přístup generovat poměrně zajímavé výsledky.

::fig[]{src=assets/collaborative-filtering.png}

Data mohou být založená na tzv. explicitním nebo implicitním feedbacku (*explicit feedback*, *implicit feedback*). Příkladem explicitního feedbacku je použití tlačítka "To se mi líbí", napsání pozitivní recenze atd. Příkladem implicitní vazby je dokoukání (nebo naopak nedokokání) filmu do konce, opětovné zhlédnutí atd.

#### Filtrování na základě obsahu

V této části si vyzkoušíme filtrování na základě popisu. Využijeme dataset s 5000 filmy ze serveru [imdb.com](https://imdb.com). Pozor, jedná se o jiný dataset, než jaký jsme používali v minulé lekci. Dataset je uložený v souboru [tmdb_5000_movies.csv](tmdb_5000_movies.csv). Budeme využívat sloupec `overview`, kde jsou uložené slovní popisy filmů. Filmy, které nemají slovní popis, z dat vyřadíme.

```python
import pandas as pd
import numpy as np
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

data = pd.read_csv("tmdb_5000_movies.csv")
data = data.dropna(subset=["overview"])
data.head()
```

Podíváme se, kolik nám zbylo filmů.

```python
data.shape
```

Na sloupec `overview` opět použijeme algoritmus TF-IDF, který jsme již používali v minulé lekci. Tento algoritmus převede jednotlivé popisy (dokumenty) na číselnou matici. Každé slovo (nebo dvojice slov) budou mít svůj sloupeček a číslo v něm bude reflektovat přítomnost slova v dokumentu a přítomnost slova ve všech dokumentech. Nově přidáme dva parametry.

- `min_df` je minimální počet výskytů slova v dokumentech, aby nebylo vyřazeno. Potřebujeme slova, která se nachází alespoň ve dvou dokumentech, unikátní slovo nám nijak nepomůže propojit dokument s jiným dokumentem.
- `max_df` je maximální počet výskytů slova (zadáváme jako desetinné číslo, tj. 70 %). Slova, která jsou ve většině dokumentů, nám taky nepomůžou vybrat vhodný obsah.

Pro oba parametry platí následující pravidla:

- Pokud zadáme hodnotu v intervalu 0 až 1, je hodnota braná jako procentuální výskyt.
- Pokud zadáme hodnotu vyšší než 1, je hodnota braná jako výskyt v absolutní hodnotě.

Parametry `stop_words` a `ngram_range` jsme si již ukázali minule a jich význam je stále stejný. Dále použijeme metodu `fit_transform()` a na výsledná data se podíváme.

```python
X = data["overview"]

vec = TfidfVectorizer(min_df=2, max_df=0.7, stop_words="english", ngram_range=(1, 2))
data_tfidf = vec.fit_transform(X)
# Převod na pole
data_tfidf.toarray()
```

Jednotlivá slova, kterým čísla patří, získáme pomocí metody `get_feature_names_out()`.

```python
vec.get_feature_names_out()
```

Abychom se na data mohli podívat v lepším formátu, můžeme si z nich opět sestavit pandas tabulku. Při vytváření tabulky řekneme, že popisky sloupců je možné získat pomocí metody `get_feature_names_out()`. Protože počet ani pořadí řádků se nijak nezměnily, načteme index tabulky ze sloupce `title` z původní tabulky s daty.

```python
data_tfidf = pd.DataFrame(data_tfidf.toarray(), columns=vec.get_feature_names_out(), index=data["title"])
data_tfidf.head()
```

Nyní si vybereme jeden film, například film [The Fugitive (Uprchlík)](https://www.imdb.com/title/tt0106977/) z roku 1993 s Harrísonem Fordem v hlavní roli. Seřadíme si hodnoty od nejvyšší po nejmenší. Vidíme, že především slovo "Richard" může být celkem matoucí, protože jde o jméno hlavní postavy filmu, ale k ději nemá žádnou vazbu. Naopak slova jako "killer", "wrongfully" nebo "pursuing team" můžou být poměrně důležitá a můžou nám pomoci najít dějově podobný film. (Film Uprchlík je o chirurgovi Richardovi Kimblovi, který byl nespravedlivě odsouzen za vraždu své ženy. Při převozu do vězení se mu podaří uprchnout. Poté hledá skutečného vraha a je při tom pronásledován skupinou policitů.)

```python
data_tfidf.loc["The Fugitive"].sort_values(ascending=False).head(20)
```

Abychom dokázali změřit podobnost mezi jednotlivými řádky tabulky `data_tfidf`, použijeme kosinovou podobnost.

##### Kosinová vzdálenost a podobnost

Kosinová vzdálenost (*cosine distance*) a kosinová podobnost (*cosine similarity*) jsou způsoby měření vzdálenosti, se kterým jsme se zatím nesetkali. Ukažme si, jak kosinová vzdálenost a podobnost fungují v dvourozměrném prostoru, tj. jak by změřila vzdálenost dvou hodnot z tabulky se dvěma sloupci. Body vidíme na obrázku níže. Kosinová podobnost je vypočítaná jako kosinus úhlu $\theta$, tj. úhlu, který svírají vektory, které směrují do obou bodů. Pokud bychom měli dva naprosto totožné body, úhel mezi jejich vektory by měl velikost 0 stupňů. Kosinová vzdálenost těchto bodů by byla 0, kosinová podobnost by byla 1.

::fig[]{src=assets/cosine.png}

Kosinovou vzdálenost a podobnost je možné spočítat i ve vícerozměrném prostoru a i v něm platí, že čím jsou si vektory podobnější, tím větší je hodnota kosinové podobnosti a menší hodnota kosinové vzdálenosti. My budeme využívat kosinovou podobnost. Kosinová podobnost je v modulu `scikit-learn` implementovaná ve funkci `cosine_similarity()`.

Funkci můžeme dát dvě hodnoty (v našem případě dva filmy) a ona spočítá kosinovou podobnost, tj. podobnost jejich popisů. Pokud funkci dáváme dva filmy, můžeme je vybrat s využitím indexu (a vlastnosti `loc`). Funkce ale očekává dvourozměrné pole.

Jednorozměrné pole můžeme ze série získat s využitím vlastnosti `.values`.

```python
data_tfidf.loc["The Fugitive"].values
```

Abychom získali dvourozměrnou matici, můžeme použít metodu `reshape()`. Té dáme parametry `1` a `-1`, čímž získáme dvourozměrné pole.

Hodnota `1` znamená, že chceme mít pole, které má jeden řádek. A hodnota `-1` udělá pole dvourozměrné.

```python
data_tfidf.loc["The Fugitive"].values.reshape(1, -1)
```

Spočítejme nyní podobnost například mezi filmem The Fugitive a Witness

```python
cosine_similarity(data_tfidf.loc["The Fugitive"].values.reshape(1, -1), data_tfidf.loc["Witness"].values.reshape(1, -1))
```

Podobnost je sice poměrně nízká (hodnota je blízká 0), absolutní hodnota nemusí být příliš důležitá. Zkusme ještě spočítat podobnost mezi filmy The Fugitive a Titanic.

```python
cosine_similarity(data_tfidf.loc["The Fugitive"].values.reshape(1, -1), data_tfidf.loc["Titanic"].values.reshape(1, -1))
```

Vidíme, že pro tyto fimy je podobnost větší. Náš systém by tedy nabídl uživateli, kterému se líbil film The Fugitive, film Witness a nikoli film Titanic.

V celé datové sadě se ale může nacházet ještě vhodnější film. Abychom získali obecná data pro doporučení libovolného filmu, použijeme funkci  `cosine_similarity` na celou tabulku `cosine_similarity()`. Tento příkaz spočítá kosinovou podobnost pro každou dvojici filmů. Získáme tedy čtvercovou matici, kde se počet řádků a počet sloupců rovná počtu filmů v původní tabulce.

```python
cosine_similarity_array = cosine_similarity(data_tfidf)
cosine_similarity_array
```

Pro snazší práci s daty opět provedeme převod na tabulku. Názvy sloupců i řádků (index) jsou dané jmény filmů.

```python
cosine_similarity_df = pd.DataFrame(cosine_similarity_array, index=data_tfidf.index, columns=data_tfidf.index)
cosine_similarity_df.head()
```

Matice je symetrická. Pokud nás zajímají podobnosti jednotlivých filmů s filmem The Fugitive, můžeme vyjmut řádek (pomocí `.loc`) nebo sloupec (s využitím hranatých závorek). Jednodušší bude práce se sloupcem, protože budeme chtít data seřadit.

```python
distances_the_fugitive = cosine_similarity_df["The Fugitive"]
distances_the_fugitive
```

Zobrazíme si tedy 10 nejvíce podobných filmů. Nejvíce "podobný" je film "The Rocket: The Legend of Rocket Richard", který ale dějově nemá s filmem The Fugitive nic společného. Podobnost je daná čistě tím, že hlavní postavy obou filmů se jmenují Richard. To bychom mohli vyřešit například tím, že bychom zohlednili zánr filmu. Naopak následující dva filmy The Iceman a The Life of David Gale lze považovat za dobré tipy.

```python
distances_the_fugitive.sort_values(ascending=False).head(10)
```

#### Výběr na základě uživatelského profilu

Pokud uživatel používá nějakou službu delší dobu, budeme určitě vědět o více produktech (např. filmech), které se mu líbily. V takovém případě nebudeme hledat podobnost mezi dvěma produkty, ale mezi uživatelským profilem a produkty, které budou pro uživatele nové (např. filmy, které ještě neviděl). Uvažujme například uživatele (uživatelku), které se líbily firmy Notting Hill, Titanic, The Great Gatsby a The Lovers. To, že se filmy líbily, můžeme poznat podle explicitního (uživatel použil tlačítko "To se mi líbí") nebo implicitního (uživatel zhlédl celý film) feedbacku.

Vytvoříme tedy proměnnou `data_user_profile`, která bude reprezentovat preference uživatele (uživatelky). Ty získáme tak, že spočítáme průměrné hodnoty jednotlivých slov (a dvojic slov) pro výše uvedené filmy. Abychom měli data ve dvourozměrné matici, oipět je třeba použít metodu `reshape()`.

```python
favorite_movies = ["Notting Hill", "Titanic", "The Great Gatsby", "The Lovers"]
data_user_profile = data_tfidf.loc[favorite_movies]
data_user_profile = data_user_profile.mean()
data_user_profile = data_user_profile.values.reshape(1, -1)
data_user_profile
```

Nyní zkusme najít filmy, které budou nejvíce podobného uživatelskému profilu. Nejprve z původní tabulky vyřadíme filmy ze seznamu `favorite_movies`. Poté spočítáme kosinové vzdálenosti mezi uživatelským profilem a jednotlivými filmy. Vznikne matice s jedním sloupcem, který pro každý film udává kosinovou vzdálenost mezi popisem filmu a uživatelským profilem.

```python
data_tfidf_not_seen = data_tfidf.drop(favorite_movies, axis=0)
cosine_similarity_array_not_seen = cosine_similarity(data_tfidf_not_seen, data_user_profile)
cosine_similarity_array_not_seen
```

Data opět převedeme na tabulku, abychom viděli názvy filmů, které náš systém danému uživateli doporučí. Jako indexy použijeme názvy filmů z tabulky `data_tfidf_not_seen`. Nakonec data seřadíme. Vidíme, že náš systém by doporučil filmy Romance & Cigarettes, Four Weddings and a Funeral a Rent.

```python
user_recommendation = pd.DataFrame(cosine_similarity_array_not_seen, index=data_tfidf_not_seen.index, columns=["score"])
user_recommendation.sort_values(by="score", ascending=False).head()
```

#### Kolaborativní filtrování

U kolaborativního filtrování se řídíme preferencemi jednotlivých uživatelů a uživatelek. Využijeme data ze seouboru [user_ratings.csv](user_ratings.csv), který obsahuje uživatelská hodnocení fimů. U hodnocení víme ID uživatele (`userId`), bodové hodnocení filmu (sloupec `rating`) a název filmu (sloupec `title`).

```python
data_ratings = pd.read_csv("user_ratings.csv")
data_ratings = data_ratings[["userId", "rating", "title"]].drop_duplicates(subset=["userId", "title"], keep="last")
data_ratings.head()
```

Pro další práci potřebujeme mít trochu jinou strukturu tabulky. Měli bychom mít názvy filmů jako sloupce, takže jeden řádek tabulky bude představovat všechna hodnocení jednoho uživatele (uživatelky). K tomu můžeme využít funkce `pivot()`. Pozor, nejedná se o funkci `pivot_table()`! Rozdíl mezi `pivot()` a `pivot_table()` je v tom, že `pivot()` neprovádí žádnou agregaci, ale pouze "přeskládá" hodnoty z jednoho sloupce do více sloupců. Z toho důvodu zadáváme funkci `pivot` pouze tři parametry: `index` (názvy řádků - index), `columns` (názvy sloupců) a `values` (hodnoty). Nezadáváme agregační funkci.

```python
data_ratings_pivot = data_ratings.pivot(index="userId", columns="title", values="rating")
data_ratings_pivot
```

Podívejme se nyní na hodnocení uživatele (nebo uživatelky) s id 2. Jeho hodnocení zjistíme jako řádek s id 2. Pozor na to, že sloupce v původní tabulce jsou čísla, musíme tedy do `loc` vložít hodnotu 2 jako číslo, tj. bez uvozovek. Pokud bychom zadali číslo s uvozovkami, modul `pandas` by hledal řetězec 2 a nic by nenašel, protože řetězec 2 je jiná hodnota než číslo 2.

Vidíme, že máme celkem 29 hodnocení a jsou zastoupena jak vysoká, tak nízká hodnocení.

```python
data_ratings_pivot.loc[2].dropna().sort_values()
```

Velkým problémem dat tohoto typu je, že v nich je obrovské množství prázdných hodnot. Pro taková data se používá označení řídká (*sparse*). My si ukážeme velmi jednoduchý přístup k řešení tohoto problému. Nemůžeme nyní ale jen nahradit chybějící hodnoty 0, protože tím bychom zkresili průměrná hodnocení filmu. Využijeme následující postup.

1. Pro každý film spočteme průměrné hodnocení.
1. Od každého hodnocení odečteme průměrné hodnocení.
1. Nyní můžeme prázdné hodnoty nahradit 0, aniž bychom ovlivnili průměrné hodnocení filmů.

```python
avg_ratings = data_ratings_pivot.mean()
avg_ratings
```

```python
data_ratings_pivot_standardized = data_ratings_pivot.sub(avg_ratings, axis=1)
data_ratings_pivot_standardized = data_ratings_pivot_standardized.fillna(0)
data_ratings_pivot_standardized
```

##### Dopočítání hodnot

Podívejme se třeba na hodnocení filmu Pulp Fiction od uživatele s id 1 a na něm si ukážeme, jak vznikly hodnoty v tabulce `data_ratings_pivot_standardized`. Film dostal od uživatele hodnocení 3 body. Nyní vidíme, že má hodnocení -1.197068. Kde se toto číslo vzalo? Rozeberme si postup výpočtu.

```python
data_ratings_pivot.loc[1, "Pulp Fiction (1994)"]
```

V tabulce `data_ratings_pivot_standardized` je hodnocení filmu Pulp Fiction -1.197068403908795.

```python
data_ratings_pivot_standardized.loc[1, "Pulp Fiction (1994)"]
```

Podívejme se na průměrné hodnocení tohoto filmu v původních tabulce `avg_ratings`. To má hodnotu 4.197068403908795.

```python
avg_ratings.loc["Pulp Fiction (1994)"]
```

K číslu se dopočítáme, pokud vezmeme hodnocení 3 a odečteme od něj průměrné hodnocení filmu. Tím získáme hodnotu -1.197068403908795, což je stejná hodnota, jaká byla v 

```python
3 - 4.197068403908795
```

##### Podobnost uživatelů

Naším cílem nyní bude vyhledat k našemu uživateli č. 1 uživatel a uživatelky, kteří jsou mu (jí) nejvíce podobní. K výpočtu podobnosti opět můžeme využít kosinovou vzdálenost. Jednou z jejích hodnot je, že jí nevadí obrovské množství chybějících hodnot, jimi ovlivněná není. Pro výpočet podobnosti uživatelů využijeme tabulku se standardizovanými hodnoceními, tj. tabulku `data_ratings_pivot_standardized`. Výsledná data opět převedeme na tabulku.

```python
similarities = cosine_similarity(data_ratings_pivot_standardized)
similarities = pd.DataFrame(similarities, index=data_ratings_pivot_standardized.index, columns=data_ratings_pivot_standardized.index)
similarities.head()
```

Zobrazíme si, kteří uživatelé do 1 nejvíce podobných uživatelů patří. Nemáme žádný klíč k tomu, kolik uživatelů vybrat, zobrazíme tady 10 nejbližších. Do nich se počítá i sám uživatel 1, protože pro něj je kosinová podobnost 1 (tj. nejvyšší možná podobnost).

```python
n_users = 10
user_1_similarities = similarities.loc[2]
user_1_similarities = user_1_similarities.sort_values(ascending=False)
user_1_similarities.head(n_users)
```

Pomocí `iloc[]` nyní vyberu `n_users` nejvíce podobných uživatelů, samotného uživatele 1 už vyřadíme.

```python
similar_users = user_1_similarities.iloc[1:n_users+1].index
similar_users

```

V tabulce níže máme hodnocení filmů od 10 vybraných uživatelů. Aby bylo hodnocení relevantní, vybereme pouze filmy, které byly ohodnoceny alespoň od 3 těchto uživatelů. K tomu využijeme metodu `dropna()`, kterou již známe, ale tentokrát ji použijeme poměrně nezvykle.

- Jako hodnotu parametru `axis` použijeme `1`, tj. chceme, aby metoda odebírala sloupečky, nikoli řádky. My totiž z výběru chceme vyřadit filmy, které mají málo hodnocení, a filmy jsou v tabulce jako sloupce.
- Nechceme ale ponechat pouze filmy hodnocené od všech vybraných uživatelů, to by nám v tabulce moc filmů nezbylo. Proto použijeme parametr `tresh=3` (zkratka od anglického výrazu Treshold), který ponechá sloupečky, které mají alespoň tři hodnoty.

```python
data_ratings_pivot_similar = data_ratings_pivot.loc[similar_users]
data_ratings_pivot_similar = data_ratings_pivot_similar.dropna(axis=1, thresh=3)
data_ratings_pivot_similar
```

Nakonec spočítáme průměrná hodnocení pro jednotlivé filmy a seřadíme je. U těchto filmů tedy můžeme předpokládat větší šanci, že by se mohly líbit i uživateli 2.

```python
average_ratins_by_similar_users = data_ratings_pivot_similar.mean().sort_values(ascending=False)
average_ratins_by_similar_users.head(20)
```

##### Odebrání filmů, které uživatel již viděl

Většině uživatelů asi nemá smysl doporučovat filmy, které už viděli, protože dají přednost něčemu novému. Můžeme tedy z našeho seznamu odebrat filmy, které už uživatel 2 ohodnotil. Nejprve si zjistíme, které to jsou. Pokud vybereme z tabulky hodnocení uživatele 2 a odebereme prázdné hodnoty, pak indexy vybrané série budou představovat filmy, které uživatel již viděl. Výsledek si uložíme do proměnné `seen_by_user_2`.

```python
seen_by_user_2 = data_ratings_pivot.loc[2].dropna().index
seen_by_user_2
```

Nakonec tyto filmy odebereme z filmů, které chceme doporučit. Pomocí dotazu vybereme ze série `average_ratins_by_similar_users` položky, jejichž index (i u této série jsou index názvy filmů) není v  `seen_by_user_2`

```python
average_ratins_by_similar_users = average_ratins_by_similar_users[~average_ratins_by_similar_users.index.isin(seen_by_user_2)]
average_ratins_by_similar_users.head(20)
```

### Zdroje a odkazy

- [Understanding Cosine Similarity in Python with Scikit-Learn](https://memgraph.com/blog/cosine-similarity-python-scikit-learn)
- [What is the 'More Like This' section?](https://help.imdb.com/article/imdb/discover-watch/what-is-the-more-like-this-section/GPE7SPGZREKKY7YN?ref_=cons_tt_rec_lm#)
- [Beginner Tutorial: Recommender Systems in Python](https://www.datacamp.com/tutorial/recommender-systems-python)
- [Build a Recommendation Engine With Collaborative Filtering](https://realpython.com/build-recommendation-engine-collaborative-filtering/)
- [How Recommendation Systems Tackle the Cold Start Problem](https://www.yusp.com/blog-posts/cold-start-problem/)
- [Overcoming Echo Chambers in Recommendation Systems (Using Movie Ratings)](https://towardsdatascience.com/overcoming-echo-chambers-in-recommendation-systems-using-movie-ratings-3686f50fc053) (placená část Towards Data Science)
- [Introduction to recommender systems](https://towardsdatascience.com/introduction-to-recommender-systems-6c66cf15ada)
- [Recommender Systems — A Complete Guide to Machine Learning Models](https://towardsdatascience.com/recommender-systems-a-complete-guide-to-machine-learning-models-96d3f94ea748)

### Cvičení

::exc[excs/filmy]
