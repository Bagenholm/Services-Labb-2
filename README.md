# Services-Labb-2

Collection of repos for Web services och integrationer:
https://github.com/AdamBremholm/televisionspecs
https://github.com/Bagenholm/ReviewService
https://github.com/Bagenholm/Axesor
https://github.com/AdamBremholm/eurekaclient
https://github.com/AdamBremholm/gatewayzuul
https://github.com/AdamBremholm/authservice

Alla Docker containers ligger på Dockerhub, men om du vill bygga själv så går det bra från ovan repos. 

Docker build: 
 > docker build --tag=[SERVICE NAME]:[VERSION] --build-arg JAR_FILE=target/[JAR FILE NAME] .
ex..
> docker build --tag=eureka:v.0.0.1 --build-arg JAR_FILE=target/eurekaserver-0.0.1-SNAPSHOT.jar .

Ändra då i docker-compose så att image: är dina egna containers image tags. 


Impending TV Bang4Buck 

Körinstruktioner:

Ladda ner filen docker-compose och, i rätt mapp, skriv.. 
 > docker-compose up 

När alla Docker containers har laddats ner och körs kan du nå inloggning på localhost:8080.

user
password

(Ibland tar det tid innan något händer första gången man försöker logga in, ser ut som att det är något Ribbon inte gillar; testa igen om det tar mer än någon halv minut).

Bang4Buck kollar hur mycket du får betala för varje rating på en TV, samt hur mycket du får betala för varje rating baserat på en TV:s storlek.

Zuul, Eureka Discovery Service, Auth, Reviews, Televisions/Specs och Axesor services körs.

För att komma åt Axesor behövs en bearer token som hämtas från /auth med JSON body
	{"username":"user","password":"password"}
Skicka sedan med den som Bearer.

Det går även att använda login.html (första exemplet), som du redirectas till när du går till gatewayen. username:user, password:password.

Televisions/Specs och Reviews har ingen path i Zuul, utan nås endast genom Axesor. De är tomma till en början, men det går att populera dem genom att köra axesor/mockdata. Den anropar i sin tur både Specs/manytelevisions och Review/manyreviews med get-requests. Färdig data, hämtad från Prisjakts 49 mest populära TV-apparater, laddas från ReviewServicens ReviewController och Televisions/Specs TelevisionController i respektive service.

För att ladda in Televisions/Specs och Reviews till Axesor, kör axesor/loadinfo. Den hämtar all data och kombinerar dem baserat på modellnamn till Axesors.

Sortera dem efter antingen (price per rating) eller (price per rating per inch), dock utan stora skillnader mellan dem. 

Samtliga endpoints är alltså:

Alla @get
axesor/loadinfo
axesor/mockdata
axesor/axesors
axesor/ratingprice
axesor/ratingpriceinch


Det går även att aktivera path för review-app och specs-app i gatewayen, då finns det även:

@get, post
/review/reviews

@get
/review/reviews/{id}

@get, post
/specs/televisions

@get
/specs/televisions/{id}


Det går även att testa att scala servicerna. Just nu fungerar detta enbart på authservice eftersom de andra innehåller databaskopplingar. Vill man exempelvis starta tre instanser av authservice kör man docker-compose up --scale authservice=3.
