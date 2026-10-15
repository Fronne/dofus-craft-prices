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

1. **Télécharge l'outil** depuis la
   [dernière version](https://github.com/berthiercyril/dofus-craft-prices/releases/latest).
   Deux choix, c'est le même programme :
   - `dofus-craft-prices.exe` seul, le plus simple :
     [lien direct](https://github.com/berthiercyril/dofus-craft-prices/releases/latest/download/dofus-craft-prices.exe).
     Si ton navigateur dit que le fichier est rarement téléchargé, choisis
     « Conserver ».
   - le zip, si ton navigateur bloque l'exe. Dézippe-le.
2. **Range l'exe dans un dossier à lui**, par exemple
   `Documents\dofus-craft-prices`. L'outil y enregistre sa configuration et les
   prix qui attendent d'être envoyés.
3. **Lance `dofus-craft-prices.exe`**. Windows peut afficher « Windows a protégé
   votre ordinateur » (SmartScreen), parce que l'exe n'est pas signé. Clique sur
   « Informations complémentaires », puis sur « Exécuter quand même ».
4. **Npcap**, si tu ne l'as pas déjà. C'est ce qui permet de lire le trafic
   réseau. L'outil l'indique au lancement : clique sur « Installer Npcap »,
   accepte la demande d'autorisation de Windows, garde les options par défaut et
   clique sur « Install ». Il ne faut le faire qu'une fois. Tu peux aussi
   l'installer toi-même : https://npcap.com/#download.

Pour vérifier que le fichier n'a pas été modifié, compare son empreinte avec
celle publiée à côté de lui dans la Release (`.sha256`) :

```
certutil -hashfile dofus-craft-prices.exe SHA256
```

## Premier lancement

La fenêtre demande trois réglages :

1. **Ta clé personnelle.** L'admin de Dofus-Craft te la donne. Colle-la avec
   Ctrl+V.
2. **Ton serveur**, dans la liste.
3. **La carte réseau** : laisse « Automatique ».

Clique sur « Enregistrer et démarrer ». La clé est vérifiée tout de suite : si
elle est refusée, la fenêtre le dit en rouge. Les réglages sont enregistrés dans
`config.toml`, à côté de l'exe, et restent modifiables avec le bouton
« Réglages ».

**Ta clé est personnelle, ne la partage pas.** Si elle fuite, demande-en une
nouvelle : l'ancienne sera révoquée.

## Au quotidien

Lance l'outil, puis le jeu, dans n'importe quel ordre. Garde la fenêtre ouverte
et consulte des ressources à l'HDV. Le voyant en haut indique l'état : vert
« Envoi actif » quand tout va bien. Toutes les 30 secondes environ, la liste
« Activité » indique ce qui est parti, par exemple « 12 prix envoyés », et le
compteur « Prix envoyés » augmente.

Les prix qui n'ont pas pu partir (pas d'internet, serveur indisponible) sont
gardés dans `upload-queue.json` et renvoyés plus tard, même après un
redémarrage.

## Ce que le voyant peut dire

| voyant | ce que ça veut dire |
|---|---|
| vert, « Envoi actif » | Tout va bien. |
| orange, « En pause : Dofus a été mis à jour… » | Ankama a changé le format des messages avec la mise à jour. Rien n'est envoyé tant que Dofus-Craft n'a pas publié le nouveau format. Ça reprend tout seul, sans rien télécharger. |
| orange, « En pause : version de Dofus introuvable… » | Dofus n'est pas installé à l'endroit habituel. Ajoute dans `config.toml` une ligne `install = 'D:\Jeux\Dofus-dofus3'`, avec ton propre chemin, puis relance l'outil. |
| orange, « Plafond du jour atteint » | Tu as envoyé beaucoup de prix aujourd'hui. Les suivants partent demain. |
| rouge, « Outil trop ancien » | Clique sur « Télécharger la nouvelle version ». |
| rouge, « Clé refusée » | Ta clé est fausse ou a été révoquée. Clique sur « Changer de clé », ou demande-en une nouvelle. |

Si l'outil ne voit aucun prix alors que tu consultes l'HDV, c'est sans doute la
mauvaise carte réseau. Dans « Réglages », choisis celle dont l'adresse
correspond à celle que donne la commande `ipconfig`.

