# BaguetteVPN 1/2
Je ne comptais pas faire une write-up sur ce challenge mais je pense que c'est mieux histoire de compléter le tout avec BaguetteVPN 2/2.<br/>
## Résolution
On a à notre disposition un site: http://challenges2.france-cybersecurity-challenge.fr:5002<br/>
En regardant le code source on voit tout en bas un commentaire:<br/>
<!-- 
  Changelog :
    - Site web v0
    - /api/image : CDN interne d'images
    - /api/debug
  TODO :
    - VPN
-->
En se rendant dans la path /api/debug on voit le debugging du Flask.<br/>
Au vu du résultat, je me dis qu'il y a forcément un call sur un fichier du serveur.<br/>
Je match ```__file__``` et je vois que le fichier ./baguettevpn_server_app_v0.py a été load.<br/>
