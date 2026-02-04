### What problems does it present in terms of security, and how would you solve them?

Utilitzar un requirements amb versions específiques de les dependències és indiscutiblement més segur i estable que fer-ho sense cap especificació (com era el cas del requirements.txt abans com es pot veure al [[requirements.txt.orig]])

Tot i això, i per molt que especifiquem la versió de les dependències fins al tercer nivell per evitar suposadament qualsevol tipus d'actualització menor
> aquí veim com asgiref especifica tots els nivells de versió (3.11.0) evitant descarregar per exemple la versió 3.11.1 cosa que no passa amb Django que si pot sofrir aquest tipus d'imprevist menor.
>```txt
>asgiref==3.11.0
>Django==6.0
>```
el que no podem evitar així és ser sensibles a un atac malintencionat on es tropitja la versió específica d'una dependència afegint codi maliciós. 

Una de les formes amb la que podem evitar ser víctimes d'aquesta situació és utilitzar les versions per hash com recomana aquest [post](https://blog.rafaelgss.dev/why-you-should-pin-actions-by-commit-hash).

Abans de conèixer aquesta alternativa jo hagués preparat un repositori de "imatges docker propies" on es guardarien les imatges del build (es necessiti o no edició de la imatge) conservant així una versió fiable i inmutable propia del servei que necessitam.

### Modify the file and hand in a new, improved version via your fork of the repository. How did you get to such result?

Per actualitzar el fitxer requirements.txt correctament he seguit un tutorial que emplea `pip-compile`que he instal·lat a través de `pip insall pip-tools`.

Un cop instal·lat sols fa falta preparar una copia de l'arxiu requirements (`requirements.in` en aquest cas) i llançar sobre aquest nou arxiu la comanda:
`pip-compile --generate-hashes requirements.in`

Això generarà un arxiu [[requirements.txt]] nou com el que podem veure.

A partir d'aquest punt tan sols fa falta seguir un fluxe de actualitzar sempre el fitxer requirements.in en cas de necessitar-ho i compilar'l-ho de la mateixa manera (pot ser interessant generar un alias per agilitzar-ho)

Cada cop que s'hagin d'instal·lar dependències farem servir
`pip install --require-hashes -r requirements.txt`

### Describe the procedure you would recommend in a production scenario to make further modifications to it as time passes and new versions of software packages are released, and other versions are deprecated.

Com ja hem comentat el fluxe ideal és comptar amb el `requirements.in`com fitxer actualitzable i aprofitar el ci per dur a terme la compilació i instalació estricte final.

Com afegit al contexte de dinstints entorns com local, develop, producció... afegiría requirements.in diferents i adaptats a cada entorn dins el controlador de versions corresponent (git en aquest cas). D'aquesta forma l'estructura del projecte no tendria exclusivament un fitxer sino que comptaria amb, per ecemple:
```
requirements.dev.in
requirements.prod.in
requirements.local.in
...
```

I el pipeline identificaria quin utilitzar segons variables imposades a Git segons entorn (2 variables "GIT_ENV" on a la branca develop mostra el valor "dev" i la branca main el valor "prod")

### What scenarios are you trying to prevent with this new version?

Per una banda volem evitar atacs de supply chain amb falses "versions estables" i també hem aprofitat per aïllar els entorns de forma que el projecte sigui més flexible aprofitant les vars de Git i el workflow per evitar errors humans.