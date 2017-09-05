ARPhone: Augmented reality i en webapp på telefonen
==

Her er to webapper som viser noen muligheter for å lage augmented reality på mobil:

* Appen "sensor" tegner opp en kunstig horisont og viser orienteringen til telefonen (kompassretning, pitch og roll).
* Appen "tracker" tegner opp en te-kanne ("Utah Teapot") oppå en fysisk markør. Markøren er en figur som du må printe ut eller vise på en skjerm for at appen skal fungere.

Webappene krever Chrome versjon 59 eller nyere om du har Android og iOS11 om du har iPhone. (Fram til iOS 11 er lansert må du bruke beta-versjonen av iOS 11 dersom du har iPhone.)

Sensor-appen bruker HTML5-APIene WebRTC, DeviceOrientation og Canvas for å gjøre henholdsvis live videostrømming, utlesing av orienteringen av mobilen og opptegning av videostrøm med kunstig horisont oppå.

![Sensor-app på Android](images/sensorapp.jpg)

Tracker-appen bruker [AR.js](https://github.com/jeromeetienne/AR.js) for å gjøre mesteparten av jobben. AR.js kan kort beskrives som en plugin til [three.js](https://threejs.org/) og koden i tracker-appen er i stor grad tatt fra en demo-applikasjon laget av [Jerome Etienne](https://twitter.com/jerome_etienne). Du vil også trenge selve [Hiro-markøren](images/Hiro.pdf) for å kunne teste applikasjonen. Markøren kommer opprinnelig fra [ArToolKit](http://www.hitl.washington.edu/artoolkit/). [Tekannen](https://en.wikipedia.org/wiki/Utah_teapot) er et klassisk eksempelobjekt i 3D-grafikk. Filen som brukes her er hentet fra [siden til Pyarelal Knowles](http://goanna.cs.rmit.edu.au/~pknowles/models.html).

![Tracker-app på iPhone](images/trackerapp.jpg)

Oppsett for utvikling og testing av applikasjonene
--

Slik kan du sette opp et utviklingsmiljø for å teste ut og jobbe videre med applikasjonene:

**Webserver**

For rask roundtrip mellom utvikling og testing trenger du en lokal webserver. Serveren må svare på forespørsler på https, dette
er nødvendig for at nettleseren på mobilen skal åpne for bruk av WebRTC-APIet for live videostrømming.

Se katalogen `httpserver/` for en ferdig Node-basert webserver som kan brukes. Serveren er i stor grad basert på [kode skrevet av Adrian Mejia](https://gist.github.com/amejiarosario/53afae82e18db30dadc9bc39035778e5). NB NB Serveren kan ha store sikkerhetshull. Den er kun ment for lokal utvikling.

**Selv-signert sertifikat**

Du må lage et selv-signert sertifikat som er bundet til utviklingsmaskinen din. Sertifikatfilene skal leses inn av webserveren. Sertifikatet kan f.eks opprettes med OpenSSL, ved å skrive denne kommandoen i et terminalvindu:

`openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -nodes`

Du vil da få en serie spørsmål. På de fleste av dem vil det være godt nok å velge default-verdien. Unntaket er der hvor man skal angi `Common Name`. Der _må_ utviklingsmaskinens hostnavn angis. På Mac vil ting fungere om man setter inn tekststrengen i `$HOSTNAME`.

Spørsmålene kan f.eks se slik ut:

<pre>What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank.
For some fields there will be a default value,
If you enter '.', the field will be left blank.
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:(her_skriver_du_hostnavnet)
Email Address []:</pre>

Resultatet fra kommandoen er at filene `key.pem` og `cert.pem` blir generert. Legg disse inn i katalogen `httpserver/`.

**Start webserveren**

Start serveren ved å gå til `httpserver/` i et terminalvindu og skrive `node server.js`. Teksten

<pre>Server listening on port 8443</pre>

skal da vises på kommandolinjen.

**Åpne applikasjonen på mobilen**

Dersom du har Mac/iPhone og lar telefonen bruke samme trådløse nett som  utviklingsmaskinen skal du kunne angi maskinnavnet (verdien i `$HOSTNAME`) på mobilen for å nå utviklingsmaskinen. Bruk https og portnummer 8443. Eksempel-URL:

<pre>https://utviklingsmaskin:8443/</pre>

Om dette ikke fungerer, prøv å finne IP-adressen til utviklingsmaskinen (via kommandoen `ifconfig` på Mac/Linux og `ipconfig` på Windows) og oppgi den istedet. Husk på https og port 8443.

**Aksept av selv-signert sertifikat**

Når du prøver å nå utviklingsmaskinen fra telefonen vil den gi deg et varsel om at identiteten til nettstedet du går mot ikke nødvendigvis er ekte. For å kunne se applikasjonene på mobilen må du bekrefte at du aksepterer at ditt egne selv-signerte sertifikat brukes.

**Remote debugging**

For å kunne debugge og steppe igjennom koden på mobilen må du koble den til utviklermaskinen via ledning (USB). Om du bruker en iPhone må du laste ned og starte opp Safari 11 Technology Preview på utviklermaskinen. (Fram til Safari og iOS versjon 11 er lansert må Technology Preview-utgaven brukes). Bruker du en Android-telefon vil det være nok å starte opp Chrome på utviklermaskinen.

Deretter må du åpne for remote debugging mot telefonen din. På iPhone, velg `Settings > Safari > Advanced` og skru på "Web Inspector".

Når dette er gjort skal du kunne koble deg til telefonens nettleser via nettleseren på utviklermaskinen din. I Safari, velg `Develop > (navnet på mobilen) > "Use for development"`. Deretter kan du velge `Develop > (navnet på mobilen) > (navnet på applikasjonen)`. Du kan da inspisere kode og DOM og ellers debugge som på en lokal maskin.
