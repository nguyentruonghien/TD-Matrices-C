
# TD — Stockage de matrices en C

**INSA Centre Val de Loire — 3e année STI**

**Module :** Programmation C  
**Enseignant :** A. ADELL

## Membres du groupe

- Nom et prénom : NGUYEN Truong Hien
- Identifiant GitHub : nguyentruonghien
- Nom et prénom : Ho Viet Manh Hung
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

Valgrind : [À COMPLÉTER].

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

Tests : [À COMPLÉTER]/14.

Compilation : [À COMPLÉTER].

Valgrind : [À COMPLÉTER].

#### C. Questions de réflexion

Q4.1. Pourquoi mat_lire remplit-elle d'abord une matrice locale avant de l'affecter à *res ?
Réponse : [À COMPLÉTER]

Q4.2. La lecture vérifie-t-elle que chaque ligne de la matrice est sur sa propre ligne de texte ? Est-ce nécessaire ?
Réponse : [À COMPLÉTER]

Q4.3. Après mat_lire(f, &m), le flux f est positionné après le dernier coefficient. Comment cette propriété peut-elle être exploitée ?
Réponse : [À COMPLÉTER]

Q4.4. Pourquoi mat_ecrire appelle-t-elle fflush avant de renvoyer MAT_OK ? Cela dispense-t-il de vérifier fclose ?
Réponse : [À COMPLÉTER]

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

Tests : [À COMPLÉTER]/14.

Compilation : [À COMPLÉTER].

Valgrind : [À COMPLÉTER].

#### C. Questions de réflexionA. 

Q5.1. Le paramètre const Matrice *a empêche-t-il d'écrire a->coef[0][0] = 7; ? Que protège exactement const ici ?
Réponse : [À COMPLÉTER]

Q5.2. Pourquoi refuse-t-on mat_additionner(&a, &b, &a) alors que, mathématiquement, on peut remplacer A par A + B ?
Réponse : [À COMPLÉTER]

Q5.3. Combien de multiplications d'entiers effectue mat_multiplier pour des matrices n × p et p × q ?
Réponse : [À COMPLÉTER]

Q5.4. Pourquoi ne suffit-il pas de calculer int s = a + b; puis de tester s < a pour détecter un dépassement ?
Réponse : [À COMPLÉTER]

---

### 3.4 Exercice 4 — Démonstration complète

#### A. Réalisation

Fichier : main.c.

- Implémentation des fonctions de chargement et de sauvegarde des matrices.

- Vérification des arguments de la ligne de commande.

- Gestion des opérations d'addition (add) et de multiplication (mul).

- Gestion des erreurs et libération des matrices avant la fin du programme.

#### B. Résultats et vérifications

Compilation : [À COMPLÉTER].

Exécution des scénarios : [À COMPLÉTER].

Codes de retour : [À COMPLÉTER].

Valgrind : [À COMPLÉTER].

#### C. Questions de réflexion

Q6.1. Pourquoi le message d'erreur est-il affiché par main.c (ou ses fonctions static) et non par mat_lire ? Pensez à un programme graphique réutilisant la bibliothèque.
Réponse : [À COMPLÉTER]

Q6.2. Pourquoi le résultat de fclose est-il testé dans sauvegarder mais pas dans charger ?
Réponse : [À COMPLÉTER]

Q6.3. Dans le scénario 8, le calcul a réussi mais l'enregistrement a échoué. Combien de matrices faut-il libérer ? Vérifiez que votre programme le fait.
Réponse : [À COMPLÉTER]

## 4. Vérifications mémoire

*À compléter.*

## 5. Démonstration finale

*À compléter.*

## 6. Bilan et difficultés

*À compléter.*
