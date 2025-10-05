## Instalace

Pokud zatím nemáš nainstalovaný Python a Visual Studio Code, postupuj dle instrukcí z úvodního kurzu, konkrétně projdi tyto kapitoly:
* [Instalace jazyka Python](https://kodim.cz/programovani/uvod-do-progr-1/priprava/jazyky-nastroje/instalace-python)
* [Rozšíření VS Code pro Python](https://kodim.cz/programovani/uvod-do-progr-1/priprava/jazyky-nastroje/instalace-rozsireni-vscode)

V rámci kurzu budeme používat modul pro práci s daty `pandas`, moduly pro vizualizace `matplotlib` a `seaborn` a moduly pro vědecké výpočty `statsmodels` a `scipy`. Doporučuji též instalaci modulů `jupyterlab` a `ipykernel` pro práci s Jupyter notebooky.

Spusť Visual Studio Code a otevřete si nový terminál (z horní lišty vyberte **Terminal → New Terminal**).

Pokud používáš Windows, napiš do terminálu následující příkaz a stiskni **Enter**:

```shell
pip install pandas
pip install matplotlib
pip install seaborn
pip install statsmodels
pip install scipy
pip install jupyterlab
pip install ipykernel
```

Pokud používáš MacOS nebo Linux, napiš následující příkaz:

```shell
pip3 install pandas
pip3 install matplotlib
pip3 install seaborn
pip3 install statsmodels
pip3 install scipy
pip3 install jupyterlab
pip3 install ipykernel
```

Instalaci můžeš ověřit tím, že si otevřeš nový program, vložíš do něj následující řádek a spustíš ho. Pokud program nevypíše nic, instalace proběhla korektně. Pokud vypíše nějakou chybu, instalace se pravděpodobně nezdařila.

```py
import pandas
```
