## Porovnání z-testu a t-testu

V předcházejících článcích jsme rozebírali [z-test](z-test.md) a [t-test](t-test.md). Oba testy slouží k otestování hypotézy o střední hodnotě a liší se pouze předpokladem o znalosti rozptylu. Nabízí se ale otázka, k čemu vlastně máme dva testy? **Jakou výhodu vlastně přináší znalost rozptylu?** Na to se nyní podíváme.

U obou dvou testů můžeme testovat hypotézy na stejných hladinách významnosti. Ať už tedy provedeme test pomocí z-testu nebo t-testu, můžeme si předem stanovit, že pravděpodobnost chyby 1. druhu (neoprávněného zamítnutí ![](assets/formula-inline-2a20eef8c291.png)) je například ![](assets/formula-inline-a20f4290bcdd.png). Neznalost rozptylu se ale projeví **v pravděpodobnosti chyby 2. druhu**, neboli v **síle testu**. V případě využití t-testu máme větší pravděpodobnost, že nezamítneme neplatnou ![](assets/formula-inline-2a20eef8c291.png).

Ukažme si to na příkladu oboustranného testu. Předpokládejme stejné hypotézy jako v předchozích článcích, tj.

* ![](assets/formula-inline-e40f0d0b340a.png),
* ![](assets/formula-inline-286d8ada6e12.png).

Vygenerujeme si soubor pomocí generátoru náhodných čísel. Ten nám vygeneruje čísla s požadovanými vlastnostmi. Budeme chtít data se střední hodnotou ![](assets/formula-inline-165ca384c811.png) a směrodatnou odchylkou ![](assets/formula-inline-6d02e7df753c.png). Víme tedy, že nulová hypotéza neplatí. Pokud tedy nulovou hypotézu při testu zamítneme, bude náš výsledek správný. V opačném případě se dopouštíme chyby 2. druhu.

::fig[]{src=assets/z-t-test-porovnani_t-test-random-gen.png}

Na obrázku níže máte vygenerovaná data a výsledky provedených testů.

::fig[]{src=assets/z-t-test-porovnani_t-test-vs-z-test.png}

p-hodnota z-testu je 0,0196, p-hodnota t-testu je 0,1405. Na hladině významnosti ![](assets/formula-inline-a20f4290bcdd.png) bychom tedy nulovou hypotézu zamítli pouze při použití z-testu. V případě použití t-testu bychom se dopustili chyby 2. druhu.

Soubor s výpočty si můžete stáhnout [zde](assets/z-t-test-porovnani_t-test1.xlsx).

Na základě jednoho příkladu ale nejde vyvozovat nějaké obecnější závěry. Zkusme tedy komplexnější experiment. Využijeme soubor náhodných čísel, který jsme vytvořili pro [analýzu síly testu z-testu](jak-vznikaji-chyby.md).

V souboru jsme měli náhodné výběry se střední hodnotou od ![](assets/formula-inline-e191f8418504.png) do ![](assets/formula-inline-226b59d2f533.png) a směrodatnou odchylkou ![](assets/formula-inline-16230539c050.png). Velikostí kroku mezi dvěma středními hodnotami byla 0,025. Rozsah náhodného výběru byl 20 a pro každou střední hodnotu jsme generovali 100 náhodných výběrů. Budeme pro každý soubor testovat hypotézu ![](assets/formula-inline-071064c6f343.png) na hladině významnosti ![](assets/formula-inline-a20f4290bcdd.png), a to jak pomocí z-testu, tak i pomocí t-testu.

Výsledek experimentu je na obrázku níže. Obě křivky zobrazují podíl správných výsledků (tj. zamítnutí ![](assets/formula-inline-071064c6f343.png)) v souboru provedených testů. Pro z-test je požita zelená barva, pro t-test červená. Červená křivka se nachází pod zelenou, což značí, že z-test je úspěšnější než t-test.

::fig[]{src=assets/z-t-test-porovnani_z-test-vs-t-test-test-power1.png}.

Soubor s experimenty si můžete stáhnout [zde](assets/z-t-test-porovnani_z-test-vs-t-test.xlsx).
