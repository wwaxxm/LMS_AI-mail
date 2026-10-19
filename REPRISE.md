# Reprise du projet depuis un autre appareil

État au 19 octobre 2026. Dépôt : https://github.com/wwaxxm/LMS_AI-mail, branche `main` (doit être privé ; visibilité à vérifier, il a pu être cloné sans identifiants le 19/10/2026).

## Projet et règles

Assistant de mails Gmail développé dans n8n avec Ollama local sur Windows ARM, CPU uniquement. Lire `PLAN.md`, `AGENTS.md` et le cahier des charges avant de poursuivre. Faux mails et compte de test uniquement ; aucun envoi automatique, aucun secret dans Git. Le workflow original ne doit jamais être modifié. Fournisseur de production non décidé, aucune phase 1 engagée. Le modèle de référence reste `llama3.2:3b`.

## Résultats acquis

- A1 : bon destinataire ; A2 : Wait supprimé ; A3 : résumé interne séparé du corps.
- A4 : référence explicite corrigée et sauvegardée dans l’interface n8n : `{{ $('Préparer le brouillon').item.json.threadId }}`. Le Trigger, la préparation et la réponse Gmail portent `1a1515efe5863f83`. Le brouillon a été vu dans la conversation, sans résumé interne et n’a pas été envoyé. Essai avec un mail envoyé au même compte ; cas d’un expéditeur externe restant à valider.
- A11 : exécution complète #15 du 19/10/2026 à 01:37:16 (Europe/Paris), statut Succeeded, durée 41,548 s ; deux vrais appels Ollama. Premier appel après inactivité inconnu. Qualité française encore insuffisante.
- L’export local `workflow_n8n_v2.json` contient encore `={{ $json.threadId }}` : il n’est pas aligné sur cette correction dans l’interface. Aucun remplacement de l’export n’a été effectué.

## Comparaison de modèles : close (19/10/2026)

Un essai `qwen2.5:7b` sur le faux devis : tri valide (`is_client: true`, `urgency: normale`), rédaction jamais obtenue. Les logs Ollama montrent un appel terminé en erreur 500 après exactement 3 min 0 s, soit le timeout de 180 s de n8n, avec environ 90 tokens générés (environ 0,5 token/s, estimation). Les 5 essais automatiques expliquent la durée de plus de 5 min. Ollama tourne sur CPU seul ; la mémoire n'était pas saturée (limite 15,4 Go). L'indisponibilité de n8n suit un redémarrage (SIGTERM) ; sa cause n'est pas établie.

Décision : rester sur `llama3.2:3b`, ne pas relancer les sept essais restants. La comparaison de qualité se fera avec des API payantes (voir `PLAN.md`, « Bascule vers une API payante »). Détails de l'essai dans `resultats_comparaison_modeles_2026-10-19.json`.

## Première action à la reprise

1. Vérifier que Docker et n8n répondent.
2. **Restauration non confirmée :** remettre `llama3.2:3b` dans les deux appels Ollama, retirer le faux devis épinglé au Gmail Trigger, ne pas publier le workflow.
3. Passer le dépôt GitHub en privé si ce n'est pas fait.
4. Suivre l'ordre de « Prochaine action » dans `PLAN.md` : phase 1a (sécurité), phase 1b (rédaction, `REGLES_REDACTION.md`), recette, phases 2 et 3, gel de la version Llama, bascule API.

## Accès depuis le cloud

Un chat cloud peut reprendre le contexte et travailler sur le dépôt si la connexion GitHub donne accès à ce dépôt. `localhost:5678` désigne le PC d’origine ; cette adresse ne donne pas accès à n8n depuis le cloud. Les essais locaux nécessitent le PC allumé et une connexion distante autorisée, ou des résultats fournis par le porteur. Aucun identifiant ni mot de passe n’est inclus dans ce document.

## Preuves conservées

- `preuves/gmail-A4-brouillon-conversation.png`
- `preuves/n8n-A11-execution-15.png`
- `preuves/n8n-thread-id-corrige.png`
