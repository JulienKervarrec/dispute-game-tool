# Chapitre 3 -- Filtrer par block de divergence et produire la liste finale

Une fois toutes les adresses de dispute games recuperees, il faut determiner
lesquelles proposent un etat pour un block L2 posterieur (ou egal) au block
de divergence -- ce sont celles-la qui sont potentiellement corrompues et
doivent etre blacklistees. `filter_dispute_game_addresses_by_l2_divergence_block_number`
interroge, en parallele lui aussi, chaque contrat de dispute game
individuellement : pour chaque adresse, elle instancie un contrat avec une
ABI minimale ne contenant qu une seule fonction, `l2BlockNumber()`, qui
retourne le block L2 propose par ce jeu precis -- une fonction standard de
l implementation `FaultDisputeGame` de l OP Stack. A noter dans le code : le
nom de variable `DISPUTE_GAME_FACTORY` est reutilise comme variable de
boucle locale (`for i, DISPUTE_GAME_FACTORY in enumerate(addresses)`), ce
qui masque temporairement la constante globale du meme nom -- chaque
iteration interroge donc bien l adresse individuelle du jeu courant, malgre
le nom trompeur.

Le filtre final ne conserve que les adresses dont le `l2_block_number`
retourne est superieur ou egal a `l2_divergence_block_number`
(`array_to_comma_seperated_string` convertit ensuite la liste retenue en une
chaine separee par des virgules). Le script imprime abondamment sa
progression a chaque etape (nombre total de jeux, chaque adresse lue, chaque
block L2 associe), ce qui en fait un outil pense pour etre execute
manuellement pendant un incident et suivi en temps reel dans un terminal,
plutot qu integre silencieusement dans un pipeline automatise. La derniere
ligne imprimee, `ADDRESSES_TO_BLACKLIST=...`, est formatee pour etre
directement copiee dans une variable d environnement ou un fichier de
configuration de la couche de gouvernance qui applique effectivement la
blacklist (hors du perimetre de ce script).
