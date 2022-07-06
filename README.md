# NASA Space Quiz
<img width="1390" alt="Schermafbeelding 2022-07-06 om 19 58 42" src="https://user-images.githubusercontent.com/70690100/177613755-d9e21d4b-d51a-4720-9aee-fc8cdcbbf211.png">

## De opdracht
Voor Real Time Web heb ik een real-time webapplicatie moeten maken waarbij er een open connectie is tussen de client en server side door middel van 
websockets. De webapplicatie moet multi user supported zijn en moet data gebruiken uit een externe data bron. 

## Mijn concept
Voor Real Time Web heb ik de NASA Space quiz ontwikkeld. De NASA Space quiz is een quiz waarbij meerdere gebruikers de juiste namen van vier verschillende
planeten moeten raden. De foto's die bij de planeten worden gerenderd zijn afkomstig van de NASA Photo and Video library API. Met deze API kon ik de 
meest prachtige foto's gebruiken voor de quiz. Bij elk goed antwoord verschijnt er een melding en worden alle gebruikers doorverwezen naar de volgende
pagina.

## De API
Voor mijn project heb ik de NASA Photo and Video library API gebruikt. Ik had aan het begin best wel veel problemen met de API en vind dan ook dat de 
documentatie van NASA erg vaag en vaak onjuist was. De base url was al gauw duidelijk alleen waren de te gebruiken endpoints zoals q= heel erg 
onduidelijk of werkte niet juist.

````
GET https://images-api.nasa.gov
````

Volgens NASA waren er verschillende endpoints waarbij je gemakkelijk specifiek naar foto's zou kunnen zoeken. Dit was echter niet het geval. De meeste
endpoints waren niet juist en kwamen er rare resultaten terug bij het proberen te fetchen van de data.
Dit waren de beschikbare endpoints:
<img width="342" alt="Schermafbeelding 2022-07-06 om 20 10 10" src="https://user-images.githubusercontent.com/70690100/177615499-87c92078-28ea-4d38-9d32-b6ab395a3485.png">

Om uiteindelijk één specifieke foto te kunnen renderen per planeet moest ik via ee NASA id zoeken zodat er maar een bepaalde foto zou terug komen. 
Op de website van de API kon ik gemakkelijk browsen tussen verschillende prachtige foto's van de planeten. Door de beschikbare id's kon ik via
het id endpoint zoeken naar één bepaalde foto.

Endpoint gebruikt voor foto planeet Jupiter:
````
const baseURLapi = 'https://images-api.nasa.gov/asset/PIA01492'
````
