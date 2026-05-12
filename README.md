# Incident Console - simulateur interactif

> Application HTML autonome qui simule un incident de production chez FeedMyHealth. Pensée pour remplacer l'exercice papier "incident surprise" du J2 par une expérience interactive scénarisée.

## Pour qui, pour quoi

Pour les 4 groupes d'étudiants en parallèle, à 17h30 sur le J2 (45 min). Chaque groupe ouvre l'URL et passe par la simulation. Pas besoin que le formateur soit présent dans chaque breakout : la console raconte elle-même l'incident.

Pour les étudiants : faire ressentir dans la simulation ce que c'est qu'un incident en cours. Dashboard qui se dégrade, fil Slack qui arrive, choix à faire vite, conséquences immédiates des choix.

## Comment ça marche

Une seule page HTML (`index.html`), pas de backend, pas de dépendance externe (sauf Google Fonts). Hébergement GitHub Pages prêt à l'emploi.

Le flow :
1. Écran d'intro avec le cadre.
2. Démarrage de l'incident à 16h22 (T+5 après la première erreur).
3. Le dashboard affiche les métriques en temps "scénarisé" (5xx, latence p95, requêtes/sec, tickets support).
4. Les logs streamment en bas à gauche.
5. Le fil Slack reçoit des messages de Julien, Karim, Mehdi, Catherine, Hadrien selon le chemin emprunté.
6. Le groupe choisit une action parmi celles proposées (rollback, désactiver l'endpoint, investiguer les logs, communiquer, etc.).
7. Chaque choix mène à une nouvelle scène avec ses propres logs, messages, métriques mises à jour.
8. Après 5 à 8 décisions, un rapport final affiche : timeline des décisions, scores Technique/Communication/Leadership, et la révélation des 4 causes racines (toutes présentes dans le dossier d'onboarding du J1).

## Branchements

13 scènes au total, plusieurs chemins possibles :

- **Chemin "investigateur"** : logs → identification PR → hotfix ou rollback manuel → résolution propre.
- **Chemin "trop rapide"** : rollback aveugle → échec (le rollback est cassé) → reprise.
- **Chemin "communicateur"** : message client avant d'avoir investigué → Catherine recadre → reprise.
- **Chemin "passif"** : attente de Karim → effet cascade → SLA cassé.

La scène finale propose 3 lectures de l'incident : erreur humaine de Théo, cause systémique, manque de chance. Chacune mène à un outcome différent dans le rapport.

## Hébergement

Le repo est déjà en GitHub Pages. L'URL sera (selon le compte/repo) :

```
https://pandormedia.github.io/HETIC-Qualite-cours/incident-simulator/
```

Pour les étudiants : envoyez simplement le lien dans le chat de la classe au moment voulu.

## Test local

```bash
cd incident-simulator
python3 -m http.server 8001
# puis ouvrir http://localhost:8001/
```

Ou simplement double-cliquez sur `index.html` (mais Google Fonts a besoin de la connexion).

## Pour le formateur

### Avant la séance

- Ouvrez l'URL et jouez une partie complète pour vérifier le flow.
- Notez les 4 causes racines révélées en fin de simulation : ce sont les éléments que vous reprendrez en débrief.

### Pendant la séance

- Annoncez le format : "vous allez ouvrir ce lien, vous avez 30 minutes pour traverser l'incident. La console vous guide. À la fin, le rapport vous indique ce que vous avez réussi et raté."
- Indiquez aux groupes de désigner un capitaine (clique), un scribe (note les hypothèses), un communicant (qui rédige les messages quand demandés).
- Tournez dans les breakouts pendant les 30 minutes. Pas pour intervenir, juste pour observer la dynamique.

### Pour le débrief (15 min)

Format suggéré :
1. Chaque groupe partage son score final.
2. "Qui a tenté un rollback aveugle ?" Levée de main. Discutez ce que ça a coûté.
3. "Qui a désactivé l'endpoint avant de fixer ?" Levée de main. Discutez.
4. "Qui a blâmé Théo dans la scène finale ?" Levée de main. Lisez la réponse de Mehdi à voix haute.
5. La punchline : les 4 causes racines étaient dans le dossier d'onboarding distribué lundi matin. Section coverage, section politique de release, section post-mortem SSO d'avril. Les groupes qui les ont vues hier peuvent maintenant les adresser. Les autres viennent de les vivre.

## Limites assumées

C'est une simulation, pas un environnement réel. Les étudiants ne diagnostiquent pas avec de vrais outils. Ils prennent des décisions sur la base de ce que la console montre.

L'enseignement n'est pas "comment on debugue un vrai incident". C'est :
- Triage sous pression (rollback vs fix vs désactivation).
- Communication sous incertitude (timing du message client).
- Lecture systémique vs personnelle (la scène finale).

Ces 3 compétences sont vraiment testées par la simulation, sans avoir besoin de vrais logs.

## Personnalisation

Pour ajouter un chemin alternatif, éditez l'objet `scenes` dans `index.html`. Format :

```js
nom_scene: {
  time: 30,                          // minute (16:XX)
  metrics: { errors: 50, ... },      // métriques visibles
  logs: [ { type: 'error', text: '...' } ],
  messages: [ { who: 'karim', ch: '#tech', text: '...' } ],
  scenario: { title: '...', text: '...', onEnter: () => { ... } },
  actions: [
    { label: '...', icon: '🔧', goto: 'autre_scene', flag: 'monFlag' }
  ]
}
```
