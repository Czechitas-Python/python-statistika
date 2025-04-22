## Čtení na doma: Hypotézy a regrese

S regresí souvisí řada testů statistických hypotéz. Jedním z nich je test statistické významnosti regresního koeficientu. Ten se hodí hlavně v případě, kdy máme koeficientů více a zajímá nás, které má smysl v modelu používat.

Test má následující hypotézy:

- H0: Koeficient je statisticky nevýznamný.
- H1: Koeficient je statisticky významný.

p-hodnoty testů pro jednotlivé koeficienty najdeš ve výstupu metody `res.summary()`.

::fig[]{src=assets/regression_summary.png}

Pokud je p-hodnota testu méně než 0.05, můžeme tedy koeficient označit jako statisticky významný. p-hodnotu testu najdeme ve sloupci `P>|t|`. p-hodnotu máme pro každý koeficient zvlášť. V našem případě platí, že koeficient `Intercept` je statisticky nevýznamný a všechny ostatní koeficienty jsou statisticky významné.

Test předpokládá normalitu reziduí, tj. rozdílu mezi odhadovanou a pozorovanou hodnotou. Výsledek testu normality reziduí můžeme najít v tabulce také, a to konkrétně v řádku `Jarque‑Bera (JB)` a `Omnibus`. Oba testy se používají k ověření normality reziduí a mají hypotézy:

- H0: Hypotézy mají normální rozdělení.
- H1: Hypotézy nemají normální rozdělení.

p-hodnotu testů najdeme v závorce `Prob()`. Pokud testujeme na běžně používané hladině významnosti 5 %, platí, že pro číslo menší než 0.05 hypotézu normality zamítáme a v opačném případě ji nezamítáme.
