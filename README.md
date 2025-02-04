1.1 Les test containers sont une librairie java quipermet d'utiliser des conteneur docker pour faire des tests des différents composant dans un environnement réaliste.
Par exemple un conteneur postgres pour tester une bdd postgres

création du main.yml
commit et push sur github
controle de la bonne execution du workflow


2.3 Stockage centralisé : Une fois que les images sont poussées vers un registre, elles peuvent être stockées, versionnées et accessibles de n'importe où. 
Cela permet d'avoir un dépôt central où vos images Docker sont gérées.
Réutilisation : Vous pouvez tirer la même image plusieurs fois à partir de n'importe quel environnement (développement, staging, production), garantissant ainsi 
que l'application s'exécute dans le même environnement et avec la même configuration à chaque fois.
cela permet aussi une partage facile