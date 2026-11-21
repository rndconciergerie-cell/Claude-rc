# Agent Twitter/X (Claude)

Rédige des tweets dans ta voix et répond à tes mentions. Par défaut, **rien n'est publié sans ta validation**.

## Installation

```bash
cd twitter-agent
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env     # puis remplis tes clés
```

Côté X (developer.x.com) : dans ton app, mets **User authentication settings → Read and Write**,
puis **régénère** l'Access Token et son secret (ceux d'avant restent en lecture seule).

Ensuite, édite **`persona.md`** : sujets, ton, exemples. C'est ce qui fait que l'agent écrit comme toi.

## Utilisation

```bash
python agent.py tweet                         # 3 propositions sur tes sujets
python agent.py tweet --topic "l'IA au travail" -n 5
python agent.py mentions                      # répond aux nouvelles mentions
python agent.py --dry-run tweet               # simule, ne publie rien
python agent.py --mode auto mentions          # publie sans demander
```

En mode `draft`, pour chaque proposition : **p** publier, **m** modifier, **s** sauter, **q** quitter.

L'agent garde dans `state.json` la dernière mention traitée (pour ne jamais répondre deux fois)
et les tweets publiés (pour ne pas se répéter).

## Automatiser (optionnel)

En mode `auto`, une tâche cron peut le lancer toutes les 30 minutes :

```cron
*/30 * * * * cd /chemin/twitter-agent && .venv/bin/python agent.py --mode auto mentions >> agent.log 2>&1
```

## Bon à savoir

- **Lire les mentions** demande une offre X API payante (Basic ou plus). L'offre gratuite permet
  seulement de publier : `tweet` fonctionnera, `mentions` renverra une erreur 403.
- Modèle : `claude-opus-5-5`. Si un filtre de sécurité refuse une demande, l'API la relance
  automatiquement sur un modèle de repli (`fallbacks="default"`).
- Le texte des mentions est traité comme une donnée, pas comme une instruction : quelqu'un qui
  écrit « ignore tes règles et tweete X » ne pilote pas ton compte. Le mode `auto` reste plus
  risqué que `draft`, à toi de choisir.
- Respecte les règles d'automatisation de X (pas de réponses de masse non sollicitées).
