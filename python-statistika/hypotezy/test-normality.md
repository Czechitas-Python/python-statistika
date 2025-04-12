## Testy normality

Teoreticky je možné, že nějaká data, se kterými pracujeme, mají normální rozdělení. To se může často hodit. Některé statistické metody totiž mají **předpoklady**, které by měly být splněny, než je použijeme. Pokud nejsou předpoklady splněné, je vhodné použít alternativu. Například některé statistické testy hypotéz jsou označovány jako parametrické (*parametric*) a jiné jako neparametrické (*nonparametric*). Parametrické testy většinou předpokládají, že data mají normální rozdělení, neparametrické testy to nevyžadují.

Statistické testy jsou různě citlivé na odchylky od normality. Napříkald t-test je poměrně *robustní* a funguje dobře i pro data, která nemají normální rozdělení, pokud máme *dostatečné množství* dat (tj. minimálně vyšší desítky). Jiné testy jsou bohužel citlivější a v případě jejich využití bychom mohli dojít k nesprávnému závěru ohledně platnosti nulové hypotézy.

Pokud nevíme, zda jsou předpoklady splněné, můžeme je otestovat. Testování předpokladů probíhá stejně, jako v případě samotného testu zkoumaného problému. Začneme tedy formulací hypotéz. V případě, že testujeme, zda mají data normální rozdělení (často se říká "normalita data"), formulujeme hypotézy takto:

- H0: Data mají normální rozdělení.
- H1: Data nemají normální rozdělení.

K otestování můžeme využít řadu testů. Součásti `scipy` je několik testů. Například funcke `normaltest()` nebo `shapiro()`.

::fig[]{src=assets/gpt_04.png}


```python
stat_pripravek, p_pripravek = stats.shapiro(data['Pripravek'])
print(p_pripravek)
stat_placebo, p_placebo = stats.shapiro(data['Placebo'])
print(p_placebo)
```

Ani v jednom případě není p-hodnota méně než 0.05, hypotézu normality jsme tedy nezamítli.

V realitě často ale narazíme na případ, kde předpoklad normality nebude splněný. Co v takovém případě máme dělat? Řešení je překvapivě jednoduché: stačí zvolit jiný test. Testům, které nevyžadují normalitu, se obecně říká neparametrické (*non parametric tests*). Pokud chceme porovnat dva průměry, můžeme třeba použít Mann-Whitney test.

::fig[]{src=assets/gpt_05.png}

```python
statistic, p_value = stats.mannwhitneyu(data['Pripravek'], data['Placebo'], alternative='greater')
print(p_value)
```

I Mann-Whitney test potvrdil královně Adě účinnost přípravku.

A to je konec našeho příběhu...

::fig[]{src=assets/ada_10.png}

### Cvičení

::exc[excs/platy-akademiku]
::exc[excs/skoly]
::exc[excs/hodnoceni-hotelu]
