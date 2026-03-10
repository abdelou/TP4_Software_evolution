# TP4_Software_evolution

Question 1:How likely is it that two different strings produce an identical cryptographical hash with SHA-256?
Réponse : La probabilité est extrêmement faible, au point d'être considérée comme négligeable ou nulle en pratique.

Car pour trouver une "collision" (deux entrées avec le même résultat) avec SHA-256, il faudrait environ \mathbf{3.4×10^{38}} tentatives.

Question 2: Think about how this algorithm works and try it with different inputs. Based on your understanding, try to come up with two different strings that produce the same checksum. This exercise will help you understand the limitations of checksum algorithms and why strong cryptographic hashes like SHA-256 are used in practice.

Reponse : Contrairement au SHA-256, l'algorithme dans le script checksum.py (Listing 1) est très simple et n'est pas sécurisé

L'algorithme multiplie la valeur Unicode d'un caractère par sa position. pour  trouver une collision en inversant deux caractères ou en modifiant les valeurs pour que la somme reste la même.

Exemple : "ab" et une autre combinaison calculée pour donner le même poids total. Cet exercice montre pourquoi on utilise des hachages complexes comme SHA-256 plutôt que de simples sommes pondérées
