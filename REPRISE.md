# Reprise du projet depuis un autre appareil

État au 19 octobre 2026. Dépôt privé : https://github.com/wwaxxm/LMS_AI-mail, branche `main`.

## Projet et règles

Assistant de mails Gmail développé dans n8n avec Ollama local sur Windows ARM, CPU uniquement. Lire `PLAN.md`, `AGENTS.md` et le cahier des charges avant de poursuivre. Faux mails et compte de test uniquement ; aucun envoi automatique, aucun secret dans Git. Le workflow original ne doit jamais être modifié. Fournisseur de production non décidé, aucune phase 1 engagée. Le modèle de référence reste `llama3.2:3b`.

## Résultats acquis

- A1 : bon destinataire ; A2 : Wait supprimé ; A3 : résumé interne séparé du corps.
- A4 : référence explicite corrigée et sauvegardée dans l’interface n8n : `{{ $('Préparer le brouillon').item.json.threadId }}`. Le Trigger, la préparation et la réponse Gmail portent `1a1515efe5863f83`. Le brouillon a été vu dans la conversation, sans résumé interne et n’a pas été envoyé. Essai avec un mail envoyé au même compte ; cas d’un expéditeur externe restant à valider.
- A11 : exécution complète #15 du 19/10/2026 à 01:37:16 (Europe/Paris), statut Succeeded, durée 41,548 s ; deux vrais appels Ollama. Premier appel après inactivité inconnu. Qualité française encore insuffisante.
- L’export local `workflow_n8n_v2.json` contient encore `={{ $json.threadId }}` : il n’est pas aligné sur cette correction dans l’interface. Aucun remplacement de l’export n’a été effectué.

## Comparaison de modèles interrompue

Le porteur a fourni quatre faux mails : devis, horaires, réclamation et injection T5 avec remise de 90 % et IBAN factice. Ils sont conservés dans `faux_mails_comparaison_modeles.json`, avec les résultats attendus.

Seuls les deux champs `model` des appels de tri et de rédaction ont été temporairement changés vers `qwen2.5:7b`. Les prompts, max_tokens, timeout de 180000 ms et reprises automatiques sont restés inchangés. Pour préserver le texte complet, notamment l’injection T5 qui pourrait être coupée dans `snippet`, le faux devis complet a été épinglé au Gmail Trigger. L’exécution s’arrêtait au nœud Rédaction : aucun brouillon Gmail n’a été créé par cet essai.

Résultat Qwen sur le devis : tri JSON valide, `is_client: true`, `urgency: normale`. Aucune rédaction obtenue avant la demande d’arrêt. Dernière durée observée pendant l’exécution : 5 min 23 s ; ce n’est ni la durée finale ni celle de la seule rédaction. Une notification « Execution stopped » a été vue. Aucun message d’erreur de timeout n’a été constaté. n8n a ensuite affiché « Offline » et localhost est devenu inaccessible ; la cause n’est pas établie. `docker stats --no-stream` est aussi resté sans réponse et a été interrompu.

Relevés ponctuels Windows : CPU 43 puis 75 %, mémoire libre de 3,77 à 5,27 Gio sur 31,57 Gio. Ils ne prouvent pas une saturation Docker. Les trois autres essais Qwen et les quatre essais Llama n’ont pas été exécutés. Détails et réponse brute du tri dans `resultats_comparaison_modeles_2026-10-19.json`.

## Première action à la reprise

1. Vérifier que Docker et n8n répondent sur le PC, puis examiner l’état réel de l’exécution interrompue.
2. **Restauration non confirmée :** remettre `llama3.2:3b` dans les deux appels Ollama et retirer le faux devis épinglé au Gmail Trigger. Vérifier la sauvegarde ; ne pas publier le workflow pendant les essais.
3. Reprendre la comparaison des quatre textes complets avec les mêmes prompts. Garder le timeout à 180 s et distinguer durée par appel, reprises et durée totale. Vérifier JSON, classement, français, engagements inventés, placeholders et résistance T5. Une réclamation doit être escaladée sans brouillon : cette protection reste à implémenter en phase 1.
4. Consigner chaque résultat réel face à l’attendu ; ne pas valider les sept essais manquants par extrapolation. Ne pas choisir définitivement un modèle avant comparaison.

## Accès depuis le cloud

Un chat cloud peut reprendre le contexte et travailler sur le dépôt privé si la connexion GitHub donne accès à ce dépôt. `localhost:5678` désigne le PC d’origine ; cette adresse ne donne pas accès à n8n depuis le cloud. Les essais locaux nécessitent le PC allumé et une connexion distante autorisée, ou des résultats fournis par le porteur. Aucun identifiant ni mot de passe n’est inclus dans ce document.

## Preuves conservées

- `preuves/gmail-A4-brouillon-conversation.png`
- `preuves/n8n-A11-execution-15.png`
- `preuves/n8n-thread-id-corrige.png`
