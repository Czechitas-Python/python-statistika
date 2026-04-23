## Levostranný z-test

Uvažujme, že chceme ověřit hypotézu o střední hodnotě nějakého náhodného výběru, k čemuž můžeme využít z-test. U [oboustranného z-testu](z-test.md) je alternativní hypotéza zadaná nerovností, tj. alternativní hypotéza tvrdila, že střední hodnota náhodného výběru je *odlišná* od teoretické (testované) střední hodnoty. Nyní rozebereme další variantu - levostranný test, kde ověřujeme, zda je skutečná střední hodnota dat **menší** než hypotetická hodnota.

Uvažujme upravené zadání našeho příkladu: *Máme zařízení, které vyrábí součástku určité délky. Směrodatná odchylka délky součástek v důsledku chybovosti zařízení je 0,9 mm. Požadovaná délka součástky je 190 mm. Pracovník nemůže zadat k výrobě delší součástku, v důsledku chybného zadání ale mohou být vyráběny kratší součástky. Ověřte, zda bylo zařízení správně nastaveno.*

V tomto příkladu uvažujeme, že průměrná délka součástky může být menší než 190 mm, neuvažujeme ale, že by mohla být větší. Formulujme tedy hypotézy:

* Střední hodnota statistického souboru je 190 mm. (![](assets/formula-inline-22aed48275fb.png))
* Střední hodnota statistického souboru je menší než 190 mm. (![](assets/formula-inline-19fe951f827a.png).)

Statistika zůstává stejná:

::fig[]{src=assets/formula-block-fcd2715fa2ce.png}

přičemž ![](assets/formula-inline-c244ddb99195.png) je průměr našeho vzorku, ![](assets/formula-inline-2d86000d76af.png) je teoretická (testovaná) střední hodnota, ![](assets/formula-inline-c06f5b649b91.png) je směrodatná odchylka základního souboru a ![](assets/formula-inline-4ed0a7331160.png) je rozsah náhodného výběru. Statistika má opět normované normální rozdělení.

##  Kam umístit kritický obor

Naše alternativní hypotéza ![](assets/formula-inline-df2759fb3cd3.png) tvrdí, že skutečná střední hodnota je menší než 190 mm. Jestliže ![](assets/formula-inline-df2759fb3cd3.png) platí, pak bude s větší pravděpodobností průměr vzorku menší než 190. Nižší hodnota ![](assets/formula-inline-c244ddb99195.png) než 190 znamená, že rozdíl ![](assets/formula-inline-284b811acfb0.png) je záporný. Protože ![](assets/formula-inline-c06f5b649b91.png) a ![](assets/formula-inline-4ed0a7331160.png) jsou vždy kladné, záporná hodnota tohoto rozdílu znamená, že i hodnota statistiky je záporná. Z toho plyne, že záporné hodnoty statistiky hovoří spíše ve prospěch alternativní hypotézy. **Čím je hodnota statistiky menší, tím větší tendenci máme k zamítnutí nulové hypotézy.**

## Hladina významnosti a možné závěry

Kritické obory pro hladinu významnosti ![](assets/formula-inline-a20f4290bcdd.png), ![](assets/formula-inline-3eb970ad22f4.png) a ![](assets/formula-inline-a81c30a0f257.png) naleznete na obrázku níže.

::fig[]{src=assets/z-test-levostranny_kritcke-obory.png}

Je důležité si uvědomit, že plocha kritického oboru je stále ![](assets/formula-inline-9dfef183d195.png). To způsobuje hranice kritického oboru (kritická hodnota) pro stejnou hladinu významnosti je "více vpravo" oproti kritickému oboru oboustranného testu (porovnejte si to s kritickými obory u [oboustranného z-testu](z-test.md)).

## Výpočet z-testu v Excelu

Podívejme se nyní, jak by se z-test provedl v aplikaci Microsoft Excel. Testovat budeme na ![](assets/formula-inline-a20f4290bcdd.png). Využijeme list, který jsme použili pro oboustranný test, provedeme pouze několik modifikací.

::fig[]{src=assets/z-test-levostranny_z-test-left1.png}

Máme pouze jednu hranici kritického oboru, která je daná vzorcem

```text
=NORM.S.INV(D6)
```

Python alternativa:

```python
from scipy.stats import norm

critical_value = norm.ppf(alpha)
```

Kritický obor vyjádřený intervalem má tvar:

::fig[]{src=assets/formula-block-0b9330a87425.png}

kde ![](assets/formula-inline-508dc4f4a41c.png) označujeme pětiprocentní kvantil normovaného normálního rozdělení. 

Dále upravíme vzorec pro výpočet p-hodnoty

```text
=NORM.S.DIST(D9;PRAVDA)
```

Python alternativa:

```python
from scipy.stats import norm

p_value = norm.cdf(z_stat)
```

Hodnota statistiky je -2,0020, tj. statistika leží v kritickém oboru. V tomto případě tedy můžeme konstatovat, že jsme na hladině významnosti ![](assets/formula-inline-a20f4290bcdd.png) prokázali, že střední hodnota délky součástky je menší než 190. Zařízení tedy bylo nastaveno chybně.

Výsledek potvrzuje i p-hodnota, která je nižší než hladina významnosti. Porovnání je na obrázku níže. p-hodnota je plocha pod hustotou statistiky od minus nekonečna až po vypočtenou hodnotu statistiky.

::fig[]{src=assets/z-test-levostranny_p-hodnota.png}

K provedení testu můžeme využít i funkci Z.TEST. Tato funkce standardně vrací p-hodnotu pravostranného testu. Protože my provádíme levostranný test, p-hodnotu získáme odečtením výsledku funkce Z.TEST od jedničky.

```text
=1-Z.TEST(A1:A20;D5;D4)
```

Python alternativa:

```python
from scipy.stats import norm

z_stat = (sample.mean() - mu0) / sigma * np.sqrt(len(sample))
p_value = norm.cdf(z_stat)
```

Pokud bychom odečtení od jedničky neprovedli, získali bychom plochu zelené oblasti na obrázku níže. Protože provádíme levostranný test, chceme znát plochu pod funkcí hustoty nalevo od hodnoty statistiky. Ta je na obrázku vyznačena modře.
