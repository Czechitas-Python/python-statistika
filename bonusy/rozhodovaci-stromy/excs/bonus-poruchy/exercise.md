---
title: Bonus – Poruchy
demand: 3
---

Vrať se k datům o poruchách ze souboru [predictive_maintenance.csv](assets/predictive_maintenance.csv) a pokus se pomocí rozhodovacího stromu klasifikovat stavy stroje.

Použij sloupce Type, Air temperature [K], Process temperature [K], Rotational speed [rpm], Torque [Nm], Tool wear [min] jako vstupní proměnné a Failure Type jako výstupní proměnnou. Sloupec `Type` obsahuje kategoriální hodnoty, použij teddy `OneHotEncoder`.

Sestav strom, který bude predikovat hodnotu sloupce `Failure Type` - tj. to, jestli dojde k selhání a případně k jakému. Omez hloubku stromu na 5, aby byl strom stále celkem přehledný a mohl být například vložen do servisního manuálu.

Níže máš připravené seznamy sloupců, názvy proměnných odpovídají tomu, co jsme používali v lekci.

```py
categorical_columns = ["Type"]
numeric_columns = ["Air temperature [K]", "Process temperature [K]", "Rotational speed [rpm]", "Torque [Nm]", "Tool wear [min]"]
```

### Nápověda

Pokud bys chtěl(a) do stromu přidat i názvy features, je to trochu složitější. Je třeba spojit názvy sloupců, které vzešly z `OneHotEncoder`, a názvy numerických sloupců, které jsou původní. Níže je kód který toto provede.

```py
feature_names = list(ohe.get_feature_names_out()) + list(numeric_columns)
feature_names
```

Níže je příklad toho, jak potom použít proměnnou `feature_names` při generování stromu.

```py
dot_data = StringIO()
export_graphviz(clf, out_file=dot_data, filled=True, class_names=clf.classes_, feature_names=feature_names)
graph = pydotplus.graph_from_dot_data(dot_data.getvalue())
Image(graph.create_png())
```

Video s řešením je [zde](https://youtu.be/K3RU6R-2sGs). Text k oběma příkladům je [zde](reseni.ipynb).

:::solution

Řešení viz reseni.ipynb.

:::
