# Chapitre 2 -- Lire le tableau des dispute games via le storage brut

`read_dispute_game_addresses` ne passe par aucune fonction de lecture du
contrat `DisputeGameFactory` -- elle lit directement les slots de storage
EVM via `w3.eth.get_storage_at(address, slot)`. Ce choix est revelateur :
appeler individuellement une fonction de type `gameAtIndex(i)` pour chacun
des N jeux existants demanderait N appels RPC (ou un seul appel avec une
grosse reponse ABI-encodee), alors que lire le storage brut permet de
paralleliser librement les lectures et de controler finement le cout, ce qui
compte quand N peut atteindre plusieurs centaines de jeux.

Le script encode en dur la connaissance du layout de stockage Solidity pour
un tableau dynamique : `ARRAY_SLOT = 104` est le numero du slot ou la
longueur du tableau des jeux est stockee dans le contrat
`DisputeGameFactory` (une constante propre a la version precise du contrat
deploye -- ce script casserait silencieusement si le layout de storage du
contrat changeait). La fonction lit d abord ce slot pour obtenir la
longueur (`length_data = get_storage_at(address, base_slot)`), puis applique
la regle standard de Solidity pour les tableaux dynamiques : les elements ne
sont pas stockes a la suite du slot de longueur, mais a partir du slot
`keccak256(base_slot)` -- calcule ici via
`keccak256(base_slot.to_bytes(32, byteorder="big"))` -- l element d indice i
se trouvant au slot `keccak256(base_slot) + i`.

Une fois la liste des slots calculee, `ThreadPoolExecutor` lance jusqu a 10
lectures RPC en parallele (`fetch_address`), chacune extrayant les 20
derniers octets du mot de 32 octets lu (`[-40:]` apres conversion hex) pour
reconstituer une adresse Ethereum valide -- une valeur de storage Solidity
pour une adresse occupe en effet seulement les 20 octets de poids faible du
slot de 32 octets. Les resultats sont reassembles dans l ordre original via
un tableau pre-alloue (`addresses = [None] * length`), meme si les futures
se completent dans le desordre (`as_completed`).
