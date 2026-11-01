
# TD — Stockage de matrices en C

**INSA Centre Val de Loire — 3e année STI**

**Module :** Programmation C  
**Enseignant :** A. ADELL

## Membres du groupe

- Nom et prénom : NGUYEN Truong Hien
- Identifiant GitHub : nguyentruonghien
- Nom et prénom : HO Viet Manh Hung
- Identifiant GitHub : kabigon048

## 1. Introduction

Ce projet a pour objectif de développer une bibliothèque en langage C permettant de manipuler des matrices d'entiers.

La bibliothèque doit permettre de créer et de détruire des matrices, de lire et d'écrire des fichiers texte, ainsi que d'effectuer des opérations d'addition et de multiplication.

Ce TD permet également de mettre en pratique la programmation modulaire, les pointeurs, l'allocation dynamique de mémoire et la gestion des erreurs.

## 2. Conception
Le programme est organisé en plusieurs modules afin de séparer les différentes fonctionnalités.

matrice.h et matrice.c : définition de la structure Matrice, gestion des codes d'erreur et vérification de la validité des matrices.

- matrice_alloc.h et matrice_alloc.c : allocation et libération de la mémoire.

- matrice_io.h et matrice_io.c : lecture, écriture et affichage des matrices.

- matrice_calcul.h et matrice_calcul.c : addition et multiplication des matrices.

- main.c : programme principal de démonstration.

Une matrice est représentée par une structure contenant un pointeur int **coef, le nombre de lignes et le nombre de colonnes.

Chaque ligne est allouée séparément avec calloc. La mémoire doit être libérée avec free lorsque la matrice n'est plus utilisée.

Les fonctions utilisent le type MatErreur pour signaler les erreurs et garantir une gestion cohérente des résultats.

## 3. Réalisation

### 3.1 Exercice 1 — Allocation et destruction des matrices

#### A. Réalisation

Fichiers : matrice.c, matrice_alloc.c.

- Implémentation des fonctions mat_message_erreur et mat_est_valide.

- Vérification des dimensions avant l'allocation.

- Allocation dynamique des lignes avec mat_calloc et libération de la mémoire en cas d'échec.

- Implémentation de mat_detruire pour libérer la mémoire et réinitialiser la matrice.

#### B. Résultats et vérifications

Tests : 16/16.

Compilation : préciser si la compilation s'effectue sans avertissement.

Valgrind : aucune fuite mémoire ni erreur détectée (ERROR SUMMARY: 0 errors).

#### C. Questions de réflexion

Q3.1. Pourquoi mat_detruire prend-elle un Matrice * et non un Matrice ? Que se passerait-il, pour l'appelant, avec un passage par valeur ?
Réponse : mat_detruire prend un pointeur pour modifier directement la matrice de l'appelant et la remettre à MATRICE_VIDE après la libération de la mémoire. Avec un passage par valeur, seule une copie de la structure serait modifiée, laissant un pointeur pendant dans la matrice originale.

Q3.2. Combien d'appels à calloc et à free faut-il pour une matrice n × p ? Si la k-ième allocation échoue, combien de libérations mat_allouer doit-elle effectuer ?
Réponse : Pour une matrice n × p, il faut n + 1 appels à calloc : un pour le tableau de pointeurs et n pour les lignes. La destruction nécessite n + 1 appels à free. Si la k-ième allocation échoue, mat_allouer doit effectuer k − 1 libérations pour éviter toute fuite mémoire.

Q3.3. Pourquoi appeler deux fois mat_detruire sur la même matrice est-il sans danger ? Est-ce encore vrai après Matrice b = a;, puis destruction de a et b ?
Réponse : Après le premier appel, mat_detruire remet la matrice à MATRICE_VIDE. Le deuxième appel est donc sans danger. En revanche, Matrice b = a; effectue une copie superficielle : les deux matrices partagent la même mémoire. Détruire successivement a et b risque alors de provoquer une double libération (double free).

Q3.4. Pourquoi mat_allouer commence-t-elle par *res = MATRICE_VIDE, avant de vérifier les dimensions ?
Réponse : Cette initialisation garantit que la matrice résultat est vide, même si les dimensions sont invalides ou si l'allocation échoue. Elle évite de laisser des pointeurs non initialisés et facilite la gestion des erreurs ainsi que la libération sécurisée de la mémoire.

---

### 3.2 Exercice 2 — Entrées-sorties

#### A. Réalisation

Fichier : matrice_io.c.

- Implémentation de mat_afficher pour afficher les coefficients des matrices.

- Implémentation de mat_ecrire pour enregistrer les matrices au format texte canonique.

- Implémentation de mat_lire pour lire les matrices et vérifier la validité des données.

Gestion des erreurs de lecture et libération de la mémoire en cas d'échec.

#### B. Résultats et vérifications

Tests : 14/14.
=== Exercice 2 : affichage (verification visuelle) ===
M (4 x 3) :
        1        2        3
        4        5        6
        7        8        9
       10       11       12
(2.a) Vous devez voir ci-dessus 4 lignes : 1 2 3 / 4 5 6 / 7 8 9 / 10 11 12
=== Exercice 2 : ecriture ===
[OK]    ouverture de test_ex2_sortie.txt en ecriture
[OK]    (2.b) mat_ecrire renvoie MAT_OK
[OK]    (2.b) le fichier contient exactement le format attendu
[OK]    (2.b) mat_ecrire refuse une matrice vide
=== Exercice 2 : relecture ===
[OK]    (2.f) mat_lire relit le fichier : MAT_OK
[OK]    (2.e) la matrice relue est identique
[OK]    (2.e) espaces et sauts de ligne en trop sont toleres
=== Exercice 2 : fichiers incorrects ===
[OK]    (2.c) fichier vide
[OK]    (2.c) dimension nulle
[OK]    (2.c) dimension negative
[OK]    (2.e) coefficient manquant
[OK]    (2.e) donnee non numerique
[OK]    (2.e) entier trop grand pour un int
=== Exercice 2 : echec d'allocation simule ===
[OK]    (2.d) mat_lire renvoie MAT_ERR_ALLOCATION proprement

Bilan : 14 / 14 verifications reussies.
Pensez a verifier l'absence de fuite : valgrind --leak-check=full ./test_ex2

Compilation : réussie sans avertissement.

Valgrind : aucune fuite mémoire ni erreur détectée (ERROR SUMMARY: 0 errors).
#### C. Questions de réflexion

Q4.1. Pourquoi mat_lire remplit-elle d'abord une matrice locale avant de l'affecter à *res ?
Réponse : Une matrice locale permet de construire le résultat entièrement avant de le transmettre à l’appelant. Si une lecture échoue, on libère cette matrice locale et *res reste vide.

Q4.2. La lecture vérifie-t-elle que chaque ligne de la matrice est sur sa propre ligne de texte ? Est-ce nécessaire ?
Réponse : Non. lire_entier utilise fscanf, qui accepte les espaces, tabulations et retours à la ligne comme séparateurs. Il suffit de lire le nombre de coefficients annoncé par les dimensions.

Q4.3. Après mat_lire(f, &m), le flux f est positionné après le dernier coefficient. Comment cette propriété peut-elle être exploitée ?
Réponse : On peut continuer à lire dans le même flux, par exemple pour récupérer une seconde matrice enregistrée à la suite de la première.

Q4.4. Pourquoi mat_ecrire appelle-t-elle fflush avant de renvoyer MAT_OK ? Cela dispense-t-il de vérifier fclose ?
Réponse : fflush force la transmission des données tamponnées et permet de détecter une erreur d’écriture. Cela ne dispense pas de vérifier fclose, qui peut encore signaler une erreur.
---

### 3.3 Exercice 3 — Calcul matriciel

#### A. Réalisation
 
Fichier : matrice_calcul.c.

- Implémentation de mat_additionner pour calculer la somme de deux matrices.

- Implémentation de mat_multiplier pour calculer le produit de deux matrices.

- Vérification de la compatibilité des dimensions.

- Détection des dépassements d'entiers et gestion des erreurs.

- Préservation des matrices d'entrée et libération de la mémoire en cas d'échec.

#### B. Résultats et vérifications

Tests : 14/14.

=== Exercice 3 : addition ===
[OK]    (3.a) (2 x 3) + (3 x 2) : MAT_ERR_DIMENSIONS
[OK]    (3.d) (2 x 3) + (2 x 3) : MAT_OK
[OK]    (3.c) a + b est correct
[OK]    a et b n'ont pas ete modifiees
[OK]    (3.b) le resultat possede sa propre memoire
[OK]    (3.c) INT_MAX + 1 : MAT_ERR_DEPASSEMENT
=== Exercice 3 : multiplication ===
[OK]    (3.e) (2 x 3) x (2 x 3) : MAT_ERR_DIMENSIONS
[OK]    (3.h) (2 x 3) x (3 x 2) : MAT_OK
[OK]    (3.g) a x c = (58 64 / 139 154)
[OK]    (3.f) (3 x 2) x (2 x 3) donne une matrice 3 x 3
[OK]    a et c n'ont pas ete modifiees
[OK]    (3.g) 2 x 10^10 depasse INT_MAX : MAT_ERR_DEPASSEMENT
=== Exercice 3 : cas particuliers ===
[OK]    resultat = entree refuse, a reste intacte
[OK]    (3.f) echec d'allocation : MAT_ERR_ALLOCATION, r vide

Bilan : 14 / 14 verifications reussies.
Pensez a verifier l'absence de fuite : valgrind --leak-check=full ./test_ex3

Compilation : réussie sans avertissement.

Valgrind : aucune fuite mémoire ni erreur détectée (ERROR SUMMARY: 0 errors).

#### C. Questions de réflexionA. 

Q5.1. Le paramètre const Matrice *a empêche-t-il d'écrire a->coef[0][0] = 7; ? Que protège exactement const ici ?
Réponse : Non. const Matrice *a interdit de modifier directement les champs de la structure par a, mais les entiers pointés par a->coef restent modifiables. Pour protéger aussi les coefficients, il faudrait une interface adaptée.

Q5.2. Pourquoi refuse-t-on mat_additionner(&a, &b, &a) alors que, mathématiquement, on peut remplacer A par A + B ?
Réponse : La fonction traite res comme un paramètre de sortie et exige qu’il soit distinct des entrées. Écraser a ferait perdre l’entrée avant la fin du calcul et risquerait de perdre la mémoire qui lui appartient.

Q5.3. Combien de multiplications d'entiers effectue mat_multiplier pour des matrices n × p et p × q ?
Réponse : Il y a n × q coefficients dans le résultat et chacun demande p multiplications, soit n × p × q multiplications.

Q5.4. Pourquoi ne suffit-il pas de calculer int s = a + b; puis de tester s < a pour détecter un dépassement ?
Réponse : Le dépassement d’un int signé est un comportement indéfini en C : le test après le calcul est trop tardif. De plus, s < a ne détecte pas tous les cas. Il faut calculer dans un type plus large puis vérifier les bornes.

---

### 3.4 Exercice 4 — Démonstration complète

#### A. Réalisation

Fichier : main.c.

- Implémentation des fonctions de chargement et de sauvegarde des matrices.

- Vérification des arguments de la ligne de commande.

- Gestion des opérations d'addition (add) et de multiplication (mul).

- Gestion des erreurs et libération des matrices avant la fin du programme.

#### B. Résultats et vérifications

Compilation : réussie avec Makefile, sans erreur ni avertissement.

Exécution des scénarios : l'addition des matrices A et B ainsi que la multiplication des matrices A et C ont réussi. Les résultats enregistrés dans R.txt correspondent aux résultats attendus. Les trois scénarios d'erreur testés (dimensions incompatibles, fichier invalide et fichier incomplet) ont été correctement détectés.

Codes de retour : 0 pour les deux opérations réussies et 1 pour les trois scénarios d'erreur testés.

Valgrind : aucune fuite mémoire ni erreur détectée lors de l'exécution vérifiée. Valgrind indique 21 allocations, 21 libérations et 0 erreur.

#### C. Questions de réflexion

Q6.1. Pourquoi le message d'erreur est-il affiché par main.c (ou ses fonctions static) et non par mat_lire ? Pensez à un programme graphique réutilisant la bibliothèque.
Réponse : La fonction `mat_lire` doit seulement lire une matrice et retourner un code d'erreur. C'est au programme principal de décider comment afficher cette erreur.

Cette séparation permet de réutiliser la bibliothèque dans différents programmes. Par exemple, une application graphique pourrait afficher l'erreur dans une fenêtre au lieu de l'afficher dans le terminal.

Q6.2. Pourquoi le résultat de fclose est-il testé dans sauvegarder mais pas dans charger ?
Réponse : Dans `sauvegarder`, le résultat de `fclose` est vérifié, car une erreur d'écriture peut apparaître lors de la fermeture du fichier. Certaines données peuvent encore être en mémoire tampon et ne pas avoir été enregistrées.

Dans `charger`, le fichier est ouvert en lecture. La fermeture ne sert pas à enregistrer des données. C'est pourquoi le code fourni ne vérifie pas le résultat de `fclose`.

Q6.3. Dans le scénario 8, le calcul a réussi mais l'enregistrement a échoué. Combien de matrices faut-il libérer ? Vérifiez que votre programme le fait.
Réponse : Il faut libérer les trois matrices : A, B et R.

Même si l'enregistrement échoue, les trois matrices ont déjà été créées en mémoire. Le programme utilise `goto nettoyage` pour accéder au point de nettoyage commun.

Les fonctions `mat_detruire(&r)`, `mat_detruire(&b)` et `mat_detruire(&a)` sont alors appelées pour libérer les trois matrices et éviter les fuites de mémoire.

## 4. Vérifications mémoire

 Les vérifications mémoire ont été réalisées avec Valgrind 3.19.0 sur une machine virtuelle Debian (Proxmox).

Les commandes utilisées sont :

valgrind --leak-check=full ./test_ex1
valgrind --leak-check=full ./test_ex2
valgrind --leak-check=full ./test_ex3
valgrind --leak-check=full ./demo_matrices add donnees/A.txt donnees/B.txt R.txt

Résultats :

Exercice 1 : 16/16 tests réussis, aucune fuite mémoire détectée.

Exercice 2 : 14/14 tests réussis, aucune fuite mémoire détectée.

Exercice 3 : 14/14 tests réussis, aucune fuite mémoire détectée.

Exercice 4 : aucune fuite mémoire ni erreur détectée lors de l'exécution vérifiée.

Valgrind indique ERROR SUMMARY: 0 errors et All heap blocks were freed -- no leaks are possible.

Ces résultats montrent que la mémoire a été correctement libérée dans les scénarios testés, sans les inventer.


## 5. Démonstration finale

Le programme `demo_matrices` permet de réaliser l'addition et la multiplication de deux matrices à partir de fichiers texte.

### 5.1 Compilation avec Makefile

Le projet contient un `Makefile` qui permet de compiler automatiquement les fichiers sources.

Pour compiler le projet :

```bash
make
```

Cette commande permet de générer les programmes `test_ex1`, `test_ex2`, `test_ex3` et `demo_matrices`.

Pour exécuter les tests :

```bash
make test
```

### 5.2 Addition de deux matrices

Pour additionner les matrices A et B :

```bash
./demo_matrices add donnees/A.txt donnees/B.txt R.txt
```

Le programme lit les deux matrices, calcule leur somme et enregistre le résultat dans `R.txt`.

Résultat attendu :

```text
4 3
26 50 87
19 38 17
52 96 19
10 23 7
```

### 5.3 Multiplication de deux matrices

Pour multiplier les matrices A et C :

```bash
./demo_matrices mul donnees/A.txt donnees/C.txt R.txt
```

Résultat attendu :

```text
4 2
193 -36
37 22
65 78
-10 17
```

### 5.4 Gestion des erreurs

Le programme vérifie les dimensions des matrices et la validité des fichiers.

Les scénarios suivants permettent de tester la gestion des erreurs :

- Addition de matrices avec des dimensions incompatibles.
- Lecture d'un fichier contenant des données invalides.
- Lecture d'un fichier incomplet.
- Lecture d'un fichier inexistant.

Dans ces situations, le programme doit afficher un message d'erreur et renvoyer un code de retour non nul.

### 5.5 Vérification automatique

Le projet utilise GitHub Actions avec le fichier `verification.yml`.

À chaque push, ce workflow vérifie notamment :

- La structure du dépôt et les fichiers nécessaires.
- La compilation du programme.
- Les tests des exercices 1, 2 et 3.
- La gestion de la mémoire avec Valgrind.
- Le fonctionnement de `demo_matrices` dans différents scénarios.

La dernière exécution observée de GitHub Actions s'est terminée avec le statut **Success**. Deux avertissements ont été signalés : l'utilisation de Node.js 20 par `actions/checkout@v4` et l'absence de Makefile dans cette version du dépôt. Le Makefile ayant été ajouté ensuite, sa présence doit être confirmée lors d'une nouvelle exécution.

## 6. Bilan et difficultés

Ce projet nous a permis de mieux comprendre la programmation en C, notamment l'allocation dynamique de mémoire, la manipulation des matrices et la gestion des erreurs. La principale difficulté rencontrée concernait l'utilisation de Git et GitHub. Nous avions créé une autre branche pour travailler sur le projet, mais nous avons oublié de fusionner nos modifications avec la branche principale main jusqu'au dernier jour. Cela a provoqué des difficultés lors de la mise en commun du code. Nous avons donc travaillé ensemble pour résoudre les problèmes de fusion et vérifier les fichiers du projet. Finalement, nous avons réussi à compiler le programme et à faire fonctionner toutes les fonctionnalités. Les tests ont été réussis et Valgrind n'a détecté aucune fuite mémoire dans les scénarios vérifiés. Cette expérience nous a appris l'importance de la communication, du travail en équipe et de la gestion des branches Git.
