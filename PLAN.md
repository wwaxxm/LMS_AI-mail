# PLAN.md : suivi du projet

> Codex : lis ce fichier en début de session et mets-le à jour à la fin de chaque étape.

## Prochaine action
- [ ] Compléter A4 avec un faux mail envoyé depuis une autre adresse de test : le rattachement fonctionne sur une vraie conversation, mais l’essai du 19/10/2026 utilise un mail envoyé au même compte. A1, A2 et A3 confirmés dans le workflow testé ; A11 : création avec Thread ID réussie et durée totale relevée (41,548 s).
- [ ] Aligner l’export local `workflow_n8n_v2.json` sur la référence explicite corrigée et sauvegardée dans n8n : le fichier contient encore `={{ $json.threadId }}`. Avant la phase 1, comparer les modèles sur les mêmes faux mails ; tests T1 à T13, dont T5 et T6, restant à exécuter.

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
- [x] A11 partiel (18–19/10/2026) : tri avec JSON valide à 4 clés et `json_schema` accepté ; les deux appels Ollama ont réellement été exécutés le 19/10. Exécution complète n8n **#15**, démarrée le **19/10/2026 à 01:37:16** (Europe/Paris), statut **Succeeded**, durée totale **41,548 s** ; brouillon créé avec Thread ID. La durée exclut l’attente du polling ; premier appel après inactivité : **inconnu**. Qualité en français encore insuffisante : l’essai du 19/10 contient notamment « empresas » et `[Votre nom]` ; l’essai du 18/10 comportait une promesse de délai inventée. Résistance aux injections et tests T1 à T13 non validés.
- [ ] Avant la phase 1 : comparer `llama3.2:3b` avec un modèle local plus gros (par ex. `llama3.1:8b` ou `qwen2.5:7b`) et/ou une API payante sur les mêmes faux mails, avec mesure du temps
- [x] A1 : destinataire corrigé en `={{ $json.to }}` (18/10/2026) ; création du brouillon sans Thread ID validée par le porteur dans n8n
- [x] A2 : Wait supprimé ; branche client raccordée directement à « Appel Ollama - Rédaction » (18/10/2026)
- [x] A3 : corps limité à la réponse ; résumé conservé dans `internalSummary`, sortie « Préparer le brouillon », uniquement dans les données d’exécution n8n (18/10/2026) ; validé par le porteur dans n8n
- [ ] A4 partiel (19/10/2026) : référence explicite `{{ $('Préparer le brouillon').item.json.threadId }}` corrigée dans l’interface n8n et sauvegarde vérifiée après rechargement. Test réel, sans données épinglées : objet `TEST A4 - 19 octobre - conversation 01`, mail non lu envoyé au même compte de test. `threadId` du Trigger, de « Préparer le brouillon » et de `message.threadId` Gmail identiques : **`1a1515efe5863f83`**. Nœud Gmail réussi ; brouillon constaté dans la même conversation Gmail, destinataire correct, aucun résumé interne dans le corps ; brouillon non envoyé. **Reste à valider : expéditeur externe au compte de test.**
- [x] Vérification manuelle du workflow n8n de test (19/10/2026) : workflow non publié pendant l’essai, absence de Wait confirmée, A1/A3 confirmés, deux appels Ollama exécutés et brouillon créé (exécution #15). Cela ne confirme pas l’alignement de l’export local : `workflow_n8n_v2.json` conserve l’ancienne expression A4.

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
| 18/10/2026 | Phase 0 : corrections A1 à A4 | Destinataire corrigé, Wait supprimé, résumé séparé, threadId transmis ; JSON et connexions validés ; code de préparation vérifié sur données fictives | Base : `235d2ab` ; prompts et nœuds Ollama inchangés (`llama3.2:3b`, sans credential). Import n8n, tests Gmail et T1 à T13 non exécutés ici ; A11 reste partiel (création brouillon et temps total). Choix du modèle futur à décider après comparaison ; aucun changement de modèle dans cette étape |
| 18/10/2026 | Retour de test et correction A4 | A1 et A3 validés par le porteur : brouillon créé sans Thread ID, résumé absent du corps. A4 : référence explicite au champ de « Préparer le brouillon » dans le nœud Gmail | Ancienne expression évaluée à vide dans l’interface ; identifiant utilisé : `test-thread-1` (fictif). JSON vérifié ; validation A4 en attente sur une vraie conversation du compte Gmail de test. A11 : création sans Thread ID confirmée, temps total restant à noter. Prompts, modèle et nœuds Ollama inchangés |
| 19/10/2026 | Test réel A4 et mesure A11 | Exécution n8n #15 réussie en 41,548 s ; deux appels Ollama réels ; Thread ID `1a1515efe5863f83` conservé du Trigger au brouillon ; rattachement vérifié dans Gmail, bon destinataire et résumé interne absent du corps | Mail envoyé au même compte : validation A4 avec expéditeur externe encore requise. Brouillon non envoyé ; premier appel après inactivité inconnu. Référence A4 corrigée et sauvegardée dans n8n, export local encore à aligner. Mise à jour de PLAN.md uniquement ; aucune phase 1 engagée |
