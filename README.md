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


------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Réponses aux questions (Section 2.6.1)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
./monte-carlo 42 10000
Estimated Pi: 3.164000
 ./monte-carlo 42 10000
Estimated Pi: 3.164000
./monte-carlo 123 10000
Estimated Pi: 3.122400
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Question 1 : If you run the program multiple times with the same seed and same number of samples, do you always get the same estimation of π? Explain why (not).
Réponse : Oui, on obtient toujours exactement la même estimation (dans mon cas : 3.164000).
Parce que la fonction rand() suit un algorithme mathématique déterministe. En fixant la graine (seed) à 42, on s'assure que la suite de nombres "aléatoires" générée pour les coordonnées X et Y est identique à chaque exécution. Les points tombent donc aux mêmes endroits, produisant le même résultat.

Question 2: If you run the program with different seeds, do you get different estimations of π?
Réponse : Oui (par exemple, avec la graine 123, j'ai obtenu 3.122400).
Car une graine différente change la séquence des nombres générés. Les points (x,y) sont placés à des positions différentes dans le carré, ce qui modifie légèrement le nombre de points tombant dans le cercle et donc l'approximation finale.

Question 3: What happens to the estimation of π as you increase the number of samples?
Réponse : L'estimation devient plus précise et se rapproche de la valeur réelle de π (≈3.14159).
C'est le principe de la méthode de Monte Carlo : plus la taille de l'échantillon est grande, plus l'erreur statistique diminue. 

Question 4: Is this method of approximating π reproducible? Explain why (not).
Réponse : Oui, elle est reproductible.
Elle est reproductible car, bien qu'elle utilise un processus pseudo-aléatoire, ce processus est entièrement contrôlé par des paramètres d'entrée (seed et samples). Si on fournit les mêmes paramètres sur la même machine, on obtient un résultat identique au bit près.


------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Réponses aux questions (4 Using Nix)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
[nix-shell:~/desktop/software_Evolution]$ ./monte-carlo-nix 42 10000
Estimated Pi: 3.164000
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
4.1 Fonctionnement et Intérêt de Nix

Question 1 : Pourquoi Nix est-il nécessaire pour la reproductibilité par rapport à une installation classique (brew, apt) ?
Réponse : Contrairement aux gestionnaires globaux qui installent les logiciels dans des dossiers système partagés (/usr/local/), Nix utilise le /nix/store. Chaque dépendance y est stockée avec un identifiant unique (hash), garantissant l'utilisation de la version exacte voulue et évitant les conflits de versions.

Question 2 : Quel est l'intérêt du fichier shell.nix ?
Réponse : C'est une spécification déclarative. Au lieu de dépendre d'un environnement pré-installé, on définit explicitement nos besoins (gcc, gnumake). Nix construit alors un environnement virtuel isolé contenant uniquement ces outils.

Question 3 : Comment Nix garantit-il la pureté du build ?
Réponse : Par l'isolation et l'immuabilité. Le processus de build est "enfermé" et ne peut pas être pollué par des fichiers ou bibliothèques externes non listés dans la recette Nix.

4.2 Résultats et Validation:  Observation expérimentale 
[nix-shell:~/desktop/software_Evolution]$ ./monte-carlo-nix 42 10000
Estimated Pi: 3.164000

Question 4 : Le résultat du programme est-il identique avec et sans Nix ? Explique pourquoi.
Réponse : Oui, j'ai obtenu 3.164000 dans les deux cas. Cela confirme que l'environnement Nix a réussi à reproduire les conditions de compilation de mon système. L'avantage est qu'en partageant ce shell.nix sur une autre machine, Nix garantira le même résultat, là où une compilation classique échouerait ou différerait selon les versions installées localement.

Question 5 : Quel est l'intérêt ultime de Nix pour la reproductibilité ?
Réponse : Nix transforme le processus de build en une opération déterministe. Il élimine les "dépendances implicites" et garantit que, peu importe la machine ou le moment, le logiciel est construit à partir d'une recette immuable, assurant ainsi la pérennité et la reproductibilité du binaire.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Réponses aux questions (4.1.6)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
[nix-shell:~/desktop/software_Evolution]$ which gcc
/nix/store/adhxk22r0v0jsm26z0hfrzznwix7l491-gcc-wrapper-15.2.0/bin/gcc

[nix-shell:~/desktop/software_Evolution]$ nix-store --query --requisites $(which gcc)

/nix/store/lcm93hfhp6gwb5ilrrbllkbvchxxj4vl-openssl-3.6.1
/nix/store/0vg1dxwn3gfqp7njbdxjcivhgpdw6p1z-ld64-954.16-lib
/nix/store/sr4c535qpc4hbiqwwn7kdq7k4b9nxg8l-zlib-1.3.1
/nix/store/m6k3gjsdqrnjyrakkv7005lpf33zxj9f-libiconv-109.100.2
/nix/store/wavd09hv269qzms6m60lq3kyyxhzbrw9-libxml2-2.15.1
/nix/store/mpd7lhq5ldp54im947q0xy9312in4xlz-xcbuild-0.1.1-unstable-2019-11-20
/nix/store/1g1dza34rpvk866vdgrdqdc254010pka-xcbuild-0.1.1-unstable-2019-11-20-xcrun
/nix/store/2jx13jbskw8clhigh82pcacbwma699pp-libffi-40
/nix/store/caczl629k3p7slsnh0f277n7qmy0nvb0-llvm-21.1.8-lib
/nix/store/6m7phq5h7mhmvqnrf5s8axc7ng8m97bj-xz-5.8.2
/nix/store/q1ajw7sd508sjf9yc1k735w2ypmg8qay-bzip2-1.0.8
/nix/store/xk3wpz1cqrs48advl4hjlsv2iph0kzsk-xar-minimal-501-lib
/nix/store/315mbsdq9wicvvgwfxa89vydp8mvljb4-ld64-954.16
/nix/store/9fir0fhbsbra72kqvvw768g8y249am6b-clang-21.1.8-lib
/nix/store/g3mdzc37n20mk89azsm0rqp5mn9g9qjv-clang-21.1.8
/nix/store/pdkxf647n2cci3s1cr61xf4fij8mnxdz-cctools-1010.6-libtool
/nix/store/vjafmpcy0sl9dbmwjwwmlal6d5apnwb2-cctools-1010.6
/nix/store/xr01y2hxsjqi30g8fkz5pbr768h3r4yd-llvm-21.1.8
/nix/store/ymlpj1ajg53a2rblavf8zbaiwwaliv84-bash-5.3p9
/nix/store/38qrlynsqfni68n833s82lvanh2aqz8w-cctools-binutils-darwin-1010.6
/nix/store/3q1qkpz8niwgybl8lj7cp1ljc6hb1ycx-libutil-72
/nix/store/48dn6b2q7zg5rqc0v0ar5m5p1jl74v0m-Csu-88
/nix/store/50fbl1lxqfynqf1m7n18a05rzwsjsnph-pcre2-10.46
/nix/store/5bwhjr63kxw3vbjs3qi03nw43xl4cvy0-libiconv-109.100.2-dev
/nix/store/5y9y97ckgs8bq3brxan5crpjv0p7a1mh-libsbuf-14.1.0
/nix/store/76zfamg10y58z4fngavfm75lbqwqkr4h-libresolv-91
/nix/store/8rfp33a81x4260821dcnmi5w83gmnfww-cups-headers-2.4.16
/nix/store/9mka8v87xj94p54sm8xkmk3x1pnahzxi-libsbuf-14.1.0-dev
/nix/store/grnv1vxslcgj80bxk1a37iri69vp1830-libresolv-91-dev
/nix/store/dmbm1z3yllxj1lb65hyhvc2fgma8vs9q-ncurses-6.6-man
/nix/store/y8vx6lw990ik9zdwykc6izwiwqsnnl15-ncurses-6.6
/nix/store/rhwy24xqa5n4qryp8aiksq0a0nzikb6k-ncurses-6.6-dev
/nix/store/86rhcgjinamc228cy07vyhd1g629zw2s-apple-sdk-14.4
/nix/store/8jwb6yvaqdn3cs9qz56n7a8cl67afrlv-gmp-with-cxx-6.3.0
/nix/store/n633lmg2bfc0lmd19cgqnlk4pfs6zdlc-expand-response-params
/nix/store/rx17m3vcnjjhh509x9jwkqf5pnbzv6dq-libSystem-B
/nix/store/xfwwgxfk71g0vkl2k2ggki8s5mgc4ki4-coreutils-9.10
/nix/store/bhg8rjr3n4dlfy0icrrg7szbhvldj74c-cctools-binutils-darwin-wrapper-1010.6
/nix/store/f1al1g5ibiazlf6csqdn518i5499vvy4-gettext-0.26
/nix/store/mmhlygv45xx76bp59mngchb63986n0s6-gcc-15.2.0-lib
/nix/store/s82qq8lmcybhncli5zv3r3h122dcdi4v-gnugrep-3.12
/nix/store/yl6x6b735cjd0y1f10jrpq6p72ibg3fr-mpfr-4.2.2
/nix/store/r8826shn8ya2lbpl17mp9gdchslqjbhv-libmpc-1.3.1
/nix/store/zm7mjdcwyq14ykrakmbrdh1bp1wm0qvx-gcc-15.2.0
/nix/store/adhxk22r0v0jsm26z0hfrzznwix7l491-gcc-wrapper-15.2.0
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Question 1. Compare le binaire résultant avec d'autres étudiants. Est-il le même ?
Réponse : Oui, si nous utilisons la même architecture système (ex: x86_64-linux), le hash SHA-256 du binaire sera identique.
Car Nix garantit que les dépendances (compilateurs, bibliothèques) sont strictement les mêmes grâce au hash du /nix/store, ce qui rend le processus de build déterministe.

Question 2. Compare le chemin d'installation (/nix/store/...) du binaire. Est-il le même ?
Réponse : Oui, il sera identique.
Car  le chemin dans le /nix/store est dérivé cryptographiquement des entrées de build. Comme nous utilisons le même fichier flake.lock, nos entrées sont identiques.

Question 3. Si tu reconstruis plusieurs fois, obtiens-tu le même résultat ?
Réponse : Oui, c'est la propriété de pureté de Nix.

Question 4. nix shell nixpkgs#hello vs nix profile add nixpkgs#hello ?
Réponse : nix shell crée un environnement temporaire pour l'exécution immédiate sans modifier le profil utilisateur, tandis que nix profile add installe le paquet de manière persistante dans ton profil utilisateur (~/.nix-profile).

Question 5. Quel est le rôle du /nix/store et pourquoi est-il immuable ?
Réponse : C'est le stockage centralisé. Il est immuable pour garantir qu'aucun processus (ni l'utilisateur, ni une mise à jour) ne puisse modifier un paquet une fois installé, évitant ainsi la "corruption" des dépendances.

Question 6. Que fait flake.lock et pourquoi est-ce crucial ?
Réponse : Il "fige" les versions exactes des dépendances (les révisions Git des inputs). C'est crucial car cela garantit que tout collaborateur qui utilise ton projet obtiendra les mêmes versions de bibliothèques, même si le dépôt nixpkgs a été mis à jour entre-temps.

Question 7. Purity & Sandboxing : Que se passe-t-il si le build essaie de lire /etc/passwd ?
Réponse : Le build échouera.
Car Nix exécute les builds dans un "sandbox" (bac à sable). Sans accès explicite déclaré dans la dérivation, le processus n'a accès à aucun fichier système. Cela garantit qu'aucun élément externe ne peut influencer ou corrompre la reproductibilité.

Question 8. Si une dépendance est mise à jour, comment Nix maintient-il la reproductibilité ?
Réponse : Grâce au fichier flake.lock. Tant que on ne mets pas à jour le  fichier de verrouillage, le  projet continue d'utiliser l'ancienne version des dépendances, assurant la stabilité.

Question 9. Partager un environnement avec Java et GCC : À quoi ressemble le flake.nix ?
Réponse : Il doit lister pkgs.gcc et pkgs.jdk dans la liste packages de notre  devShell. Oui, il est impératif de partager le flake.lock pour garantir que tout le monde utilise la même version de Java et de GCC.


