# Chapitre 1 -- Presentation de dispute-game-tool

Ce depot est volontairement minuscule : un seul script Python
(`script/fetch_dispute_games.py`), un `Makefile` d une cible, et un
`requirements.txt`. Il n implemente aucun contrat, aucun service -- c est un
outil operationnel d incident, concu pour un scenario precis de l
architecture des preuves de fraude (fault proofs) d un rollup OP Stack comme
Base.

Le contexte est le suivant. Un rollup avec preuves de fraude s appuie sur une
`DisputeGameFactory` : n importe qui peut y creer un "dispute game" (jeu de
litige) qui propose un etat L2 (un output root) pour un block L2 donne, et
challenger ou defendre cette proposition on-chain jusqu a resolution. Si un
bug de client cause une divergence (le client produit un mauvais etat L2 a
partir d un certain block), tous les dispute games qui proposent un etat
calcule apres ce block de divergence deviennent potentiellement invalides --
ils doivent etre exclus (blacklist) du processus de resolution avant que
l un d eux ne soit finalise a tort. Le README l explicite : ce script
determine la liste des jeux a blacklister, en lisant l etat de la chaine
*offchain* -- c est-a-dire en interrogeant un noeud RPC directement plutot
qu en executant cette recherche dans un contrat on-chain, ce qui serait
lent, couteux en gas, et limite par les capacites d enumeration du contrat.
Le script prend trois variables d environnement en entree : `RPC_URL` (le
noeud a interroger), `DISPUTE_GAME_FACTORY` (l adresse de la factory a
inspecter) et `L2_DIVERGENCE_BLOCK_NUMBER` (le premier block L2 pour lequel
l etat propose est suspect), et produit en sortie une chaine
`ADDRESSES_TO_BLACKLIST` prete a etre injectee dans un processus de
gouvernance ou de configuration en aval.
