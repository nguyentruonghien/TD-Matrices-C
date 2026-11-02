
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
#### A. Code complété 
```
/* (1.a) À COMPLÉTER : les cas MAT_ERR_DIMENSIONS et
     *       MAT_ERR_DEPASSEMENT, sur le même modèle que ci-dessus. */
    case MAT_ERR_DIMENSIONS:   return "dimensions incompatibles";

    case MAT_ERR_DEPASSEMENT: return "depassement de capacite";

/* (1.b) À COMPLÉTER : renvoyer 1 si les QUATRE conditions suivantes
     *       sont vraies, 0 sinon :
     *         - m n'est pas NULL ;
     *         - m->coef n'est pas NULL ;
     *         - 1 <= m->nb_lignes <= MAT_DIM_MAX ;
     *         - 1 <= m->nb_colonnes <= MAT_DIM_MAX.
     *       Une seule instruction suffit :
     *         return m != NULL && m->coef != NULL && ... ;            */
    return m!= NULL
    && m->coef != NULL
    && m->nb_lignes >= 1
    && m->nb_lignes <= MAT_DIM_MAX
    && m->nb_colonnes >= 1
    && m->nb_colonnes <= MAT_DIM_MAX;    

/* (1.c) À COMPLÉTER : si nb_lignes ou nb_colonnes n'est pas compris
     *       entre 1 et MAT_DIM_MAX, renvoyer MAT_ERR_ARGUMENT.          */
    if (nb_lignes < 1 || nb_lignes > MAT_DIM_MAX || nb_colonnes < 1 || nb_colonnes > MAT_DIM_MAX)
    {
    return MAT_ERR_ARGUMENT;
    }

/* (1.d) À COMPLÉTER : allouer chaque ligne.
     *   for (int i = 0; i < nb_lignes; i++) {
     *       coef[i] = mat_calloc(..., ...);    // nb_colonnes entiers
     *       if (coef[i] == NULL) {
     *           for (int k = i - 1; k >= 0; k--)  // lignes déjà
     *               free(coef[k]);                // obtenues
     *           free(coef);                       // puis le tableau
     *           return MAT_ERR_ALLOCATION;
     *       }
     *   }                                                             */
    for (int i = 0; i < nb_lignes; i++) {
    coef[i] = mat_calloc((size_t)nb_colonnes, sizeof *coef[i]);

        if (coef[i] == NULL) {
            for (int k = i - 1; k >= 0; k--) {
                free(coef[k]);
            }

            free(coef);
            return MAT_ERR_ALLOCATION;
         }
    }

/* (1.e) À COMPLÉTER : maintenant que tout a réussi, remplir *res
     *       (trois affectations : coef, nb_lignes, nb_colonnes)
     *       et renvoyer MAT_OK.                                        */
    res->coef = coef;
    res->nb_lignes = nb_lignes;
    res->nb_colonnes = nb_colonnes;

return MAT_OK;
```
#### B. Réalisation

Fichiers : matrice.c, matrice_alloc.c.

- Implémentation des fonctions mat_message_erreur et mat_est_valide.

- Vérification des dimensions avant l'allocation.

- Allocation dynamique des lignes avec mat_calloc et libération de la mémoire en cas d'échec.

- Implémentation de mat_detruire pour libérer la mémoire et réinitialiser la matrice.

#### C. Résultats et vérifications

Tests : 16/16.

Compilation : réussie sans avertissement.v

Valgrind : aucune fuite mémoire ni erreur détectée (ERROR SUMMARY: 0 errors).

#### D. Questions de réflexion

Q3.1. Pourquoi mat_detruire prend-elle un Matrice * et non un Matrice ? Que se passerait-il, pour l'appelant, avec un passage par valeur ?

Réponse : mat_detruire prend un pointeur pour modifier directement la matrice de l'appelant et la remettre à MATRICE_VIDE après la libération de la mémoire. Avec un passage par valeur, seule une copie de la structure serait modifiée, laissant un pointeur pendant dans la matrice originale.


Q3.2. Combien d'appels à calloc et à free faut-il pour une matrice n × p ? Si la k-ième allocation échoue, combien de libérations mat_allouer doit-elle effectuer ?

Réponse : Pour une matrice n × p, il faut n + 1 appels à calloc : un pour le tableau de pointeurs et n pour les lignes. La destruction nécessite n + 1 appels à free. Si la k-ième allocation échoue, mat_allouer doit effectuer k − 1 libérations pour éviter toute fuite mémoire.


Q3.3. Pourquoi appeler deux fois mat_detruire sur la même matrice est-il sans danger ? Est-ce encore vrai après Matrice b = a;, puis destruction de a et b ?

Réponse : Après le premier appel, mat_detruire remet la matrice à MATRICE_VIDE. Le deuxième appel est donc sans danger. En revanche, Matrice b = a; effectue une copie superficielle : les deux matrices partagent la même mémoire. Détruire successivement a et b risque alors de provoquer une double libération (double free).


Q3.4. Pourquoi mat_allouer commence-t-elle par *res = MATRICE_VIDE, avant de vérifier les dimensions ?

éponse : Cette initialisation garantit que la matrice résultat est vide, même si les dimensions sont invalides ou si l'allocation échoue. Elle évite de laisser des pointeurs non initialisés et facilite la gestion des erreurs ainsi que la libération sécurisée de la mémoire.

---

### 3.2 Exercice 2 — Entrées-sorties

#### A. Code complété 
```
/* (2.a) À COMPLÉTER : pour chaque ligne i, afficher chaque
     *       coefficient avec printf(" %8d", m->coef[i][j]),
     *       puis passer à la ligne avec putchar('\n').               */
    for (int i = 0; i < m->nb_lignes; i++) {
        for (int j = 0; j < m->nb_colonnes; j++)
            printf(" %8d", m->coef[i][j]);
        putchar('\n');
    }

/* (2.b) À COMPLÉTER : pour chaque ligne i de la matrice :
     *         - écrire les coefficients séparés par UN espace :
     *             le premier (j == 0) avec "%d", les autres avec " %d" ;
     *         - terminer la ligne avec fputc('\n', f) ;
     *         - si un fprintf renvoie un nombre < 0, ou si fputc
     *           renvoie EOF : return MAT_ERR_ECRITURE;               */
    for (int i = 0; i < m->nb_lignes; i++) {
        for (int j = 0; j < m->nb_colonnes; j++) {
            if (fprintf(f, j == 0 ? "%d" : " %d", m->coef[i][j]) < 0)
                return MAT_ERR_ECRITURE;
        }
        if (fputc('\n', f) == EOF)
            return MAT_ERR_ECRITURE;
    }

/* (2.c) À COMPLÉTER : si nb_lignes ou nb_colonnes n'est pas compris
     *       entre 1 et MAT_DIM_MAX, renvoyer MAT_ERR_FORMAT
     *       (c'est le contenu du fichier qui est faux).               */


/* (2.d) À COMPLÉTER : déclarer une matrice LOCALE et l'allouer :
     *         Matrice tmp;
     *         err = mat_allouer(&tmp, nb_lignes, nb_colonnes);
     *         if (err != MAT_OK) return err;                          */


/* (2.e) À COMPLÉTER : deux boucles imbriquées (i, puis j) qui lisent
     *       chaque coefficient avec lire_entier(f, &tmp.coef[i][j]).
     *       Si une lecture échoue :
     *         mat_detruire(&tmp);  return err;                        */


/* (2.f) À COMPLÉTER : tout a réussi, on transfère le résultat :
     *         *res = tmp;  return MAT_OK;                             */

    /* ligne provisoire : à remplacer en (2.f) */
    
    if (nb_lignes < 1 || nb_lignes > MAT_DIM_MAX ||
        nb_colonnes < 1 || nb_colonnes > MAT_DIM_MAX)
        return MAT_ERR_FORMAT;

    Matrice tmp = MATRICE_VIDE;
    err = mat_allouer(&tmp, nb_lignes, nb_colonnes);
    if (err != MAT_OK)
        return err;

    for (int i = 0; i < nb_lignes; i++) {
        for (int j = 0; j < nb_colonnes; j++) {
            err = lire_entier(f, &tmp.coef[i][j]);
            if (err != MAT_OK) {
                mat_detruire(&tmp);
                return err;
            }
        }
    }
    *res = tmp;
    return MAT_OK;
```
#### B. Réalisation

Fichier : matrice_io.c.

- Implémentation de mat_afficher pour afficher les coefficients des matrices.

- Implémentation de mat_ecrire pour enregistrer les matrices au format texte canonique.

- Implémentation de mat_lire pour lire les matrices et vérifier la validité des données.

Gestion des erreurs de lecture et libération de la mémoire en cas d'échec.

#### C. Résultats et vérifications

Tests : 14/14.

Compilation : réussie sans avertissement.

Valgrind : aucune fuite mémoire ni erreur détectée (ERROR SUMMARY: 0 errors).
#### D. Questions de réflexion

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

#### A. Code complété 
```
/* (3.a) À COMPLÉTER : si a et b n'ont pas le même nombre de lignes
     *       OU pas le même nombre de colonnes,
     *       renvoyer MAT_ERR_DIMENSIONS.                              */
    if (a->nb_lignes != b->nb_lignes || a->nb_colonnes != b->nb_colonnes)
        return MAT_ERR_DIMENSIONS;


    /* (3.b) À COMPLÉTER : allouer r avec les dimensions de a ;
     *         err = mat_allouer(&r, ..., ...);
     *         if (err != MAT_OK) return err;                          */
    Matrice r = MATRICE_VIDE;
    err = mat_allouer(&r, a->nb_lignes, a->nb_colonnes);
    if (err != MAT_OK)
        return err;

/* (3.c) À COMPLÉTER : calculer la somme des coefficients
             *       (i, j) de a et de b EN long long :
             *         long long s = (long long)a->coef[i][j] + ... ;  */
            long long s = (long long)a->coef[i][j] + b->coef[i][j];

/* (3.d) À COMPLÉTER : transférer le résultat :
     *         *res = r;  return MAT_OK;                               */
    *res = r;
    return MAT_OK;

/* (3.e) À COMPLÉTER : le produit a x b n'existe que si le nombre de
     *       COLONNES de a est égal au nombre de LIGNES de b.
     *       Sinon, renvoyer MAT_ERR_DIMENSIONS.                       */
     if (a->nb_colonnes != b->nb_lignes)
        return MAT_ERR_DIMENSIONS;

    /* (3.f) À COMPLÉTER : allouer r de dimensions
     *       (lignes de a) x (colonnes de b).                          */
    Matrice r = MATRICE_VIDE;
    err = mat_allouer(&r, a->nb_lignes, b->nb_colonnes);
    if (err != MAT_OK)
        return err;

/* (3.g) À COMPLÉTER : boucle sur k, de 0 à a->nb_colonnes - 1,
             *       qui ajoute à s le produit a(i,k) x b(k,j) :
             *
             *   for (int k = 0; k < a->nb_colonnes; k++) {
             *       long long p = (long long)a->coef[i][k] * b->coef[k][j];
             *       if ((p > 0 && s > LLONG_MAX - p)
             *           || (p < 0 && s < LLONG_MIN - p)) {
             *           mat_detruire(&r);
             *           return MAT_ERR_DEPASSEMENT;
             *       }
             *       s += p;
             *   }                                                     */
            for (int k = 0; k < a->nb_colonnes; k++) {
                long long produit = (long long)a->coef[i][k] * b->coef[k][j];
                if ((produit > 0 && s > LLONG_MAX - produit) ||
                    (produit < 0 && s < LLONG_MIN - produit)) {
                    mat_detruire(&r);
                    return MAT_ERR_DEPASSEMENT;
                }
                s += produit; 

/* (3.h) À COMPLÉTER : *res = r;  return MAT_OK;                    */
   *res = r;
    return MAT_OK;
```    
#### B. Réalisation
 
Fichier : matrice_calcul.c.

- Implémentation de mat_additionner pour calculer la somme de deux matrices.

- Implémentation de mat_multiplier pour calculer le produit de deux matrices.

- Vérification de la compatibilité des dimensions.

- Détection des dépassements d'entiers et gestion des erreurs.

- Préservation des matrices d'entrée et libération de la mémoire en cas d'échec.

#### C. Résultats et vérifications

Tests : 14/14.

Compilation : réussie sans avertissement.

Valgrind : aucune fuite mémoire ni erreur détectée (ERROR SUMMARY: 0 errors).

#### D. Questions de réflexionA. 

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

#### A. Code complété 
```
/* (4.a) À COMPLÉTER : afficher A et B avec mat_afficher.          */
    mat_afficher("A", &a);
    mat_afficher("B", &b);

/* (4.b) À COMPLÉTER : calculer r.
     *         MatErreur err = addition ? mat_additionner(&a, &b, &r)
     *                                  : mat_multiplier(&a, &b, &r);
     *       Si err != MAT_OK : afficher sur stderr un message utilisant
     *       mat_message_erreur(err) et les dimensions de A et de B,
     *       puis : goto nettoyage;                                    */
    MatErreur err = addition
        ? mat_additionner(&a, &b, &r)
        : mat_multiplier(&a, &b, &r);

    if (err != MAT_OK) {
        fprintf(stderr,
            "Erreur : calcul impossible : %s ",
            mat_message_erreur(err));

        fprintf(stderr,
            "(A : %d x %d, B : %d x %d)\n",
            a.nb_lignes, a.nb_colonnes,
            b.nb_lignes, b.nb_colonnes);

        goto nettoyage;
    }

/* (4.c) À COMPLÉTER : afficher r (nom "R = A + B" ou "R = A x B").  */
    mat_afficher(
        addition ? "R = A + B" : "R = A x B",
        &r
    );

/* (4.d) À COMPLÉTER : tout a réussi :
     *         - afficher "Résultat enregistré dans \"...\"." ;
     *         - statut = EXIT_SUCCESS;                                 */
    printf("Résultat enregistré dans \"%s\".\n", argv[4]);

    statut = EXIT_SUCCESS; 
```
#### B. Réalisation

Fichier : main.c.

- Implémentation des fonctions de chargement et de sauvegarde des matrices.

- Vérification des arguments de la ligne de commande.

- Gestion des opérations d'addition (add) et de multiplication (mul).

- Gestion des erreurs et libération des matrices avant la fin du programme.

#### C. Résultats et vérifications

Compilation : réussie sans erreur ni avertissement.

Exécution : addition et multiplication réussies, résultats conformes aux attentes.

Gestion des erreurs : 3/3 scénarios correctement détectés.

Codes de retour : 0 en cas de succès, 1 en cas d'erreur.

Valgrind : aucune fuite mémoire ni erreur détectée (21 allocations, 21 libérations, 0 erreur).

#### D. Questions de réflexion

Q6.1. Pourquoi le message d'erreur est-il affiché par main.c (ou ses fonctions static) et non par mat_lire ? Pensez à un programme graphique réutilisant la bibliothèque.

Réponse : La fonction `mat_lire` doit seulement lire une matrice et retourner un code d'erreur. C'est au programme principal de décider comment afficher cette erreur. Cette séparation permet de réutiliser la bibliothèque dans différents programmes. Par exemple, une application graphique pourrait afficher l'erreur dans une fenêtre au lieu de l'afficher dans le terminal.


Q6.2. Pourquoi le résultat de fclose est-il testé dans sauvegarder mais pas dans charger ?

Réponse : Dans `sauvegarder`, le résultat de `fclose` est vérifié, car une erreur d'écriture peut apparaître lors de la fermeture du fichier. Certaines données peuvent encore être en mémoire tampon et ne pas avoir été enregistrées. Dans `charger`, le fichier est ouvert en lecture. La fermeture ne sert pas à enregistrer des données. C'est pourquoi le code fourni ne vérifie pas le résultat de `fclose`.


Q6.3. Dans le scénario 8, le calcul a réussi mais l'enregistrement a échoué. Combien de matrices faut-il libérer ? Vérifiez que votre programme le fait.

Réponse : Il faut libérer les trois matrices : A, B et R. Même si l'enregistrement échoue, les trois matrices ont déjà été créées en mémoire. Le programme utilise `goto nettoyage` pour accéder au point de nettoyage commun. Les fonctions `mat_detruire(&r)`, `mat_detruire(&b)` et `mat_detruire(&a)` sont alors appelées pour libérer les trois matrices et éviter les fuites de mémoire.

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
