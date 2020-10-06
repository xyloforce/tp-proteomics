# tp-proteomics

## Mise en place

### Méthodologie

Vous forkerez le présent "repository" pour vous permettre de sauvegarder votre travail.
Vous éditerez ce fichier `README.md` pour répondre aux questions dans les encarts prévus à cet effet et inserer les figures que vous aurez générées.

### Ressources

Seul le langage Python (v3.X) est requis pour ce travail.
Il vous est conseillé d'installer un environnement virtuel python pour installer les libraries requises independamment de votre systèmes d'exploitation.

* Le systême de gestion de paquets [Conda](https://docs.conda.io/en/latest/) est très pratique et disponible pour la plupat des systèmes d'exploitation. Une version légère suffisante pour nos besoin est téléchargeable [ici](https://docs.conda.io/en/latest/miniconda.html)
* Si vous disposez d'un interpreteur python 3.X installé sur votre systême [virtualenv](https://docs.python.org/3/library/venv.html) est désormais "built-in".

#### Procédure conda

Depuis le repertoire de votre repository Git, installez le package scipy et lancez jupyter.

```sh
$PATH_TO_CONDA_DIR/bin/conda install -c conda-forge scipy notebook
$PATH_TO_CONDA_DIR/bin/jupyter notebook
```

Jupyter est une environnement de type notebook permettant l'execution de code python dans ces cellules avec une persitance des variables entre chaque evaluation de cellule. Jupyter fournit nativement le support de la librarie graphique matplotlib.

#### Procédure virtualenv


#### Test de l'installation

Dans l'inteface de jupyter, creez un nouveau fichier notebook (*.ipynb).
Dans la première cellule 

```python
%matplotlib nbagg
import matplotlib.pyplot as plt
import numpy as np
from scipy.stats import norm
```
La première ligne sert à activer le rendu graphique, pour tout le fichier notebook.
```python
fig, ax = plt.subplots()
x = np.linspace(norm.ppf(0.01),
                norm.ppf(0.99), 100)

ax.plot(x, norm.pdf(x),
       'r-', lw=5, alpha=0.6)

ax.plot(x, np.full(len(x), 0.2),
       'b-', lw=1)

fig.show()
```


Pour **dessiner des graphiques**, il vous est conseillé de suivre cette méthode:

* Creation des objets `fig`et `ax`
* Ajout successif de graphiques sur la même figure par l'appel à des methodes de l'objet `ax`
* Affichage de la figure complète via `fig.show()`
* Evaluation de la cellule pour visualisation dans la cellule de résultats.

#### On entend par figure un graphique avec des **axes légendés et un titre**.

La documentation matplotlib est bien faite, mais **attention** il vous est demandé, pour la construction des graphiques, **d'ignorer les méthodes de l'objet `plt`** et d'utiliser uniquement les méthodes de l'[objet Axes](https://matplotlib.org/api/axes_api.html?highlight=axe#plotting). `plt` est un objet global susceptible d'agir simultanement sur tous les graphiques d'un notebook. A ce stade, son utilisation est donc à eviter.

## Données disponibles

### Mesures experimentales

Un fichier `data/TCL_wt1.tsv` contenant les données d'abondances differentielles effectuées sur une souche sauvage d'*Escherichia coli* entre deux conditions, avec Tetracycline et en milieu riche. Le contrôle est le milieu riche.

| Accession | Description | Gene Symbol  |   Corrected Abundance ratio (1.53)    | Log2 Corrected Abundance Ratio | Abundance Ratio Adj. P-Value |   -LOG10 Adj.P-val |
| --- | --- | --- | --- | --- | --- | ---|
| Uniprot Identifier | Texte libre | Texte libre  | $\frac{\text{WildType}_{\text{Tc}}}{\text{WildType}_{\text{rich}}}$ | $Log_2(\frac{\text{WildType}_{\text{Tc}}}{\text{WildType}_{\text{rich}}})$  | Réel positif OU indéfini | Réel positif OU indéfini  |

Attention certaines valeurs numériques sont manquantes ou erronées, constatez par vous même.

### Fiches uniprot

Les fiches de toutes les protéines de E.coli sont stockées dans un seul document XML `data/uniprot-proteome_UP000000625.xml`. Nous allons extraire de ce fichier les informations dont nous aurons besoin à l'aide, du module de la librarie standard [XML.etree](https://docs.python.org/3/library/xml.etree.elementtree.html#module-xml.etree.ElementTree). Pour vous facilitez la tâche, les exemples suivants vous sont fournis. Prenez le temps de les executer et de les modifier dans un notebook. Vous vous familliariserez ainsi avec la structure du document XML que vous pouvez egalement  inspectez dans un navigateur.

```python
from xml.etree.ElementTree import parse, dump
# Parse the E.Coli proteome XML Document
tree = parse('data/uniprot-proteome_UP000000625.xml')
root = tree.getroot()
ns = '{http://uniprot.org/uniprot}' # MANDATORY PREFIX FOR ANY SEARCH within document
# Store all entries aka proteins in a list of xml nodes
proteins = root.findall(ns + 'entry')
# Display the xml subtree of the first protein 
dump(proteins[0])
```

```python
# Find the xml subtree of a protein with accession "P31224"
for entry in proteins:
    accessions = entry.findall(ns+"accession")
    for acc in accessions:
        if acc.text == "P31224":
            dump(entry)
            break
```

Cherchez par exemple le sous arbre de la protéine codée par le gene LacZ

### Statistiques de termes GO

Les nombres d'occurence de tous les termes GO trouvés dans le protéome de E.coli sont stockés dans le fichier `data/EColiK12_GOcounts.json`. Ces statistiques ont été dérivées du fichier `data/uniprot-proteome_UP000000625.xml`.

## Objectifs

Representer graphiquement les données d'abondance et construire la pvalue des fonctions biologiques (terme GO) portées par les protéines sur-abondantes.

### Description statistique des Fold Change

##### source:`data/TCL_wt1.tsv`

##### 1. Charger le contenu de ce fichier dans un notebook en eliminant les lignes porteuses de valeurs numériques aberrantes.

##### 2. Representez par un histogramme les valeurs de `Log2 Corrected Abundance Ratio`

##### 3. A partir de cette échantillon de ratio d'abondance,  estimez la moyenne $\mu$ et l'ecart-type $\sigma$ d'une loi normale.
```


```

##### 4. Superposez la densité de probabilité de cette loi sur l'histogramme. Attention, la densité de probabilité devra être mis à l'echelle de l'histogramme:

![Histogramme](histogram_log2FC.png "Title")

```python
hist = ax.hist(_, bins=100) # draw histogram
x = np.linspace(min(_), max(_), 100) # generate PDF domain points
dx = hist[1][1] - hist[1][0] # Get single value bar height
scale = len(_)*dx # scale accordingly
ax.plot(x, norm.pdf(x, mu, sqrt(S_2))*scale) # compute theoritical PDF and draw it
```

##### 5. Quelles remarques peut-on faire à l'observation de l'histogramme et de loi théorique?

```


```

##### Construction d'un volcano plot

##### A l'aide de la méthode [scatter](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.axes.Axes.scatter.html) representer $-\text{Log}_{10}({\text{p-value}}) = f(\text{Log}_2(\text{abundance ratio}))$

##### Matérialisez le quadran des protéines surabondantes, par deux droites ou un rectangle
Sont condidérées comme surabondantes les proteines remplissant ces deux critères:

* $\text{Log}_2(\text{abundance ratio}) > \mu$  
* $\text{p-value} > 0.1%$ 

### Analyse Fonctionelle de pathway

Nous allons implementer une approche ORA (Over Representation Analysis) naive.

##### 1. Retrouver les entrées du fichier TSV des protéines surabondates

Quelles sont leurs identifiants UNIPROT ?
``` 



```

#### 2. Lister les termes GO portés par ces protéines surabondates

Les `entry` du fichier `data/uniprot-proteome_UP000000625.xml` présentent des balises de ce type:

```xml
<dbReference type="GO" id="GO:0005737">
<property type="term" value="C:cytoplasm"/>
<property type="evidence" value="ECO:0000501"/>
<property type="project" value="UniProtKB-SubCell"/>
</dbReference>
```

A ce stade, on se contentera des identifiants GO (eg `GO:0005737`). Vous pouvez faire un [set](https://docs.python.org/3.8/library/stdtypes.html#set) de cette liste d'identifiants GO pour en éliminer rapidement la redondance.

#### 3. Obtention des paramètres du modèle

Nous evaluerons la significativité de la présence de tous les termes GO portées par les protéines surabondantes à l'aide d'un modèle hypergéometrique.
Si k protéines surabondantes porte un terme GO, la pvalue de ce terme sera équivalente à $P(X\ge k), X \sim H(k,K,n,N)$.
Où,

Completer le tableau ci-dessous avec les quantités vous  semblant adéquates


| Symbole | Paramètre | Quantités Biologique |
| --- | --- | --- |
| k | nombre de succès observés| |
| K | nombre de succès possibles| |
| n | nombre d'observation| |
| N | nombre d'elements observables| |


#### 4. Calcul de l'enrichissement en fonction biologiques

A l'aide du contenu de `data/EColiK12_GOcounts.json` parametrez une loi hypergeomtrique et calculez les pvalues
des termes GO portés par les protéines sur-abondantes et reporte ces données dans le tablau ci-dessous

| identifiant GO | définition | occurence | pvalue|
|---|---|---|---|
|   |   |   |   |

Quelle interpretation biologique faites-vous de cet enrichissement en fonction biologiques spécifiques ?
```




```
