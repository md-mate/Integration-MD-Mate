# Documentation d'Intégration WebSocket
Une description de la façon dont nous aimerions nous intégrer avec vous.
Idéalement, les médecins recevraient une notification en temps réel via WebSocket avec le rapport du *PATIENT ACTUEL* directement dans les champs appropriés.

## Point de Terminaison WebSocket
wss://upload-with-html.onrender.com/reports/ws  ---> adresse temporaire que les médecins pourront modifier
CELLE-CI CHANGERA FRÉQUEMMENT

## Authentification
Après l'établissement de la connexion WebSocket, vous devez envoyer un message d'authentification dans les 10 secondes.
Le message d'authentification doit être un objet JSON contenant la clé API du médecin :
{
"doctor_key": "api-key"
}

## Cycle de Vie de la Connexion
1. Connexion au point de terminaison WebSocket
2. Envoi du message d'authentification dans les 10 secondes
3. Maintien de la connexion avec messages ping/pong (optionnel)
4. Gestion des notifications entrantes
5. Gestion des scénarios de déconnexion

### Heartbeat Ping/Pong
Pour maintenir la connexion active, vous pouvez envoyer un message ping :
- Envoi : "ping"
- Réception : "pong"
Idéalement toutes les 30 secondes

## Notifications
### Notification de Rapport Prêt
Lorsqu'un rapport est prêt, vous recevrez un message JSON avec la structure suivante :



{
"type": "report_ready", (STRING)

"url": "url-signée-pour-télécharger-docx", (STRING ---> URL signée pour télécharger le fichier docx)

"html_content": "version-html-du-rapport", (STRING ---> version HTML du rapport)

"rtf_report": "version-rtf-du-rapport", (STRING ---> version format texte enrichi du rapport)

"plaintext_report": "version-texte-simple", (STRING ---> version texte simple du rapport)

"json_report": ----> OBJET JSON IMBRIQUÉ avec accès à toutes les données du rapport

{
"history": "La patiente est venu parce qu'elle a mal",
"findings": "Pouls de 100/min, tension arterielle 140/80mmHg",
"diagnosis": "Cephalées chroniques non spécifiées",
"procedure": "Examen physique et neurologique",
"recommendation": "Nous recommandons du repos à la patiente"
}

}


### Notifications d'Erreur
En cas d'erreur, vous recevrez un message JSON avec la structure suivante :
{
"type": "error",
"message": "description-erreur",
"code": 4000
}

## Codes de Connexion
1000 : Fermeture normale
1008 : Violation de politique (généralement échec d'authentification)
4000 : Erreur générique

## Meilleures Pratiques
- Authentification : Stockez et gérez la clé API de manière sécurisée
- Reconnexion : Implémentez une stratégie de backoff exponentiel pour les tentatives de reconnexion
- Gestion des Erreurs : Gérez toujours les messages d'erreur de manière appropriée
- État de la Connexion : Surveillez l'état de la connexion et implémentez un retour UI approprié
- Analyse des Messages : Validez tous les messages entrants avant le traitement

## Limites de Taux et Délais
- Délai d'authentification : 10 secondes
- Limite de connexion : Une connexion active par ID utilisateur
- Intervalle de ping recommandé : 30 secondes
