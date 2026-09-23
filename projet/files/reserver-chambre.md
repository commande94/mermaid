# Fiche de cas d'utilisation : Réserver une chambre

| | |
|---|---|
| **Cas d'utilisation** | Réserver une chambre |
| **Acteur principal** | Client (sur Internet) ou Agent de voyage partenaire (pour le compte du client) |
| **Acteur secondaire** | Service de paiement externe (arrhes) |
| **Objectif** | Obtenir une chambre d'un hôtel pour une période et un nombre d'occupants donnés. |
| **Cas liés** | `include` Consulter les disponibilités ; `extend` Verser les arrhes ; `extend` Identifier le client final (agent) |

## Précondition

- Les hôtels, catégories, chambres et tarifs sont configurés par le gérant.
- Le demandeur est connecté au site (l'agent de voyage est authentifié avec son compte partenaire).
- Le service de paiement externe est censé être joignable (voir alternative 8b s'il ne l'est pas).

## Règles métier applicables

- **R1** : une chambre n'est jamais réservée deux fois sur une même nuit.
- **R2** : une réservation porte sur **une chambre, une période et un nombre d'occupants** compatible avec la capacité de la chambre.
- **R3** : pour toute réservation faite **plus de 8 jours avant l'arrivée**, des arrhes d'au moins **10 %** du prix du séjour sont exigées ; une réservation non confirmée (arrhes non versées) est annulée automatiquement à J-8.

## Scénario nominal

1. Le demandeur choisit un hôtel, une date d'arrivée, une date de départ et un nombre d'occupants.
2. Le système affiche les chambres **disponibles sur toutes les nuits de la période** et dont la capacité est **≥ au nombre d'occupants**, avec catégorie, confort et prix total du séjour (*include* Consulter les disponibilités).
3. Le demandeur choisit une chambre.
4. Le système revérifie la disponibilité de la chambre (R1) et la compatibilité capacité / occupants (R2), puis affiche un récapitulatif : chambre, période, occupants, prix, montant des arrhes éventuelles, conditions d'annulation et de remboursement.
5. Le demandeur saisit les coordonnées du client (nom, e-mail, téléphone) et valide la réservation.
6. Le système crée la réservation avec le statut **« En attente de confirmation »** et bloque la chambre sur toutes les nuits de la période.
7. Le système détecte que l'arrivée est dans **plus de 8 jours** (R3), calcule les arrhes (≥ 10 % du prix du séjour) et demande le paiement (*extend* Verser les arrhes).
8. Le demandeur saisit ses moyens de paiement ; le système transmet la demande d'encaissement au **service de paiement externe**.
9. Le service de paiement confirme l'encaissement.
10. Le système enregistre les arrhes, passe la réservation au statut **« Confirmée »**, puis envoie une confirmation (numéro de réservation, récapitulatif, date limite d'annulation avec remboursement) au client, et à l'agent de voyage le cas échéant.

## Alternatives et exceptions

| Étape | Alternative | Traitement |
|---|---|---|
| **2a** | Aucune chambre disponible pour la période / le nombre d'occupants. | Le système l'indique et propose d'autres dates, une autre catégorie ou un autre hôtel. Retour à l'étape **1** (ou fin du cas si le demandeur abandonne). |
| **2b** | Le nombre d'occupants dépasse la capacité de toutes les chambres de l'hôtel (R2). | Le système propose de répartir les occupants sur plusieurs réservations ou un autre hôtel. Retour à l'étape **1**. |
| **4a** | La chambre a été réservée par quelqu'un d'autre entre l'étape 3 et l'étape 4 (R1). | Le système refuse, affiche un message et relance la liste actualisée. Retour à l'étape **2**. |
| **5a** | Le demandeur est un **agent de voyage** (*extend* Identifier le client final). | L'agent renseigne l'identité du client final et son propre code partenaire ; la confirmation est envoyée à l'agent **et** au client. Reprise à l'étape **6**. |
| **7a** | L'arrivée est dans **8 jours ou moins** : les arrhes ne sont pas exigées. | La réservation passe directement au statut « Confirmée ». Reprise à l'étape **10**. |
| **8a** | Le paiement des arrhes est **refusé** (carte invalide, plafond, etc.). | Le système informe le demandeur et propose un autre moyen de paiement. Retour à l'étape **8**. S'il abandonne, la réservation reste « En attente » et sera annulée automatiquement à J-8 (cas *Annuler automatiquement les réservations non confirmées*). |
| **8b** | Le **service de paiement est indisponible**. | Le système conserve la réservation « En attente », informe le demandeur qu'il peut payer plus tard via un lien de son e-mail. Fin du cas (postcondition dégradée). |
| **8c** | Le demandeur ne paie pas dans le délai de la session. | La réservation reste « En attente » ; libération de la chambre à l'annulation automatique J-8. Fin du cas. |

## Postcondition

- **Succès :** une réservation existe pour **une chambre, une période et un nombre d'occupants compatible** ; la chambre est indisponible pour ces nuits ; le statut est « Confirmée » (arrhes encaissées, ou non exigées si arrivée ≤ J+8) ; une confirmation a été envoyée.
- **Succès dégradé (8a abandon, 8b, 8c) :** la réservation est « En attente de confirmation » et sera annulée automatiquement à J-8 si les arrhes ne sont pas versées.
- **Échec (2a, 2b abandon) :** aucune réservation n'est créée ; aucun changement de disponibilité.
- **Dans tous les cas :** aucune chambre n'est réservée deux fois sur une même nuit.
