# Peindre comme un artiste avec un CNN

En Aôut 2015, un article nommé "A Neural Algorithm of Artistic Style" est sorti et cet article montre comment un réseau de neurones à convolutions peut être utilisé pour combiner le contenu d'une image et le style d'une autre image. L'auteur de l'article illustre la technique avec des photos de Thurbingen en Allemagne, avec le style de cinq célèbres peintures.

![Germany](https://user-images.githubusercontent.com/38551652/79652700-0c5ffb00-81b2-11ea-8466-2a09be8aca6d.jpeg)

Les réseaux de neurones à convolution permettent d'extraire des caractéristiques d'une image. Ainsi on peut se servir de ce type de réseau pour extraire le contenu et le style d'images.

Le but est donc de construire un tel réseau de neurones. Nous allons donc comme il est indiqué dans l'article de Gatys et al. nous servir d'un réseau pré-entraîné qui le VGG19. Ce réseau est un réseau de neurones profonds à convolution créé par l'Univesité d'Oxford. Il a été entraîné sur la base de données ImageNet qui contient 14 millions d'images regroupées en 1000 catégories. L'objctif intitial du réseau était d'identifier des images d'objects comme cela : 

![ImageNet](https://user-images.githubusercontent.com/38551652/79662053-c22c4900-81b4-11ea-9e38-5e323aca3de5.png)

Le modèle contient de nombreuses couches, incluant des couches convolutionnelles. L'architecture est la suivante : 

![CNN_Peindre_Architecture](https://user-images.githubusercontent.com/38551652/79662049-c22c4900-81b4-11ea-8b1e-67e04a7a6f40.jpeg)

Sur la représentation du réseau ci-dessus, les couches noires sont les couches convolutionnelles. Elles sont regroupées par bloc. Par convention, la couche conv2_3 fait référence au second bloc de la toisième couche de convolution. La dernière couche permet de sortir des probabilités d'appartenance à une catégorie d'objet, nous n'avons dans notre cas pas besoin de cette couche. Nous allons nous servir seulement des couches allant jusquà conv5_3.

Dans l'article de Gatys et al, il est indiqué d'utilisé la couche conv5\_2 pour extraire les caractéristiques d'une image. Cela servira de base et il faudra "peindre" sur cette image. On cherche donc maintenant à savoir comment transférer les caractéristiques récupérés par la couche conv5_3 vers une nouvelle image.

Supposons que nous ayons ume image p contenant le contenu de notre image et un canevas "blanc" x sur lequel nous voulus extraire et peindre notre contenu. Le méthode consiste à ajuster itérativement l'image que l'on veut obtenir jusqu'à ce que la sortie de la couche conv5_2 soit similaire au contenu de l'image. Le processus en détail est le suivant : 

* Mettre l'image dont on veut extraire le contenu (l'image p) en entrée du modèle pour avoir la sortie donnée par la couche de convolution conv5_2. On l'appelle P5_2.
* Faire de même avec l'image cible x (initialisée aléatoirement). On appelle la sortie F5_2.
* Calculer l'erreur entre les deux sorties
* Ajuster l'image cible par backpropagation pour minimiser la fonction côut.
* Répéter les étapes 2 à 4 pour ajuste l'image cible


Nous avons vu comment extraire le contenu d'une image. Nous allons maintenant voir comment extraire le style. La méthode est très similaire à l'extraction du contenu d'une image, avec deux différences : 
* Au lieu d'utiliser une seule couche de convolution, on va en utiliser 5. Gatys et al utiliser les couches 1_1, 2_1, 3_1, 4_1 et 5_1. Ces 5 couches ont la même contribution dans le coût total affecté au style
* Avant de passer les sorties des couches de convolutions dans la fonction coût, une fonction appelée Gram matrix est appliquée.

La matrice de Gram a pour but de calculer les corrélations entre les différentes réponses des filtres. La matrice de Gram regroupe des informations sur les similitudes d'une image. De cette façon, elle capture les aspects stylistiques de l'image.

Maintenant que nous avons vu comment extraire le style d'une image, on peut utiliser a même méthode que précemment pour peindre ce style. Si on peint le style sur un canevas blanc, on peut visualiser ce que les couches de convolutions capturent.

![stylecapture](https://user-images.githubusercontent.com/38551652/79662045-c193b280-81b4-11ea-890a-79e9de3c8ef3.jpeg)


Pour obtenir le style et le contenu sur une seule image, on applique le même schéma que précédemment, c'est à dire qu'on va chercher à ajuster itérativement l'image afin de minimiser une nouvelle fonction coût.

Les paramètres importants à prendre en compte lors des phases de tests pour obtenir les résultats sont les suivants : 

* Le poids du style : permet d'ajuster l'image selon que l'on préfère privilégier l'exactitude du contenu ou du style.
* Quelles couches sont à utiliser pour extraire le style et le contenu. Gatys et al a utilisé les couches que nous avons citées précédemment, mais d'autres couches pourraient être utilisées.
* Le nombre d'itérations : plus le nombre d'itérations est elevé, plus le résultat sera long. Selon la taille des images, les temps de calcul peuvent aller jusqu'à des dizaines d'heures pour un nombre d'itération assez faible.

Dans notre cas, nous avons essayé d'obtenir des résultats avec nos ordinateurs personnels pourtant assez puissant mais le temps de calcul était de plusieurs heures. Nous avons eu la chance de pouvoir utiliser un ordinateur contenant 40 coeurs et le temps de calcul pour 100 itérations était d'environ 18 minutes. Voici nos résultats.

Tout d'abord l'image dont on cherche à extraire le contenu et à peindre dessus est la suivante : 

![cdmoriginal](https://user-images.githubusercontent.com/38551652/79662040-c193b280-81b4-11ea-9a2c-2f9298f43be2.jpg)

Les styles que l'on veut appliquer à l'image sont les suivants : 
![vanGogh](https://user-images.githubusercontent.com/38551652/79661990-be98c200-81b4-11ea-90b2-4d4fda7051b0.jpg)
![matisse](https://user-images.githubusercontent.com/38551652/79662011-bfc9ef00-81b4-11ea-86d4-4b4e094ccb37.jpg)
![monet](https://user-images.githubusercontent.com/38551652/79662023-c0628580-81b4-11ea-9fdc-fb03dc49b2a4.jpg)
![picasso2](https://user-images.githubusercontent.com/38551652/79662035-c0fb1c00-81b4-11ea-8075-26bebbc3cd86.jpg)

Finalement, les résultats :

![vangogh_cdm](https://user-images.githubusercontent.com/38551652/79661953-bc366800-81b4-11ea-86fe-bca4d17c86b2.jpg)
![matisse_cdm](https://user-images.githubusercontent.com/38551652/79662019-c0628580-81b4-11ea-89c7-4956f4d3c68f.jpg)
![monet_cdm](https://user-images.githubusercontent.com/38551652/79662028-c0fb1c00-81b4-11ea-9642-b25c2d02f784.jpg)
![picasso_cdm](https://user-images.githubusercontent.com/38551652/79662031-c0fb1c00-81b4-11ea-8ea9-22768f258f76.jpg)



