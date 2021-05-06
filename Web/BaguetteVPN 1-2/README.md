# BaguetteVPN 1/2
Je ne comptais pas faire une write-up sur ce challenge mais je pense que c'est mieux histoire de compléter le tout avec BaguetteVPN 2/2.<br/>
## Résolution
On a à notre disposition un site: http://challenges2.france-cybersecurity-challenge.fr:5002<br/>
En regardant le code source on voit tout en bas une citation.<br/>
```
<!-- 
  Changelog :
    - Site web v0
    - /api/image : CDN interne d'images
    - /api/debug
  TODO :
    - VPN
-->
```
En se rendant dans la path /api/debug on voit le debugging du Flask.<br/>
Au vu du résultat, je me dis qu'il y a forcément un call d'un fichier interne.<br/>
Je match ```__file__``` et je vois que le fichier ```./baguettevpn_server_app_v0.py``` a été load.<br/>
Je me rends dans http://challenges2.france-cybersecurity-challenge.fr:5002/baguettevpn_server_app_v0.py<br/>
Cool, on voit le flag.<br/>
```
# /usr/bin/env python3
# -*- coding:utf-8 -*-
# -*- requirements:requirements.txt -*-

# Congrats! Here is the flag for Baguette VPN 1/2
#   FCSC{e5e3234f8dae908461c6ee777ee329a2c5ab3b1a8b277ff2ae288743bbc6d880}
```
