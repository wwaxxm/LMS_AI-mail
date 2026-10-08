# Guide pas à pas (version Codex) : de zéro à un projet vendable

Fichiers fournis : `cahier_des_charges_reponse_mail_ia.md`, `AGENTS.md`, `PLAN.md`, `.gitignore`, `workflow_n8n_v1_original.json` et ce guide.
`AGENTS.md` remplace l'ancien `CLAUDE.md` : c'est le fichier d'instructions que Codex lit automatiquement avant de travailler.

---

## Étape 0 : décider avant de coder
Remplis le tableau « Décisions prises » dans `PLAN.md` (au minimum : messagerie, installation par client ou service partagé, hébergement de n8n, modèle IA du workflow, langues, volume). Sans ces réponses, Codex fera des hypothèses.

## Étape 1 : compte et accès
Codex CLI se connecte de deux façons (source : dépôt officiel github.com/openai/codex) :
- **Avec ton compte ChatGPT** : formules Plus, Pro, Team, Edu ou Enterprise.
- **Avec une clé API OpenAI** : possible, mais demande une configuration supplémentaire.

Je n'ai pas vérifié les tarifs ni les limites d'usage actuels : consulte les pages officielles d'OpenAI avant de t'abonner.

## Étape 2 : installer Codex
Ouvre un terminal et lance la commande de ton système (source : README officiel, github.com/openai/codex) :
- **Windows (PowerShell)** : `powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"`
- **macOS / Linux** : `curl -fsSL https://chatgpt.com/codex/install.sh | sh`
- Alternatives : `npm install -g @openai/codex` (nécessite Node.js, version à vérifier dans la doc) ou `brew install --cask codex` (macOS)

Vérifie ensuite avec `codex --version`.
Codex existe aussi en application de bureau (`codex app`) et en extension pour éditeurs de code (VS Code, Cursor, Windsurf). Le README mentionne aussi une version cloud sur chatgpt.com/codex : pour ce projet, utilise la version **locale**, qui travaille dans ton dossier.
Note : certains guides anciens disent que Windows passe par WSL2. Le README actuel donne un installateur PowerShell ; en cas de problème, vérifie la documentation.

## Étape 3 : préparer l'environnement de test
- Un **compte Gmail de test** (jamais ta vraie boîte ni celle d'un client).
- Une **instance n8n de test** (la tienne, en Docker local).
- Une **clé API OpenAI** (platform.openai.com), avec un **plafond de dépenses** défini dans les réglages de facturation. Ce compte API est distinct de ton accès à Codex : vérifie la facturation.
- Entre toi-même les credentials dans n8n. Ne les donne pas à Codex :
  - **Gmail** : connexion OAuth (compte de test).
  - **OpenAI** : nouveau credential de type **Header Auth**, nom du credential `Clé OpenAI Header`, champ Name = `Authorization`, champ Value = `Bearer ` suivi de ta clé (avec l'espace après Bearer).
- L'ancien credential « Clé Gemini Header » n'est plus utilisé par la v2 : tu peux le supprimer, et révoquer la clé Gemini si tu ne t'en sers plus.

## Étape 4 : créer le dossier du projet
```
mon-projet-mail-ia/
├── AGENTS.md
├── PLAN.md
├── cahier_des_charges_reponse_mail_ia.md
├── workflow_n8n_v1_original.json     (ne jamais modifier)
├── workflow_n8n_v2.json              (copie de v1, c'est elle qu'on modifie)
└── .gitignore                        (contient notamment : .env)
```
- **Supprime l'ancien `CLAUDE.md`** s'il est dans ton dossier.
- **Ne lance pas `/init`** dans Codex : cette commande génère un `AGENTS.md` de départ dans le dossier, et ton fichier existe déjà.
- Lance `git init` dans le dossier pour pouvoir revenir en arrière.

## Étape 5 : lancer Codex
1. Dans le terminal : `cd C:\Users\Utilisateur\Documents\Projets\mon-projet-mail-ia` (adapte au nom réel de ton dossier), puis `codex`.
2. À la première utilisation, choisis **Sign in with ChatGPT** (ou configure ta clé API).
3. **Vérifie qu'il a bien chargé tes règles** : demande-lui « Cite les règles de AGENTS.md que tu as chargées ». S'il ne les cite pas, vérifie que tu l'as lancé dans le bon dossier.
4. Pour le choix du modèle, regarde la liste des commandes slash dans l'interface ou la référence CLI (developers.openai.com/codex/cli/reference). Je n'ai pas vérifié ici la commande exacte ni les modèles proposés.
5. Valide toi-même les commandes qu'il demande d'exécuter, surtout au début. Ne lui donne pas des droits plus larges que nécessaires, et vérifie les réglages d'approbation dans la documentation.

## Étape 6 : session de cadrage (aucun code)
Colle ce prompt :
```
Lis AGENTS.md, PLAN.md et cahier_des_charges_reponse_mail_ia.md.
1. Cite d'abord les règles de AGENTS.md que tu as chargées.
2. Résume-moi en 10 lignes ce que tu as compris du projet, de l'existant et du plan.
3. Liste les ambiguïtés ou informations manquantes (en t'appuyant sur la section 14).
4. Ne modifie aucun fichier et n'écris aucun code.
```
Réponds à ses questions, mets à jour les décisions dans `PLAN.md`, puis **ferme la session**.

## Règle d'or pour toutes les phases suivantes
**Une phase = une nouvelle session.** Ferme Codex, relance `codex` dans le dossier du projet, et colle le prompt de la phase. Après chaque phase :
1. Importe `workflow_n8n_v2.json` dans ton n8n de test (étape 11).
2. Teste avec de faux mails sur le Gmail de test.
3. Vérifie que PLAN.md est à jour avant de passer à la suite.

---

## Étape 7 : Phase 0, corrections rapides
```
Étape : Phase 0 (corrections rapides).
Objectif : traiter A1, A2, A3 et A4 de la section 3.3 du cahier des charges.
Périmètre : modifie uniquement workflow_n8n_v2.json. Ne touche ni aux prompts
ni à la structure générale du workflow.

Méthode :
1. Relis PLAN.md et la section 3.3.
2. Propose un plan en quelques lignes. N'applique rien avant mon accord.
3. Applique les corrections une par une et explique chaque changement simplement.
   Pour A2 (nœud Wait), explique-moi ce qu'il fait et propose de le supprimer
   ou de le réduire. Pour A3, propose-moi 2 ou 3 emplacements possibles pour le
   résumé interne, hors du corps du brouillon.
Critère de fin : les 4 anomalies sont traitées et le JSON reste importable dans n8n.
À la fin : résumé « fait / pas fait / à décider », puis mise à jour de PLAN.md.
```

## Étape 8 : Phase 1, sécurité du contenu
```
Étape : Phase 1 (sécurité du contenu).
Objectif : traiter A5, A6 et A7 (exigences EF-02, EF-04, EF-05, EF-06, ES-03, ES-05).
Périmètre : prompts de tri et de rédaction, aiguillage des mails sensibles, nouveau
fichier base_de_connaissances.md.

Méthode :
1. Relis PLAN.md et les sections 4, 5 et 9 du cahier des charges.
2. Propose un plan. N'écris rien avant mon accord.
3. Mets en place : les clés category et sensitive dans le tri ; les délimiteurs
   <email>...</email> et la consigne anti-injection dans les deux prompts ;
   l'aiguillage « sensible = aucun brouillon + libellé + alerte » ;
   la règle « ne jamais inventer prix, délais, engagements ».
4. Prépare de faux mails pour les tests T1 à T6 (dont le mail piégé T5 et la
   fausse autorité T6) avec le résultat attendu pour chacun.
Critère de fin : le plan de test T1 à T6 est prêt et chaque résultat attendu est clair.
À la fin : résumé « fait / pas fait / à décider », puis mise à jour de PLAN.md.
```
Remplis ensuite `base_de_connaissances.md` avec les vraies informations de l'entreprise (FAQ, horaires, services, ton, signature).

## Étape 9 : Phase 2, fiabilité
```
Étape : Phase 2 (fiabilité).
Objectif : traiter A8, A9, A10 (exigences EF-09 à EF-12).
Périmètre : contrôle de la sortie, gestion des erreurs, journal d'audit,
interrupteur d'urgence, plafond de coûts, Reply-To.

Méthode :
1. Relis PLAN.md et les sections 4, 7 et 8.
2. Propose un plan. N'écris rien avant mon accord.
3. Mets en place : contrôle « réponse non vide + JSON valide » ; workflow d'erreur
   avec alerte au porteur ; journal (mail reçu, catégorie, décision, brouillon créé,
   horodatage, sans données personnelles inutiles) ; interrupteur d'urgence ;
   alerte de plafond de coûts ; gestion de Reply-To.
Critère de fin : tests T7 à T13 prêts et chaque résultat attendu est clair.
À la fin : résumé « fait / pas fait / à décider », puis mise à jour de PLAN.md.
```

## Étape 10 : Phase 3, validation complète
```
Étape : Phase 3 (validation).
Objectif : valider l'ensemble avec le jeu de tests T1 à T13 et la checklist de sécurité ES-01 à ES-11.

Méthode :
1. Propose une méthode de test adaptée à n8n (par exemple des données de test
   épinglées ou des faux mails envoyés vers le Gmail de test).
2. Pour chaque test, présente un tableau « résultat attendu / résultat obtenu ».
3. Passe en revue ES-01 à ES-11 et dis-moi, pour chaque point, ce qui est conforme et ce qui ne l'est pas.
4. Rédige un rapport de tests dans un fichier rapport_tests.md.
Ne corrige rien sans m'en parler : liste d'abord les écarts.
```
Tu exécutes et vérifies toi-même les tests dans n8n. Ne te contente pas du compte rendu de Codex.

## Étape 11 : importer le workflow dans n8n (à faire toi-même)
1. Ouvre ton n8n de test dans le navigateur.
2. Crée un nouveau workflow, ouvre le menu du workflow et choisis l'import depuis un fichier (« Import from File »), puis sélectionne `workflow_n8n_v2.json`.
3. Ouvre les deux nœuds « Appel OpenAI - Tri » et « Appel OpenAI - Rédaction » et sélectionne le credential `Clé OpenAI Header` (le JSON ne contient que son nom, pas son identifiant, donc n8n peut te le redemander). Vérifie aussi que le nœud Gmail utilise ton credential de **test**.
   - Au premier test, si l'appel renvoie « modèle introuvable » ou « paramètre non supporté », note le message exact : c'est le point A11, à corriger avec Codex d'après la documentation officielle OpenAI.
4. Désactive le workflow tant que tu ne veux pas qu'il tourne sur la boîte de test, et lance un test manuel avec un faux mail.

Je n'ai pas vérifié si Codex peut piloter l'interface de n8n à ta place. Fais cette étape manuellement : c'est aussi plus sûr pour tes identifiants.

## Étape 12 : Phase 4, pilote chez un client
- Choisis un client de confiance et explique-lui le fonctionnement (brouillons relus par un humain).
- Mode **brouillon uniquement** pendant 2 à 4 semaines.
- Mesure : part de brouillons utilisables sans modification, erreurs, escalades, retours du client.
- Corrige avant de proposer à d'autres clients.

## Étape 13 : Phase 5, packaging commercial
- **Contrat de service** et **clause de responsabilité** (l'IA peut se tromper, le client valide les envois).
- **DPA** (contrat de sous-traitance RGPD) et liste des sous-traitants avec leurs conditions.
- Documentation et formation du client.
- Tarification (installation + abonnement) et gestion des coûts d'API.
- **Fais valider les documents juridiques par un professionnel.** Codex peut préparer des brouillons, pas un avis juridique.

## Étape 14 : Phase 6, options
À proposer selon la demande : validation Slack ou e-mail, résumé quotidien, tableau de bord, envoi automatique limité aux catégories validées par le client.

---

## Checklist finale avant de vendre
- [ ] Brouillons uniquement, aucun envoi automatique non validé
- [ ] Mails sensibles escaladés, jamais de réponse générée
- [ ] Tests T1 à T13 passés et documentés (dont mail piégé et fausse autorité)
- [ ] Journal, alertes d'erreur, interrupteur d'urgence et plafond de coûts en place
- [ ] Une instance ou des credentials séparés par client, droits Gmail minimaux
- [ ] Aucune clé dans les fichiers ni dans Git
- [ ] DPA, contrat, clause de responsabilité validés par un professionnel
- [ ] Pilote réussi chez au moins un client

## Si Codex se perd
1. Arrête-le et rappelle le périmètre : « Reviens à l'étape [X], tu es sorti du périmètre. »
2. Si la session est confuse : ferme-la, relance `codex` dans le dossier, et demande-lui de relire PLAN.md.
3. Garde `AGENTS.md` court et cohérent avec le cahier des charges : un fichier court et précis est plus utile qu'un long fichier de règles vagues.
4. Vérifie que tu l'as bien lancé depuis `mon-projet-mail-ia` et non depuis `Projets` ou `Documents`.

## Ce qui reste TOUJOURS à ta charge
- Les décisions du projet (section 14).
- Les identifiants et connexions sensibles.
- La relecture de ce que Codex propose et l'exécution des tests.
- La validation juridique.
