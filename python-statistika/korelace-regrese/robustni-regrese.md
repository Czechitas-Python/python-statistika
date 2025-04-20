## Odlehlá pozorování

V případě regrese často můžeme narazit na problémy, které souvisí s daty, které máme k dispozici. Uvažujme například dataset s extrémě úspěšnými farmami [avocado_farming_data_outlier.csv](avocado_farming_data_outlier.csv). 

Odlehlá pozorování mohou mít mnoho příčin. Na některé farmě můžou být prostě farmáři pracovitější, takže mají větší úrodu.

::fig[]{src=assets/ada_06.png}

Alternativně se může jednat o chyby při zadávání dat.

::fig[]{src=assets/ada_09.png}

Případně i o podvody.

::fig[]{src=assets/ada_07.png}


```python
data = pd.read_csv("avocado_farming_data_outlier.csv")
data.head()
```


Vytvoříme si regresní graf. Na grafu jsou vidět body, které označujeme jako odlehlá pozorování. Tyto body ovlivňují sklon regresní funkce, sklon je mnohem menší, než by měl být.


```python
g = sns.regplot(data=data, x="rainfall", y="avocado_yield", line_kws={"color": "red"}, ci=None)
```

Podívejme se na krabicový graf.

```python
sns.boxplot(data, y="avocado_yield", whis=[5, 95])
```

Odlehlá pozorování můžeme z dat vyřadit, např. s využitím tzv. Cookovy vzdálenosti. My využijeme postup označovaný jako robustní regrese. Robustní regrese přikládá menší váhu odlehlým pozorováním, takže výrazně snižuje jejich vliv na regresní funkci.


```python
g = sns.regplot(data=data, x="rainfall", y="avocado_yield", line_kws={"color": "red"}, ci=None, robust=True)
```


```python
formula = "avocado_yield ~ rainfall"
mod = smf.rlm(formula=formula, data=data)
res = mod.fit()
res.summary()
```

U robustní regrese většinou nepočítáme koeficient determinace. Je to z důvodu, že tento koeficient je počítaný jako druhá mocina rozdílu mezi predikovanou a skutečnou hodnotou. Odlehlá pozorování by tedy hodnotu koeficientu výrazně navýšila, zvláště v případě, kdy bychom na tato pozorování brali menší ohled a více se zaměřili na "běžné" hodnoty. Příklady měřítek, která mohou být použita k vyhodnocení regresního modelu, najdeš [v tomto článku](https://towardsdatascience.com/ways-to-evaluate-regression-models-77a3ff45ba70). Kupříkladu :term{cs="průměrná absolutní chyba" en="Mean Absolute Error (MAE)"} je absolutní hodnota rozdílu mezi predikovanou a skutečnou hodnotou. Absolutní hodnota zařídí, že na chybu je pohlíženo stejně, ať už model predikoval příliš vysokou nebo naopak příliš nízkou hodnotu. Protože tento ukazatel nepoužívá druhou mocninu, nedává odlehlým pozorováním nadproporciálně větší význam.

```python
tools.eval_measures.meanabs(data["avocado_yield"], res.fittedvalues, axis=0)
```

A to je konec našeho příběhu.

::fig[]{src=assets/ada_08.png}


### Cvičení

::exc[excs/robustni-regrese]
