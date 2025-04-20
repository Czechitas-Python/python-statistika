## Odlehlá pozorování

V případě regrese často můžeme narazit na problémy, které souvisí s daty, které máme k dispozici. Uvažujme například dataset s extrémě úspěšnými farmami [avocado_farming_data_outlier.csv](avocado_farming_data_outlier.csv). 

Odlehlá pozorování mohou mít mnoho příčin. Na některé farmě můžou být prostě farmáři pracovitější, takže mají větší úrodu.

::fig[]{src=assets/ada_06.png}

Alternativně se může jednat o chyby při zadávání dat.

::fig[]{src=assets/ada_09.png}

Případně i o podvody.

::fig[]{src=assets/ada_08.png}


```python
data = pd.read_csv("avocado_farming_data_outlier.csv")
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>rainfall</th>
      <th>avocado_yield</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1099.342831</td>
      <td>278.163385</td>
    </tr>
    <tr>
      <th>1</th>
      <td>972.347140</td>
      <td>208.556978</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1129.537708</td>
      <td>293.655367</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1304.605971</td>
      <td>308.132332</td>
    </tr>
    <tr>
      <th>4</th>
      <td>953.169325</td>
      <td>229.541519</td>
    </tr>
  </tbody>
</table>
</div>



Vytvoříme si regresní graf. Na grafu jsou vidět body, které označujeme jako odlehlá pozorování. Tyto body ovlivňují sklon regresní funkce, sklon je mnohem menší, než by měl být.


```python
g = sns.regplot(data=data, x="rainfall", y="avocado_yield", line_kws={"color": "red"}, ci=None)
```


    
![png](statistika-2_files/statistika-2_27_0.png)
    


Podívejme se na krabicový graf.


```python
sns.boxplot(data, y="avocado_yield", whis=[5, 95])
```




    <Axes: ylabel='avocado_yield'>




    
![png](statistika-2_files/statistika-2_29_1.png)
    


Odlehlá pozorování můžeme z dat vyřadit, např. s využitím tzv. Cookovy vzdálenosti. My využijeme postup označovaný jako robustní regrese. Robustní regrese přikládá menší váhu odlehlým pozorováním, takže výrazně snižuje jejich vliv na regresní funkci.


```python
g = sns.regplot(data=data, x="rainfall", y="avocado_yield", line_kws={"color": "red"}, ci=None, robust=True)
```


    
![png](statistika-2_files/statistika-2_31_0.png)
    



```python
formula = "avocado_yield ~ rainfall"
mod = smf.rlm(formula=formula, data=data)
res = mod.fit()
res.summary()
```




<table class="simpletable">
<caption>Robust linear Model Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>    <td>avocado_yield</td>  <th>  No. Observations:  </th> <td>    25</td>
</tr>
<tr>
  <th>Model:</th>                 <td>RLM</td>       <th>  Df Residuals:      </th> <td>    23</td>
</tr>
<tr>
  <th>Method:</th>               <td>IRLS</td>       <th>  Df Model:          </th> <td>     1</td>
</tr>
<tr>
  <th>Norm:</th>                <td>HuberT</td>      <th>                     </th>    <td> </td>  
</tr>
<tr>
  <th>Scale Est.:</th>            <td>mad</td>       <th>                     </th>    <td> </td>  
</tr>
<tr>
  <th>Cov Type:</th>              <td>H1</td>        <th>                     </th>    <td> </td>  
</tr>
<tr>
  <th>Date:</th>           <td>Sun, 20 Oct 2024</td> <th>                     </th>    <td> </td>  
</tr>
<tr>
  <th>Time:</th>               <td>21:05:48</td>     <th>                     </th>    <td> </td>  
</tr>
<tr>
  <th>No. Iterations:</th>        <td>12</td>        <th>                     </th>    <td> </td>  
</tr>
</table>
<table class="simpletable">
<tr>
      <td></td>         <th>coef</th>     <th>std err</th>      <th>z</th>      <th>P>|z|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th> <td>  -26.5897</td> <td>   36.611</td> <td>   -0.726</td> <td> 0.468</td> <td>  -98.346</td> <td>   45.166</td>
</tr>
<tr>
  <th>rainfall</th>  <td>    0.2741</td> <td>    0.037</td> <td>    7.376</td> <td> 0.000</td> <td>    0.201</td> <td>    0.347</td>
</tr>
</table><br/><br/>If the model instance has been used for another fit with different fit parameters, then the fit options might not be the correct ones anymore .



U robustní regrese většinou nepočítáme koeficient determinace. Je to z důvodu, že tento koeficient je počítaný jako druhá mocina rozdílu mezi predikovanou a skutečnou hodnotou. Odlehlá pozorování by tedy hodnotu koeficientu výrazně navýšila, zvláště v případě, kdy bychom na tato pozorování brali menší ohled a více se zaměřili na "běžné" hodnoty. Příklady měřítek, která mohou být použita k vyhodnocení regresního modelu, najdeš [v tomto článku](https://towardsdatascience.com/ways-to-evaluate-regression-models-77a3ff45ba70). Kupříkladu průměrná absolutní chyba (*Mean Absolute Error (MAE)*) je absolutní hodnota rozdílu mezi predikovanou a skutečnou hodnotou. Absolutní hodnota zařídí, že na chybu je pohlíženo stejně, ať už model predikoval příliš vysokou nebo naopak příliš nízkou hodnotu. Protože tento ukazatel nepoužívá druhou mocninu, nedává odlehlým pozorováním nadproporciálně větší význam.

::fig[]{src=assets/ada_08.png}


```python
tools.eval_measures.meanabs(data["avocado_yield"], res.fittedvalues, axis=0)
```

# Cvičení

## Robustní regrese

Využij robustní regresi na dataset o kvalitě betonu a porovnej, zda došlo ke zmenám hodnot regresních koeficientů.
