------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# TP4_Software_evolution
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Question 1:How likely is it that two different strings produce an identical cryptographical hash with SHA-256?
Réponse : La probabilité est extrêmement faible, au point d'être considérée comme négligeable ou nulle en pratique.
Car pour trouver une "collision" (deux entrées avec le même résultat) avec SHA-256, il faudrait environ \mathbf{3.4×10^{38}} tentatives.

Question 2: Think about how this algorithm works and try it with different inputs. Based on your understanding, try to come up with two different strings that produce the same checksum. This exercise will help you understand the limitations of checksum algorithms and why strong cryptographic hashes like SHA-256 are used in practice.

Reponse : Contrairement au SHA-256, l'algorithme dans le script checksum.py (Listing 1) est très simple et n'est pas sécurisé
L'algorithme multiplie la valeur Unicode d'un caractère par sa position. pour  trouver une collision en inversant deux caractères ou en modifiant les valeurs pour que la somme reste la même.
Exemple : "ab" et une autre combinaison calculée pour donner le même poids total. Cet exercice montre pourquoi on utilise des hachages complexes comme SHA-256 plutôt que de simples sommes pondérées


Question 3 : Is it advised to use a tool such as TAR or Gzip to consolidate a filesystem object (e.g., symlink, file, directory) into a single file, then compute the digest of that resulting file?

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Réponse : Non, ce n'est pas conseillé pour garantir la reproductibilité.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Réponses aux questions (Section 2.2.1)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
shasum -a 256 hello-world
6ff46ac2f837863a2e376a951f329002a88dec9ae834445a96cd7d738fdf2359  hello-world

shasum -a 256 hello-world
bd69a258d4ea93c0defce872ca432112ed10508116638e3a6e89baf5cd69667d  hello-world

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Question 1: Le binaire produit est-il identique à chaque compilation ?
Réponse : Non, le binaire produit n'est pas identique. Bien que le code source (hello-world.c) soit strictement le même, la valeur du hachage (checksum) change à chaque fois que l'on recompile le programme à un moment différent.

Question 2: Pourquoi ?
Réponse : Cela est dû à l'utilisation des macros spéciales du préprocesseur C : __DATE__ et __TIME__. Ces macros injectent la date et l'heure exactes de la compilation directement dans le code binaire. Par conséquent, deux builds effectués à quelques minutes d'intervalle produisent des fichiers binaires différents au niveau des octets.

Question 3: Quel est l'impact sur la reproductibilité ?
Réponse : Ce programme n'est pas reproductible. La reproductibilité logicielle exige que, pour un code source donné, on obtienne toujours un binaire identique bit à bit. Ici, l'environnement (le temps système) influence le résultat final, ce qui rend impossible la vérification de l'intégrité du binaire par simple comparaison de hachage.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Réponses aux questions (Section 2.3.1)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 ./random-generator
Random number: 16807
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Question 1:  Si vous lancez le programme plusieurs fois (sans recompiler), obtenez-vous toujours le même résultat ?
Réponse : Oui, tu obtiendras exactement le même nombre à chaque exécution.
Bien que la fonction rand() soit censée être imprévisible, elle utilise un algorithme déterministe. Comme le programme ne définit pas de "graine" (seed) différente à chaque fois, l'état interne est initialisé avec la même valeur par défaut au démarrage

Question 2: Est-ce que cette "aléatoirité" est reproductible ?
Réponse : Oui, elle est parfaitement reproductible.
Puisque le processus produit strictement la même séquence de nombres sur une même machine, le comportement est déterministe et donc reproductible

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Réponses aux questions (Section 2.4.1)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
./random-seed
Random number: 743313204

./random-seed
Random number: 743346818

./random-seed
Random number: 743363625

./random-seed
Random number: 743380432

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Question 1: If you compile the program multiple times, do you always get the same output? Explain why (not).
Réponse : Oui, on obtient toujours le même fichier binaire (le même exécutable).
Contrairement à l'exercice précédent avec __DATE__ et __TIME__, le code source de random-seed.c est désormais fixe. Il ne contient aucune instruction qui demande au compilateur d'insérer des données variables lors de la compilation. Le processus de construction (build) est donc reproductible.

Question 2: If you run the program multiple times (without recompiling), do you always get the same output? Explain why (not).
Réponse : Non, on obtient un résultat différent à chaque exécution (comme le montrent mes tests : 743313204, 743346818, etc.).
Car  Cela est dû à la fonction srand(time(NULL)). La fonction time(NULL) renvoie le nombre de secondes . Comme cette valeur change à chaque seconde, la "graine" (seed) du générateur change, ce qui produit une séquence de nombres différente à chaque lancement.

Question 3: Does this version of the application behave differently (at runtime)? Explain why (not)?
Réponse : Oui, elle se comporte différemment.
Car La version précédente (Section 2.3) était déterministe : elle affichait le même nombre à chaque fois car la graine par défaut était toujours la même. Cette version est non-déterministe à l'exécution car elle dépend d'une entrée externe imprévisible (le temps système). Elle simule donc un "vrai" hasard pour l'utilisateur, mais perd en reproductibilité d'exécution.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Réponses aux questions (Section 2.5)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
./random-user-seed 42
Random number: 705894
./random-user-seed 42
Random number: 705894
./random-user-seed 100
Random number: 1680700
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Question 1:  If you run the program multiple times with the same seed, do you always get the same output? Explain why (not).
Réponse : Oui. Comme on le voit dans mes tests avec la graine 42, le résultat est systématiquement 705894.
Car l'algorithme de génération de nombres pseudo-aléatoires (rand()) est une suite mathématique fixée. En donnant la même valeur de départ (seed) avec srand(), on force l'algorithme à recalculer exactement la même suite de nombres.

Question 2: If you run the program with different seeds, do you get different results?
Réponse : Oui (par exemple, avec la graine 100, j'obtiens 1680700).
J'ai Changer la graine déplace le point de départ de l'algorithme dans sa séquence mathématique, ce qui produit un résultat différent.

Question 3 :What is the benefit of this approach for software reproducibility?
Réponse : Cette approche permet de rendre un comportement complexe prévisible et reproductible.
C'est crucial pour le débogage (reproduire un bug qui dépend du hasard) et pour la recherche scientifique (permettre à d'autres chercheurs d'obtenir exactement les mêmes résultats avec le même code).






