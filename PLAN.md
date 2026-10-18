# PLAN.md : suivi du projet

> Codex : lis ce fichier en début de session et mets-le à jour à la fin de chaque étape.

## Prochaine action
- [ ] Le porteur teste la v2 (Ollama) dans n8n et donne les résultats (voir Phase 0). Ensuite : proposer le plan détaillé de la correction de A1 à A4, puis attendre validation avant exécution.

## Décisions prises (à remplir avec le porteur du projet)
| Question | Décision | Date |
|---|---|---|
| Messagerie cible (Gmail seul ou aussi Outlook) | Gmail uniquement pour la v1 et le pilote ; Outlook en option ultérieure (OP-06) | 09/10/2026 |
| Installation dédiée par client ou service partagé | Instance n8n dédiée par client (pour garantir la confidentialité) | 09/10/2026 |
| Hébergement de n8n (cloud / auto-hébergé), lieu de stockage | Docker local pour le développement, puis VPS. Hébergeur, pays de stockage et sauvegardes à décider avant le pilote (EJ-02, EJ-03, ES-08) | 09/10/2026 |
| Modèle IA du workflow | Ollama local (`llama3.2:3b`) pour le développement et les tests, sans coût ni clé (conteneur Docker `lms_ai_agency_ollama`). Fournisseur de production non décidé (Ollama sur serveur, OpenAI `gpt-6-luna` / `gpt-6.1-sol`, autre) : à trancher après mesure de la qualité, de la vitesse et du coût. Gemini = référence v1 uniquement | 15/10/2026 |
| Langues à gérer | Français et anglais : réponse dans la langue du mail ; toute autre langue = escalade humaine (T7) | 09/10/2026 |
| Volume de mails attendu par jour | Environ 150 mails / jour pour le pilote | 09/10/2026 |
| Lien avec le projet LMS_AI_Agency (indépendant / à relier) | Produit indépendant, hors périmètre | 09/10/2026 |
| Expérience en code du porteur | Intermédiaire | 09/10/2026 |
| Données et environnement de test | Uniquement de faux mails, y compris des mails inspirés de vrais mails mais anonymisés, sur un compte Gmail de test ; jamais la vraie boîte | 09/10/2026 |
| Canal d'alerte | Canal séparé, à choisir parmi 2 ou 3 options proposées en phase 2 ; l'accès Gmail n'a pas le droit d'envoyer, y compris les alertes (ES-01) | 09/10/2026 |
| Plafond budgétaire | Sans objet en test local (Ollama gratuit). Si la production utilise un service payant : plafond de 10 €/mois à réévaluer avant le pilote sur des coûts réels mesurés (faux mails, environ 150 mails/jour) | 15/10/2026 |
| Environnement de travail | Dossier du projet : `C:\Users\Utilisateur\Documents\Projets\mon-projet-mail-ai` ; Docker (n8n + Ollama) dans `C:\Users\Utilisateur\Documents\Projets\LMS_AI_Agency` (hors projet, Codex ne le lit pas). Sauvegarde : dépôt GitHub privé `wwaxxm/LMS_AI-mail` | 15/10/2026 |
| Critères de réussite du pilote | Au moins 70 % de brouillons utilisables avec peu de retouches ; aucun envoi non validé ; aucun brouillon pour un mail sensible ; 100 % des erreurs signalées | 09/10/2026 |

## Phase 0 : corrections rapides (workflow n8n)
- [x] Migration Gemini vers OpenAI dans `workflow_n8n_v2.json` (09/10/2026) : abandonnée pour les tests (compte API sans crédit)
- [x] Migration vers Ollama local dans `workflow_n8n_v2.json` (15/10/2026) : modèle `llama3.2:3b`, URL `http://ollama:11434/v1/chat/completions`, sans credential, timeout 180000 ms, nœuds « Appel Ollama - Tri » et « Appel Ollama - Rédaction ». Ollama joignable vérifié (modèle installé, `/api/tags` répond)
- [x] A11 partiel (18/10/2026) : test 1 OK (tri : JSON valide avec les 4 clés, `json_schema` accepté par `llama3.2:3b`) ; test 2 : rédaction générée mais de mauvaise qualité en français (mots anglais, phrases bancales, promesse de délai inventée, placeholder `[Votre nom]`). Reste à noter : création du brouillon Gmail (A1 peut le bloquer) et temps total
- [ ] Avant la phase 1 : comparer `llama3.2:3b` avec un modèle local plus gros (par ex. `llama3.1:8b` ou `qwen2.5:7b`) et/ou une API payante sur les mêmes faux mails, avec mesure du temps
- [ ] A1 : corriger `"=={{ $json.to }}"` en un seul `=` (nœud « Créer le brouillon Gmail »)
- [ ] A2 : vérifier l'unité du nœud Wait (30 quoi ?) et le supprimer ou le réduire à quelques secondes
- [ ] A3 : retirer le résumé interne du corps du brouillon
- [ ] A4 : rattacher le brouillon au fil de conversation (threadId), si le nœud le permet
- [ ] Import de `workflow_n8n_v2.json` dans n8n de test, vérification manuelle (voir A11 ci-dessus)

## Phase 1 : sécurité du contenu
- [ ] Ajout de `category` et `sensitive` au tri (A5, EF-02, EF-04)
- [ ] Mails sensibles : pas de brouillon, libellé « À traiter par un humain » + alerte
- [ ] Délimiteurs `<email>...</email>` et consigne anti-injection dans les deux prompts (A6, ES-03, ES-05)
- [ ] Fichier `base_de_connaissances.md` + règle anti-invention (A7, EF-05, EF-06, EF-13)
- [ ] Tests T1 à T6 passés

## Phase 2 : fiabilité
- [ ] Contrôle de la sortie : réponse non vide, JSON valide (A8, EF-09)
- [ ] Workflow d'erreur + alerte au porteur
- [ ] Proposer 2 ou 3 options de canal d'alerte séparé, puis faire valider le choix ; aucun envoi via l'accès Gmail (ES-01)
- [ ] Journal d'audit (A9, EF-10)
- [ ] Interrupteur d'urgence (EF-11)
- [ ] Plafond de coûts + alerte (EF-12) : sans objet avec Ollama local ; à concevoir selon le fournisseur de production choisi
- [ ] Gestion de `Reply-To` (A10)
- [ ] Tests T7 à T13 passés

## Phase 3 : validation complète
- [ ] Jeu de tests T1 à T13 documenté (résultats attendus / obtenus)
- [ ] Revue de sécurité : checklist ES-01 à ES-11
- [ ] Rapport de tests

## Phase 4 : pilote
- [ ] Avant le pilote : décider de l'hébergeur, du pays de stockage et des sauvegardes (EJ-02, EJ-03, ES-08)
- [ ] Avant le pilote : choisir le fournisseur IA de production (qualité, vitesse, coût, confidentialité) en comparant `llama3.2:3b` ou un autre modèle local avec une option payante (par ex. OpenAI) sur de faux mails ; si payant, réévaluer le plafond de 10 €/mois sur des coûts réels (environ 150 mails/jour)
- [ ] Choix du client pilote, information du client
- [ ] Mise en service en mode brouillon uniquement (2 à 4 semaines)
- [ ] Suivi : % de brouillons utilisables, erreurs, escalades, retour du client
- [ ] Valider les critères de réussite : au moins 70 % de brouillons utilisables avec peu de retouches, aucun envoi non validé, aucun brouillon pour un mail sensible, 100 % des erreurs signalées

## Phase 5 : packaging commercial
- [ ] Contrat de service, DPA, clause de responsabilité (à valider par un professionnel)
- [ ] Liste des sous-traitants et conditions vérifiées (EJ-02)
- [ ] Documentation et formation client
- [ ] Tarification définie

## Phase 6 : options (selon demande des clients)
- [ ] Validation Slack / e-mail (OP-01)
- [ ] Résumé quotidien (OP-02)
- [ ] Tableau de bord (OP-03)
- [ ] Envoi automatique limité aux catégories validées (OP-04)

## Journal des sessions
| Date | Étape | Résultat | Décisions / remarques |
|---|---|---|---|
| 09/10/2026 | Mise en cohérence documentaire | PLAN.md et cahier des charges alignés sur les décisions validées ; aucun code ni workflow modifié | Plafond OpenAI de 10 €/mois à réévaluer avant le pilote après mesure des coûts réels à environ 150 mails/jour ; test de rédaction avec gpt-6-luna possible |
| 15/10/2026 | Passage à Ollama local | `workflow_n8n_v2.json` migré vers Ollama (`llama3.2:3b`) ; AGENTS.md, PLAN.md, cahier des charges et guide mis à jour | OpenAI conservé comme option de production non décidée ; tests n8n à faire par le porteur ; A1 à A4 pas encore traitées |
| 18/10/2026 | Tests n8n avec Ollama | Tri OK ; rédaction techniquement OK mais qualité insuffisante | Modèle de 3 milliards de paramètres trop faible pour un produit vendable ; comparaison de modèles à prévoir ; prochaine étape : A1 à A4 avec Codex |
