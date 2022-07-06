# NASA Space Quiz
<img width="1390" alt="Schermafbeelding 2022-07-06 om 19 58 42" src="https://user-images.githubusercontent.com/70690100/177613755-d9e21d4b-d51a-4720-9aee-fc8cdcbbf211.png">

## De opdracht
Voor Real Time Web heb ik een real-time webapplicatie gemaakt waarbij er een open connectie is tussen de client en server side door middel van 
websockets. De webapplicatie is multi user supported en er wordt data gebruikt uit een externe data bron. 

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
Echter was er een zware tegenvaller. Ik kwam er namelijk achter dat de id's meerdere foto's teruggaven na het fetchen van de data. Ik vond dit erg
apart omdat een id's meestal voor een specifiek object is. Dit heb ik gelukkig makkelijk kunnen oplossen door elke keer met een index alleen de 
eerste foto op te halen en alleen deze te renderen.

### Conclusie NASA Photo and Video library API
Voor een Amerikaanse overheidsinstantie vind ik de API zwaar tegenvallen. Ik vind dat de documentatie onjuist en vaag is. Hierdoor kost het 
veel tijd om uberhaupt iets van data te kunnen ophalen. Ik zou niet zo snel deze API meer gebruiken ongeacht de prachtige content die je eruit kan
krijgen. Als experiment heb ik eerder de picture of the day api gebruikt van NASA. Deze API was super makkelijk te gebruiken en had deze situatie daarom
niet verwacht.

## Database Mongodb
Voor de NASA Space quiz heb ik besloten om een database te gebruiken, dit omdat de API die ik gebruik alleen een foto levert als data. Dit terwijl 
de vragen die ik gebruik meer content zoals een een titel, hint en antwoord moeten hebben. Om van elke vraag daadwerkelijk een object te maken heb ik 
daarom besloten om al deze informatie op te slaan als een object in de database. Zo kan ik gemakkelijk per vraag de juiste data ophalen en renderen. 
Door het gebruik van de database is de juiste informatie aan de juiste vraag gekoppeld en heb ik hier gemakkelijk toegang tot.

### Connectie maken met de database
Voordat er data in de database kan worden gezet moet er eerst een connectie zijn met de database. Om makkelijk te communiceren met de database heb
ik gebruik gemaakt van Mongoose. Wat communiceren gemakkeijker maakt. 
````
const mongoose = require('mongoose');
const Question = require('./models/Question.js')
const { getEnvironmentData } = require('worker_threads')
const dbURI = 'mongodb+srv://<username>:<password>@nasaspacequiz.zrhfg.mongodb.net/?retryWrites=true&w=majority';

mongoose.connect(dbURI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then((result) =>  console.log('Mongodb connected'))
  .catch((error) => console.log(error + 'has occured'))

http.listen(port, () => {
  console.log('Socket io application is running on ' + port)
})
````
Om een connectie te maken wordt eerst Mongoose geninstalleerd en tot een variabele gemaakt in de app.js. Tevens is het juiste datamodel nodig. 
In het volgende kopje wordt dit geheel uitgelegd. En als allerlaatst is de data base URI nodig, die de standaard naam dbURI heeft. Deze URI kan
gemakkelijk opgevraagd worden vanuit het Mongodb dashboard op het internet. Door middel van mongoose.connect wordt er uiteindelijk een connectie met
de database gemaakt. 

### Data Schema voor de quiz vragen
Om de data juist gestructureerd in de database te krijgen moet er in een Javascript bestand een Schema voor de quiz vragen worden opgesteld.
Dit Schema bepaald wat voor soort content er in de database moet worden geplaatst.

#### Question Schema
````
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const questionSchema = new Schema({
    title: {
        type: String,
        required: true
    },
    hint: {
        type: String,
        required: true
    },
    image: {
        type: String,
        required: true
    },
    answer: {
        type: String,
        required: true
    }
})

const Question = mongoose.model('Question', questionSchema);
module.exports = Question;
````
````
const Question = require('./models/Question.js')
````
In het Schema wordt er aangegeven dat elke vraag een titel, hint, foto en antwoord nodig heeft. Elk veld is een string en ze zijn ze allemaal een 
verplicht veld die nodig zijn voor het opstellen van een vraag. Om het Schema te koppelen aan een variabele wordt deze onder aan de pagina gedeclareerd door middel van mongoose.model. Dit geeft aan dat onder de naam Question het questionSchema hoort. Om het Schema te exporteren wordt module.export gebruikt en wordt deze later opgehaald in de app.js.

### Quiz vragen in database zetten
Om daadwerkelijk de quiz vragen in de database te zetten heb ik besloten om een simpele admin pagina te maken die een formulier bevat. Dit formulier
heeft voor elk dataveld een invoerveld waarin de juiste content kan worden geplaatst. 
<img width="1383" alt="Schermafbeelding 2022-07-06 om 20 56 44" src="https://user-images.githubusercontent.com/70690100/177622841-ff24bf95-ed4e-4b6d-a3c4-8b2ee53749cd.png">
````
app.post('/admin', (req, res) => {
  const title = req.body.title;
  const hint = req.body.hint
  const image = req.body.image;
  const answer = req.body.answer;
  console.log(title, image, answer)
  
  const question = new Question({
    title: title,
    hint: hint,
    image: image,
    answer: answer
  });

  question.save()
    .then((result) => {
      console.log(result)
    })
    .catch((err) => {
      console.log(err)
    })
    res.render('admin')
})
````
In de app.post van de admin pagina wordt alle informatie van het formulier opgehaald. Met body-parser kon ik gemakkelijk door middel van req.body.'name'
de waardes van alle invoervelden ophalen. Op deze pagina wordt er vervolgens een nieuwe Question aangemaakt. Deze nieuwe vraag bevat het Question Schema 
waarbij alle waarden van de invoervelden aan de juiste datavelden worden gekoppeld. Nadat de data in het Schema is gezet wordt de data opgeslagen door 
middel van .save(). Daarna wordt de promise afgehandeld en wordt het resultaat in de console.log( ) gezet. Tevens wordt de admin pagina gerendert.

### Resultaat Mongodb Dashboard
<img width="1205" alt="Schermafbeelding 2022-07-06 om 21 04 39" src="https://user-images.githubusercontent.com/70690100/177624162-c273ff2c-22b5-4c1e-932e-5a5ea3cdbe42.png">










