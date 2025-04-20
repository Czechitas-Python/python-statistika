## Kvantily a funkce hustoty

Čast používaný pojem ve statistice je kvantil. Medián je číslo, pro které platí, že polovina hodnot v daném souboru je menší a polovina je větší. Podobné číslo bychom ale našli i pro jiná procenta. Uvažujme například tzv. dolní kvartil. Jedná se číslo, pro které platí, že 25 % hodnot v soubou je menších (a tím pádem 75 % je větších). Pro horní kvartil je to naopak, 75 % je menších a 25 % větších. Obecně takovým číslům říkáme **kvantil** (*quantile*).

Na grafu vidíme:

- Medián (čára uprostřed).
- Kvartily (tvoří barevný obdelník, dolní hranice obdélníku je dolní kvartil a horní hranice obdélníku horní kvartil).
- Poslední jsou čáry označené jako `whis` (od slova whisker - kočičí vousy). Ty zobrazují rozsah ještě větší části hodnot. Nastavme ho tak, aby označoval 90 % všech hodnot, tj. hodnoty mezi 5%ním a 95%ním kvantilem.

```python
import matplotlib.pyplot as plt
import seaborn as sns
sns.boxplot(data)
```

Histogram je graf, který se skládá ze sloupců. V případě bodů z testu vidíme na vodorovné ose získané body a výška sloupce udává, kolik studentů a studentek má body v daném intervalu.

```python
sns.histplot(data=data)
```

Použijme nyní funkci `displot` a nastavme parametr `kde=True`. Nyní v grafu vidíme i čáru, která je označovaná jako odhad funkce hustoty (*density function*). Funkci hustoty můžeme použít podobně jako histogram, ale s tím rozdílem, že funkce hustoty nám "prozradí" procento hodnot, které se nachází v libovolném intervalu.

```python
sns.displot(data=data, kde=True)
```

Funkci hustoty můžeme sestrojit (odhadnout) z nějakých dat. Existují ale tzv. statistická rozdělení (*statistical distribution*), což jsou v podstatě vzorečky pro funkci hustoty.

Většina funkcí hustoty má parametry, pomocí kterých můžeme měnit tvar nebo polohu funkce. Nejznámějším statistickým rozdělením je :term{cs="normální rozdělení" en="normal distribution"}. Normální rozdělení má spousta "jevů", které se vyskytují v přírodě, například délka, výška nebo hmotnost živé tkáně atd. Používá se také ve financích na oceňování některých cenných papírů. Normální rozdělení mají často i chyby měření při experimentech.

Normální rozdělení má dva parametry - střední hodnotu a rozptyl. Střední hodnota určuje, kde se nachází vrchol distribuční funkce, a rozptyl určuje "šířku" distribuční funkce. Rozptyl tedy říká, jak daleko jsou hodnoty rozptýlené od střední hodnoty.

*Poznámka*: Hustota normáního rozdělení má dvě důležité vlastnosti:

- Je symetrická. To, jak je rozdělení symetrické, měříme pomocí ukazatele označovaného jako šikmost (*skewness*).
- Normální rozdělení má relativně málo "odlehlých pozorování". Platí pravidlo "6 sigma". Pokud bychom vytvořili interval, kde minimum je střední hodnota rozdělení minus 3 směrodatné odchylky (*sigma*) a maximum je střední hodnota rozdělení plus 3 směrodatné odchylky, obsahuje tento interval přes 99 % hodnot. Toto měříme pomocí ukazatele označovaného jako špičatost (*kurtois*).

Kromě normálního existuje řada dalších statistických rozdělení (např. exponenciální, rovnoměrné atd.). Existuje i skupina rozdělení, která se používá jen pro celá čísla.
