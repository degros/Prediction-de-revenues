# Prediction-de-revenues
Mes missions Mission 1 Résumez les données utilisées :

année(s) des données utilisées ; nombre de pays présents ; population couverte par l'analyse (en termes de pourcentage de la population mondiale). Les données de la World Income Distribution présentent pour chaque pays les quantiles de la distribution des revenus de leur population respective.

De quel type de quantiles s'agit-il (quartiles, déciles, etc.) ? Échantillonner une population en utilisant des quantiles est-il selon vous une bonne méthode ? Pourquoi ? Nous appellerons ici chaque quantile une classe de revenu. Ainsi, la valeur de la colonne income pour un quantile donné peut être vue comme le revenu moyen des personnes appartenant à la classe de revenu correspondante à ce quantile.

L'unité utilisée dans la colonne income de la world income distribution est le $PPP. Cette unité est calculée par la Banque mondiale, selon la méthode Eltöte-Köves-Szulc. Après vous être documenté, vous expliquerez à votre mentor très brièvement à quoi correspond cette unité et pourquoi elle est pertinente pour une comparaison de pays différents (Il n'est pas nécessaire de donner cette explication lors de la soutenance).

Mission 2 Montrez la diversité des pays en termes de distribution de revenus à l'aide d'un graphique. Celui-ci représentera le revenu moyen (axe des ordonnées, sur une échelle logarithmique) de chacune des classes de revenus (axe des abscisses) pour 5 à 10 pays que vous aurez choisis pour montrer la diversité des cas. Représentez la courbe de Lorenz de chacun des pays choisis. Pour chacun de ces pays, représentez l'évolution de l'indice de Gini au fil des ans. Classez les pays par indice de Gini. Donnez la moyenne, les 5 pays ayant l'indice de Gini le plus élevé et les 5 pays ayant l'indice de Gini le plus faible. En quelle position se trouve la France ?

Mission 3 Dans l'état actuel, nous avons à disposition deux des trois variables explicatives souhaitées :

(m_{j}) le revenu moyen du pays (j)

(G_{j}) l'indice de Gini du pays (j)

Il nous manque donc, pour un individu (i) , la classe de revenu (c_{i,parent}) de ses parents.

Nous supposons ici que l'on associe à chaque individu (i) une unique classe (c_{i,parent}) ; quel que soit le nombre de parents de (i).

Nous allons donc simuler cette information grâce à un coefficient (\rho_{j}) (propre à chaque pays (j) ), mesurant une corrélation entre le revenu de l'individu (i) et le revenu de ses parents. Ce coefficient sera ici appelé coefficient d'élasticité ; il mesure la mobilité intergénérationnelle du revenu.

Pour plus d'informations sur le calcul du coefficient d'élasticité, consulter ce document, notamment l'équation 1 de la page 8. Ce coefficient est déterminé par une régression linéaire simple dans laquelle le logarithme du revenu de l'enfant (Y_{child}) est une fonction du logarithme du revenu des parents (Y_{parent}) :

[ln(Y_{child}) = \alpha + \rho_j\ ln(Y_{parent}) + \epsilon]

Pour obtenir le coefficient d'élasticité, deux possibilités s'offrent à vous :

Vous baser sur ces coefficients donnés par la Banque mondiale, dans GDIM dataset. Le coefficient d'élasticité est donné pour certains pays, sous le nom d'IGE Income (relative IGM in income). Vous baser sur des estimations provenant de multiples études, extrapolées à différentes régions du monde : elles se trouvent dans le fichier elasticity.txt. Attention, ces données sont parfois anciennes. Il est aussi possible de combiner ces deux approches.

Pour chaque pays, nous allons utiliser une génération aléatoire de la classe de revenu des parents, à partir de ces seules deux informations :

(\rho_j) la classe de revenu de l'enfant (c_{i,child}). Attention à bien utiliser la classe de revenu de l'enfant (qui est un nombre compris entre 1 et 100 si vous utilisez 100 quantiles), plutôt que son revenu PPP. De même, on ne cherche pas à générer le revenu des parents, mais la classe de revenu des parents (c_{i,parent}).

Voici le protocole de génération pour un pays (j) donné, qui se base sur l'équation donnée ci dessus :

Un exemple de code permettant de réaliser les opérations 1 à 6 est donné tout en bas. Libre à vous de l'utiliser. Notamment, la fonction proba_cond vous donnera les probabilités (P(c_{i,parent}|c_{i,child},j)).

Générez un grand nombre (n) de réalisations d'une variable que nous appellerons (ln(Y_{parent})) selon une loi normale. Le choix de la moyenne et de l'écart type n'auront pas d'incidence sur le résultat final. (n) doit être supérieur à 1000 fois le nombre de quantiles. Générez (n) réalisations du terme d'erreur (\epsilon) selon une loi normale de moyenne 0 et d'écart type 1. Pour une valeur donnée de (\rho_j) (par exemple 0.9), calculez (y_{child} = e^{\alpha+\rho_jln(y_{parent})+\epsilon}) . Le choix de ( \alpha) n'a aucune incidence sur le résultat final et peut être supprimé. À ce stade, (y_{child}) contient des valeurs dont l'ordre de grandeur ne reflète pas la réalité, mais cela n'a pas d'influence pour la suite. Pour chacun des (n) individus générés, calculez la classe de revenu (c_{i,child}) ainsi que la classe de revenu de ses parents (c_{i,parent}) , à partir de (y_{child}) et (y_{parent}). À partir de cette dernière information, estimez pour chaque (c_{i,child}) la distribution conditionnelle de (c_{i,parent}) . Par exemple, si vous observez 6 individus ayant à la fois (c_{i,child} = 5) et (c_{i,parent} = 8) , et que 200 individus sur 20000 ont ( c_{i,child} = 5) , alors la probabilité d'avoir ( c_{i,parent} = 8) sachant (c_{i,child} = 5) et sachant (\rho_j=0.9) sera estimée à 6/200 (On note cette probabilité comme ceci : (P(c_{i,parent}=8|c_{i,child}=5,\rho_j=0.9) = 0.03)). Si votre population est divisée en (c) classes de revenu, vous devriez alors avoir (c^2) estimations de ces probabilités conditionnelles, pour chaque pays. Optionnellement et pour vérifier la cohérence de votre code, vous pouvez créer un graphique représentant ces distributions conditionnelles. Voici 2 exemples pour une population segmentée en 10 classes, pour 2 valeurs de (\rho_j) : l'une traduisant une forte mobilité (0.1) et l'autre une très faible mobilité (0.9) : Faible mobilité Forte mobilité Forte mobilité Faible mobilité Éventuellement et pour éviter toute confusion, effacez les individus que vous venez de générer (nous n'en avons plus besoin), et ne gardez que les distributions conditionnelles. Nous allons maintenant travailler sur un nouvel échantillon. Celui-ci sera créé à partir de la WID. Pour chaque individu de la World Income Distribution, créez-en 499 "clones". La taille de votre nouvel échantillon sera donc 500 fois plus grand que celui de la World Income Distribution. Pour chaque (c_{i,child}) et chaque pays, il y a maintenant 500 individus. Vous attribuerez aux 500 individus leurs classes ( c_{i,parent}) conformément aux distributions trouvées précédemment. Par exemple, si (P(c_{i,parent}=8|c_{i,child}=5,\rho_j=0.9) = 0.03) , alors vous assignerez la classe (c_{i,parent} = 8) à 15 des 500 individus du pays ( j) ayant (c_{i,child}=5) , car 500*0.03 = 15. Éventuellement et pour éviter toute confusion, effacez la variable (c_{i,child}) : nous n'en avons pas besoin pour la mission 4. Assurez-vous que votre nouvel échantillon contiennent bien les variables initialement présentes dans la World Income Distribution : (m_j) et (G_j) . Utilisez ce nouvel échantillon pour la mission 4.

Mission 4 Pour cette mission 4, nous chercherons à expliquer le revenu des individus en fonction de plusieurs variables explicatives : le pays de l'individu, l'indice de Gini de ce pays, la classe de revenus des parents, etc.

Appliquez une ANOVA sur vos données, en n’incluant comme variable explicative que le pays de l’individu. Analysez la performance du modèle.

Pour chacune des régressions suivantes, vous testerez 2 version : l'une en exprimant le revenu moyen du pays et les revenus (parents & enfants) en logarithme (ln), l'autre en les laissant tels quels. Vous choisirez la version la plus performante pour répondre aux question.

Appliquez une régression linéaire sur vos données, en incluant comme variables explicatives uniquement le revenu moyen du pays de l’individu et l’indice de Gini du pays de l’individu. Quel est le pourcentage de variance expliquée par votre modèle ?

Selon ce modèle, donnez la décomposition de variance totale expliquée par :

le pays de naissance (ie. le revenu moyen et l’indice de Gini) ; les autres facteurs non considérés dans le modèle (efforts, chance, etc.). Améliorez le modèle précédent en incluant maintenant la classe de revenu des parents. Quel est le pourcentage de variance expliquée par ce nouveau modèle ?

En observant le coefficient de régression associé à l’indice de Gini, peut-on affirmer que le fait de vivre dans un pays plus inégalitaire favorise plus de personnes qu’il n’en défavorise ?

Selon ce dernier modèle, donnez la décomposition de variance totale expliquée par :

le pays de naissance et le revenu des parents les autres facteurs non considérés dans le modèle (efforts, chance, etc.)
