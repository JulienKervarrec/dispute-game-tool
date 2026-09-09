# Chapitre 4 -- Limites et perimetre de ce parcours

Ce parcours couvre l integralite du seul fichier de logique du depot,
`script/fetch_dispute_games.py`, ainsi que le `Makefile` et le `README.md`
qui en documentent l usage. Le depot ne contient rien d autre : aucun
contrat Solidity, aucun test, aucune configuration CI au-dela d un
`requirements.txt`.

Sont volontairement laisses hors champ, faute d etre presents dans ce
depot : l implementation reelle du contrat `DisputeGameFactory` et de
`FaultDisputeGame` (dont ce script depend implicitement via le numero de
slot `104` et la signature `l2BlockNumber()`, mais qui vivent dans le depot
principal de l OP Stack) ; le mecanisme exact par lequel la liste
`ADDRESSES_TO_BLACKLIST` produite ici est ensuite appliquee on-chain
(vraisemblablement via un contrat de type `AnchorStateRegistry` ou un
processus de gouvernance du Guardian, non presents ici) ; et le contexte
plus large de reponse a incident dans lequel cet outil s inserait.

L objectif reste le meme que pour les parcours precedents de cette
bibliotheque : comprendre precisement ce que fait ce script, ligne par
ligne, sans pretendre reconstituer l architecture complete des preuves de
fraude de Base a partir d un seul outil d incident isole. Ce parcours compte
volontairement moins de chapitres que les precedents : la taille reelle du
depot (un seul script d environ 110 lignes) ne justifie pas d en inventer
davantage.
