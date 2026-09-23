# Fiche de cas d'utilisation : Facturer le départ

| | |
|---|---|
| **Cas d'utilisation** | Facturer le départ |
| **Acteur principal** | Réceptionniste |
| **Acteur secondaire** | Service de paiement externe (encaissement du solde) |
| **Objectif** | Clôturer le séjour du client en produisant une facture juste (chambre, prestations, taxe de séjour) et en encaissant le solde. |
| **Cas liés** | `include` Relever le compteur téléphonique ; `include` Encaisser un paiement |

## Précondition

- Le séjour est **en cours** : l'arrivée a été enregistrée (remise des clés, relevé initial du compteur téléphonique).
- Les consommations du séjour (restaurant, bar, téléphone) ont été enregistrées au fil du séjour.
- Le client est présent à la réception pour son départ.

## Scénario nominal

1. Le réceptionniste sélectionne le séjour du client (numéro de chambre ou nom).
2. Le système vérifie que le séjour est en cours et affiche son récapitulatif (chambre, période, occupants, arrhes déjà versées).
3. Le réceptionniste relève le **compteur téléphonique final** (*include* Relever le compteur téléphonique) ; le système calcule la consommation téléphonique (compteur final - compteur initial) × tarif de l'unité.
4. Le système calcule le **prix de la chambre** : nombre de nuits × tarif de la catégorie, **selon le nombre d'occupants**.
5. Le système totalise les **prestations** du séjour : restaurant, bar, téléphone.
6. Le système calcule la **taxe de séjour** (nombre d'occupants assujettis × nombre de nuits × taux applicable).
7. Le système établit la facture détaillée (chambre + prestations + taxe de séjour), déduit les **arrhes** déjà versées et affiche le **solde à payer**.
8. Le client valide la facture ; le réceptionniste lance l'encaissement du solde auprès du **service de paiement externe** (*include* Encaisser un paiement).
9. Le service de paiement confirme l'encaissement.
10. Le système enregistre le paiement, passe la facture au statut **« Payée »**, clôture le séjour, marque la chambre comme libre (à préparer), récupère les clés, puis remet ou envoie la facture au client.

## Alternatives et exceptions

| Étape | Alternative | Traitement |
|---|---|---|
| **2a** | Le séjour est introuvable ou **l'arrivée n'a pas été enregistrée**. | Le système refuse la facturation et invite à enregistrer l'arrivée ou à vérifier la recherche. Fin du cas (échec). |
| **3a** | Compteur final **inférieur au compteur initial** ou illisible. | Le système bloque le calcul ; le réceptionniste resaisit ou corrige la valeur (avec motif tracé). Retour à l'étape **3**. |
| **4a** | Le **nombre réel d'occupants** diffère du nombre réservé. | Le réceptionniste corrige le nombre d'occupants (dans la limite de la capacité) ; le système applique le tarif correspondant au nombre réel. Reprise à l'étape **4**. |
| **7a** | Le client **conteste une ligne** de la facture (consommation erronée). | Le réceptionniste corrige ou annule la ligne (trace conservée) ; le système recalcule. Retour à l'étape **5**. |
| **7b** | Les **arrhes dépassent le total** (départ anticipé, prestations moins élevées que prévu). | Le solde est négatif : le système déclenche le remboursement du trop-perçu via le service de paiement. Reprise à l'étape **10**. |
| **8a** | Le paiement est **refusé** (carte, plafond, etc.). | Le système propose un autre moyen de paiement. Retour à l'étape **8**. Si le client n'a aucun moyen valide, la facture reste « Impayée » et le gérant est informé ; fin du cas (postcondition dégradée). |
| **8b** | Le **service de paiement est indisponible**. | Le système garde la facture « À encaisser », clôture le séjour et permet un règlement différé (lien de paiement). Fin du cas (postcondition dégradée). |

## Postcondition

- **Succès :** la facture (chambre selon occupants, prestations, taxe de séjour, moins arrhes) est émise et au statut « Payée » ; le séjour est clôturé ; la chambre est libérée ; les clés sont restituées.
- **Succès dégradé (8a, 8b) :** la facture est émise mais « Impayée » ou « À encaisser » ; le séjour est clôturé ; le solde reste à recouvrer.
- **Échec (2a) :** aucune facture n'est créée ; le séjour reste dans son état initial.
