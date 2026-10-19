# PLAN.md : suivi du projet

> Codex : lis ce fichier en début de session et mets-le à jour à la fin de chaque étape.

## Prochaine action
Stratégie validée le 19/10/2026 : construire et figer la logique du workflow avec `llama3.2:3b`, avec des règles de rédaction et de mise en page appliquées surtout par le code (`REGLES_REDACTION.md`), puis passer à une API payante avant le pilote. Ordre à suivre, une session Codex par sous-étape :
- [ ] **Porteur, tout de suite** : dans n8n, remettre `llama3.2:3b` dans les deux appels Ollama, retirer le faux devis épinglé au Gmail Trigger, vérifier que le workflow n'est pas publié. Restauration non confirmée à ce jour.
- [ ] **Porteur, tout de suite** : passer le dépôt `wwaxxm/LMS_AI-mail` en **privé** dans GitHub (Settings, General, Danger Zone, Change visibility). Visibilité **publique confirmée par l’API GitHub le 19/10/2026** ; aucun changement de visibilité effectué pendant la synchronisation demandée. Voir aussi `preuves/` (capture avec une adresse mail du compte de test) et l'adresse d'auteur des anciens commits.
- [ ] **Codex, phase 1a (sécurité)** : A5 et A6 : `category` et `sensitive` au tri, escalade sans brouillon des mails sensibles, délimiteurs `<email>` et consigne anti-injection. Test T5 jamais exécuté à ce jour : le comportement de `llama3.2:3b` face à l'injection est inconnu.
- [ ] **Codex, phase 1b (rédaction)** : A7 et `REGLES_REDACTION.md` : prompt court avec un exemple, formule d'appel et signature ajoutées par le code, contrôles C1 à C8, `max_tokens` 400, `base_de_connaissances.md`.
- [ ] **Porteur + Codex** : recette sur les 4 faux mails de `faux_mails_comparaison_modeles.json` plus un mail en anglais et un expéditeur sans nom, avec `llama3.2:3b`. Consigner attendu / obtenu, durée, nombre d'essais.
- [ ] **Codex, réglage local** : passer les essais automatiques des deux appels Ollama de 5 à 2 : 5 essais de 180 s peuvent bloquer n8n jusqu'à environ 15 minutes (voir journal du 19/10).
- [ ] Compléter A4 avec un faux mail envoyé depuis une **autre** adresse de test (l'essai du 19/10 était envoyé au même compte).
- [ ] Phases 2 et 3 (fiabilité, tests T1 à T13), puis **gel de la « version Llama »** (voir « Bascule vers une API payante »).

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
| Environnement de travail | Dossier du projet : `C:\Users\Utilisateur\Documents\Projets\mon-projet-mail-ai` ; Docker (n8n + Ollama) dans `C:\Users\Utilisateur\Documents\Projets\LMS_AI_Agency` (hors projet, Codex ne le lit pas). Sauvegarde : dépôt GitHub `wwaxxm/LMS_AI-mail` (doit être privé, visibilité à vérifier, voir « Prochaine action ») | 15/10/2026 |
| Stratégie IA | Développer et figer la logique avec `llama3.2:3b` ; `qwen2.5:7b` écarté en local (environ 0,5 token/s sur ce PC, appel coupé à 180 s). Règles de rédaction appliquées surtout par le code. Passage à une API payante avant le pilote, en rejouant la même recette | 19/10/2026 |
| Critères de réussite du pilote | Au moins 70 % de brouillons utilisables avec peu de retouches ; aucun envoi non validé ; aucun brouillon pour un mail sensible ; 100 % des erreurs signalées | 09/10/2026 |

## Phase 0 : corrections rapides (workflow n8n)
- [x] Migration Gemini vers OpenAI dans `workflow_n8n_v2.json` (09/10/2026) : abandonnée pour les tests (compte API sans crédit)
- [x] Migration vers Ollama local dans `workflow_n8n_v2.json` (15/10/2026) : modèle `llama3.2:3b`, URL `http://ollama:11434/v1/chat/completions`, sans credential, timeout 180000 ms, nœuds « Appel Ollama - Tri » et « Appel Ollama - Rédaction ». Ollama joignable vérifié (modèle installé, `/api/tags` répond)
- [x] A11 partiel (18–19/10/2026) : tri avec JSON valide à 4 clés et `json_schema` accepté ; les deux appels Ollama ont réellement été exécutés le 19/10. Exécution complète n8n **#15**, démarrée le **19/10/2026 à 01:37:16** (Europe/Paris), statut **Succeeded**, durée totale **41,548 s** ; brouillon créé avec Thread ID. La durée exclut l’attente du polling ; premier appel après inactivité : **inconnu**. Qualité en français encore insuffisante : l’essai du 19/10 contient notamment « empresas » et `[Votre nom]` ; l’essai du 18/10 comportait une promesse de délai inventée. Résistance aux injections et tests T1 à T13 non validés.
- [x] Comparaison de modèles (19/10/2026) : abandonnée pour `qwen2.5:7b` en local. Tri valide, mais rédaction trop lente sur CPU (appel coupé à 3 min 0 s par le timeout de 180 s, environ 90 tokens générés, soit environ 0,5 token/s d'après les logs Ollama ; estimation). Décision : rester sur `llama3.2:3b`. La comparaison de qualité se fera avec les API candidates (phase « Bascule »).
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
- [ ] Règles de rédaction et de mise en page : `REGLES_REDACTION.md` (formule d'appel et signature par le code, contrôles C1 à C8, prompt court avec exemple)
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

## Bascule vers une API payante (après la phase 3, avant le pilote)
- [ ] Définir « version Llama terminée » : phases 1 à 3 faites, T1 à T13 passés sur la logique (escalade, anti-injection, contrôles), recette de rédaction passée pour la mise en page. La qualité de style de `llama3.2:3b` n'est pas un critère de sortie : elle sera jugée sur l'API.
- [ ] Geler cette version dans Git (tag, par exemple `v3-llama`), puis travailler sur une branche pour l'API
- [ ] Choisir 2 candidats (par exemple OpenAI et un second fournisseur) en vérifiant prix, modèles et conditions de traitement des données dans la documentation officielle au moment du choix (EJ-02)
- [ ] Rejouer la recette de `REGLES_REDACTION.md` à l'identique ; mesurer qualité, durée et coût par mail ; estimer le coût mensuel à environ 150 mails/jour
- [ ] Choisir, créer le credential dans n8n (jamais de clé dans Git), fixer le plafond de dépenses et l'alerte (EF-12)
- [ ] Adapter uniquement le nœud HTTP et les nœuds qui lisent la réponse ; rejouer T1 à T13

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
| 19/10/2026 | Comparaison de modèles : premier essai Qwen interrompu | Deux champs `model` temporairement passés à `qwen2.5:7b`, prompts et timeout 180000 ms inchangés. Faux devis complet injecté au Trigger ; exécution limitée à Rédaction, sans création Gmail. Tri valide : `is_client: true`, urgence `normale` ; aucune sortie Rédaction observée. Arrêt demandé après une durée affichée de 5 min 23 s, notification « Execution stopped », puis n8n hors ligne | Reprises automatiques préexistantes conservées ; aucun message d’erreur de timeout constaté, durée finale inconnue. CPU Windows observé à 43 puis 75 %, mémoire libre entre 3,77 et 5,27 Gio sur 31,57 Gio ; mesures ponctuelles système, pas de preuve de saturation Docker. Sept essais non exécutés. Modèles et données épinglées à restaurer dès rétablissement de n8n ; export JSON local inchangé. Aucun commit/push pour cette étape, aucune phase 1 engagée |
| 19/10/2026 | Revue du plan par l'orchestrateur (Claude) | Plan validé avec 4 ajustements : sécurité avant style ; règles de rédaction surtout par le code ; sortie « version Llama » définie par des critères ; bascule API préparée dès maintenant. Ajout de `REGLES_REDACTION.md`, de la phase « Bascule », export `workflow_n8n_v2.json` aligné sur la correction A4 | Logs Ollama du 19/10 : l'appel de rédaction `qwen2.5:7b` s'est terminé en erreur 500 après exactement 3 min (timeout n8n), environ 90 tokens générés ; 5 essais automatiques expliquent la durée de plus de 5 min ; CPU seul, mémoire non saturée (limite 15,4 Go) ; l'arrêt de n8n vient d'un redémarrage (SIGTERM), cause de l'indisponibilité non établie. Dépôt clonable sans identifiants : visibilité à vérifier. Rien n'a été poussé par Claude (GitHub non lié à la session) |
| 19/10/2026 | Synchronisation GitHub demandée par le porteur | Onze fichiers demandés vérifiés ; JSON valides, connexions du workflow cohérentes, original v1 inchangé. Mise en version des documents actualisés, de `REGLES_REDACTION.md` et de la correction A4 dans l’export v2 | Publication sur `main` explicitement autorisée par le porteur. Dépôt public confirmé par l’API GitHub ; visibilité inchangée. Aucun essai n8n exécuté pendant cette synchronisation |
