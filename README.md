# dofus-craft-prices

Un petit outil Windows qui envoie à Dofus-Craft les
prix des **ressources** que tu consultes à l'HDV. Tu joues normalement, l'outil
tourne dans une fenêtre à côté, et chaque prix vu alimente la base de prix
communautaire.

## Avant de commencer

**Lis ceci.** L'outil lit le trafic réseau entre ton PC et le serveur Dofus.
Il n'envoie rien au jeu, ne touche pas au client et ne joue pas à ta place.
Mais les CGU d'Ankama interdisent les logiciels tiers qui interagissent avec le
jeu, et lire son trafic peut être vu comme tel. Tu l'utilises à tes risques.

## Ce qu'il fait, ce qu'il ne fait pas

- Il lit **seulement** les réponses de l'HDV qui donnent des prix.
- Il envoie **seulement** les ressources, avec les prix par lot de 1, 10, 100
  et 1000. Dofus-Craft en déduit lui-même le prix unitaire.
- Il n'envoie **jamais** les équipements, ton inventaire, tes messages, ton
  pseudo ou ton compte.
- Un même objet est renvoyé au plus une fois par heure, et seulement si son prix
  a bougé.
- Il ne se met pas à jour tout seul. S'il est trop ancien, il te le dit et te
  donne le lien de la nouvelle version.

## Installation

1. **Télécharge** le zip de la [dernière version](https://github.com/berthiercyril/dofus-craft-prices/releases/latest).
2. **Dézippe** le dossier où tu veux, par exemple dans `Documents`.
3. **Lance `dofus-craft-prices.exe`**. Windows peut afficher « Windows a protégé
   votre ordinateur » (SmartScreen), parce que l'exe n'est pas signé. Clique sur
   « Informations complémentaires », puis sur « Exécuter quand même ».
4. **Npcap**, si tu ne l'as pas déjà. C'est ce qui permet de lire le trafic
   réseau. L'outil propose de le télécharger depuis npcap.com et de lancer son
   installation : réponds `O`, accepte la demande d'autorisation de Windows,
   garde les options par défaut et clique sur « Install ». Il ne faut le faire
   qu'une fois. Tu peux aussi l'installer toi-même :
   https://npcap.com/#download.

Pour vérifier que le fichier n'a pas été modifié, compare son empreinte avec
celle publiée à côté du zip (`.sha256`) :

```
certutil -hashfile dofus-craft-prices-0.2.0.zip SHA256
```

## Premier lancement

L'outil pose trois questions :

1. **Ta clé personnelle.** L'admin de Dofus-Craft te la donne. Colle-la avec un
   clic droit dans la fenêtre. Elle est vérifiée tout de suite.
2. **Ton serveur**, par son numéro dans la liste ou par son nom.
3. **La carte réseau** : rien à faire, elle est détectée automatiquement.

Tes réponses sont enregistrées dans `config.toml`, à côté de l'exe. Pour tout
recommencer, supprime ce fichier.

**Ta clé est personnelle, ne la partage pas.** Si elle fuite, demande-en une
nouvelle : l'ancienne sera révoquée.

## Au quotidien

Lance l'outil, puis le jeu, dans n'importe quel ordre. Garde la fenêtre ouverte
et consulte des ressources à l'HDV. Toutes les 30 secondes environ, une ligne
indique ce qui a été envoyé :

```
[envoi] envoi actif (mapping 3)
[envoi] 12 prix envoyés
```

Les prix qui n'ont pas pu partir (pas d'internet, serveur indisponible) sont
gardés dans `upload-queue.json` et renvoyés plus tard, même après un
redémarrage.

## Messages que tu peux voir

| message | ce que ça veut dire |
|---|---|
| `Dofus a été mis à jour (...), l'outil attend sa mise à jour` | Ankama a changé le format des messages avec la mise à jour. Rien n'est envoyé tant que Dofus-Craft n'a pas publié le nouveau format. Ça reprend tout seul, sans rien télécharger. |
| `cet outil est trop ancien` | Télécharge la nouvelle version avec le lien affiché. |
| `clé refusée` | Ta clé est fausse ou a été révoquée. Supprime `config.toml` et recolle-la, ou demande-en une nouvelle. |
| `plafond du jour atteint` | Tu as envoyé beaucoup de prix aujourd'hui. Les suivants partent demain. |
| `Npcap n'est pas installé` | Relance l'outil et réponds `O`, ou installe Npcap toi-même depuis https://npcap.com/#download. |
| `version introuvable, l'envoi reste en pause` | Dofus n'est pas installé à l'endroit habituel. Ajoute dans `config.toml` une ligne `install = 'D:\Jeux\Dofus-dofus3'`, avec ton propre chemin. |

Si l'outil ne voit aucun prix alors que tu consultes l'HDV, c'est sans doute la
mauvaise carte réseau. Ajoute dans `config.toml` la ligne
`adapter = "192.168.1.10"`, en remplaçant le nombre par l'adresse IPv4 que
donne la commande `ipconfig`.

