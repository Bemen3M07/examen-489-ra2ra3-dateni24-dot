# EXAMEN · MÒDUL 489

## Programació de Dispositius Mòbils i Multimèdia

**Unitats Formatives:** RA2 i RA3  
**Curs:** 2n DAM · Videojocs  
**Data:** 23/03/2026 
**Durada:** 2 hores  

**Alumne/a:** Daniel Tejedor Nieto 
**Grup:** 2ndo DAM 

---

## Posada en marxa de l'entorn

Consulta el fitxer **`README.md`** del projecte per a les instruccions completes d'instal·lació i arrencada (Node.js, servidor mock i Flutter).

---

> **Instruccions generals**
>
> - Respon cada pregunta en l'espai indicat (substitueix el text `[Escriu la teva resposta aquí]`).
> - Per a la part de codi, escriu directament en bloc `dart`. No és necessari que el codi compili, però ha de reflectir coneixement real de Flutter/Dart.
> - Tens el codi dels projectes **Cars** i **Phone** com a referència en el teu ordinador. **No pots accedir a internet** durant l'examen.
> - Desa el fitxer i lliura'l amb el nom: `EXAMEN_M489_[el_teu_nom].md`
> - Fes commit i push del .md modificat i de tots els arxius que hagis modificat

---

## BLOC 1 · ARQUITECTURA I CICLE DE VIDA *(RA 2)*

### Pregunta 1.1 – Comunicació entre Widgets *(12 punts)*

Al projecte **Cars**, el widget `CarsPage` gestiona el número de pàgina actual (`_currentPage`) i el passa a `CarsList1`. El widget `ButtonPanel` conté els botons "Anterior" i "Següent".

**a)** A `cars_page.dart`, el widget utilitza el mètode `setState` per gestionar la paginació. Explica:

- Quina és la funció de `setState` i per què cridar-lo fa que la UI es torni a dibuixar.
- Per quin motiu `_loadPage()` fa servir dos crides a `setState` separades (una a l'inici i una al final) en lloc d'una sola.

**Resposta:**

Antes del await _isloading = true primero muestra el iconito de carga mientras espera la respuesta de nuestra peticion.
Y entonces cuando llega el await y el _cars = resultat y entonces cambiamos el _isloading = false actualiza la lista y esconde el icono de carga al llegar los datos de la petición.

Hacen falta 2 llamadas si o si por que si se pusiera antes del await no tendriamos los datos nunca y si lo pusieramos despues que en principio saldrian los datos no saldria el icono de carga y pareceria que estuvieramos en la app bloqueados.  
---

```

---

### Pregunta 1.2 – Cicle de vida d'un widget amb recursos *(13 punts)*

Al projecte **Camera**, el widget `CameraScreen` utilitza un `CameraController` per gestionar la càmera del dispositiu. Aquest controlador ocupa recursos del sistema (càmera, memòria) i cal alliberar-los correctament.

**a)** Quin mètode del cicle de vida de `State` s'usa a `CameraScreen` per alliberar el `CameraController` quan el widget és destruït? Escriu com es fa i explica per quina raó és imprescindible cridar-lo.

El metodo es dispose()


ahora el codigo entero no me lo se de memoria pero este metodo que entra dentro de la clase State para poder sobrescribir o en este caso borrar el arbol de widgets que harian que elimine el proceso y liberando la memoria que se está utilizando. 
@override
void dispose() {

y por que es indispensable tenerlo? por varias razónes y alguna que me dejaré seguro... Por ejemplo que si se deja el proceso activo entonces la camara no podrá ser utilizada en otros procesos y tambien una muy obvia es que está utilizando recursos en segundo plano por lo que no es muy eficiente y probablemente tambien exista algun error de cache por tenerlo ahi ronando. 


---

**b)** El `CameraController` s'inicialitza de forma asíncrona a `initState()` i el resultat es guarda a `_initializeControllerFuture`. Respon les preguntes següents:

- Per quin motiu no es pot fer `await` directament a `initState()`?


Pues por que el void initState() no es un metodo async y en principio no devuelve Future por lo que entiendo que Flutter espera que se ejecute de manera sincronizada para poder completar la contruccion del widget, hacerlo async no es posible por que rompe el ciclo de vida, se podria hacer guardando el Future creo pero no añadiendo un async y ya. 

- Quina millora aporta a l'usuari usar `FutureBuilder` en lloc de bloquejar el fil?

El FutureBuilder lo hemos usado en clase en la practica hace que la interfaz sea interactiva y de respuestas mientras espera a que por ejemplo se inicie la camara en lugar de bloquearla con el hilo principal por ejemplo se podria poner un indicador de barra o circular como hemos visto ya en la practica asi que es muy util para que el usuario vea que la app esta trabajando y no una pantalla en negro sin mas. 

- Com treballen junts `_initializeControllerFuture` i `FutureBuilder`?

El FutureBuilder sobreescribe al Future y llama al constructor cada vez que el Future cambia de por ejemplo waiting a done o hace un catch de un error y el _initializeControllerFuture es como un puente que se crea una unica vez en el initState y pasado a FutureBuilder para que no se recicle cada vez que se haga un cambio antes dicho por ejemplo de waiting a done y vicebersa. 


## BLOC 2 · COMUNICACIÓ, PERSISTÈNCIA I PROVES *(RA 2 — 35 punts)*

### Pregunta 2.1 – Consum d'API i robustesa *(18 punts)*

Analitza el mètode `getCarsPage(int page, int limit)` de `car_http_service.dart`.

Què passaria si el servidor de l'API trigués 60 segons a respondre? L'aplicació quedaria bloquejada per a l'usuari? Per què? Escriu com implementaries un *timeout* de 10 segons a la petició HTTP.


La interfaz grafica no queda bloqueada como tal el hilo sigue procesando hasta que decidamos cerrar la app o la pagina o lo que sea si que yo personalmente siendo joven no esperaria mas de 60 segundos incluso menos tiempo por mucha barrita de progreso o circulo con animación que tengamos y hablo por practicamente cualquier usuario. 

```dart
// Escriu la modificació al getCarsPage aquí:
Future<List<CarsModel>> getCarsPage(int page, int limit) async {
  // ...
}
```
 response = await http {
    get(uri, headers: _headers)
    timeout(Duration(seconds: 10)); 
 }
con esta modificacion tenemos un timeout a los 10 segundos 
```
---

### Pregunta 2.2 – Models de dades  *(17 punts)*

Analitza el constructor `factory CarsModel.fromMapToCarObject(Map<String, dynamic> json)` de `car_model.dart`.

**a)** Imagina que l'API retorna per error el camp `year` com a `String` en lloc d'`int` (per exemple, `"2021"` en lloc de `2021`). El codi actual fallaria. Escriu com resoldries el problema.

**Resposta:**

```
Vale si fallara por error y da String

 year: json['year'] is String
        ? int.tryParse(json['year'] as String) ?? 0
        : (json['year'] as int? ?? 0),

        como solución robusta podriamos por ejemplo si por lo que sea da el error y es un String y nos da un null entonces tendriamos primero que parsear el año de String a int claro tambien tenemos que darle un valor por defecto que seria 0 y entonces luego pasarle el valor del año y entonces pasariamos del string a int, le damos el valor 0, recogemos el valor del coche y se lo otorgamos. 
```

---

**b)** Al fitxer `class_model_test.dart`, el test utilitza un `const jsonString` amb un JSON escrit a mà en lloc de fer una petició real a l'API de RapidAPI. Explica per quin motiu és millor simular el JSON en un test unitari.


Esta pregunta ha salido en el tipo test y daré la misma respuesta o como lo entiendo yo por ejemplo una razon de hacerlo a mano es que no dependemos de la red ni del server de RapidAPI si por lo que sea cae o cambia ya nos ahorramos ese problema, luego es mucho mas rapido hacerlo nosotros que tener que hacer las peticiones a otro server.
Y como hemos visto en el ejercicio anterior se pueden simular datos incorrectos para hacer test de errores y poner el año como String, si fuera en una pagina pues.. no se puede a menos que sea un servidor explicitamente para pruebas. 


```

---

## BLOC 3 · IMPLEMENTACIÓ PRÀCTICA *(RA 3 — 30 punts)*

### Exercici – Widget de detall amb dades remotes

Imagina que volem crear una pantalla de detall per a cada cotxe del projecte Cars. Implementa el mètode `build` d'un widget `StatelessWidget` anomenat `CarDetailPage` que compleixi els requisits següents:

1. Rep un paràmetre `final CarsModel car` al constructor.
2. Mostra el **make** i el **model** del cotxe com a títol destacat (`Text` amb estil gran i negreta).
3. Mostra una **icona diferent** depenent del `type` del cotxe:
   - Si el `type` és `'SUV'`, mostra `Icons.directions_car`.
   - Per qualsevol altre tipus, mostra `Icons.car_rental`.
4. Afegeix un botó `ElevatedButton` que, quan es premi, mostri un `SnackBar` amb el text: `"Cotxe seleccionat: [make] [model]"`.

```dart


class CarDetailPage extends StatelessWidget {
  final CarsModel car;

  const CarDetailPage({super.key, required this.car});

  @override
  Widget build(BuildContext context) {
    return Scaffold(

  appBar: AppBar(
     title: Text('${car.make} ${car.model}'),),
      body: Padding(
    child: Column(
       crossAxisAlignment:
          children: [
            Text(
              '${car.make} ${car.model}',
              style: const TextStyle(
                fontSize: 34,
                fontWeight: FontWeight.bold,
              ),
            ),
            Text('Tipus: ${car.type}', style: const TextStyle(fontSize: 16)),
            Text('Any: ${car.year}',   style: const TextStyle(fontSize: 16)),
            Text('Ciutat: ${car.city}', style: const TextStyle(fontSize: 16)),
            Test('Color: ${car.color}', style: cont TestStyle(fontSize: 16))

            ElevatedButton(
              onPressed: () {
                ScaffoldMessenger.of(context).showSnackBar(
                  SnackBar(
                    style: const TextStyle(
                      fontSize 20,
                    ),
                    content: Text('Coche seleccionado: ${car.make} ${car.model}'),
                  ),
                );
              },
          child: const Text('Selecciona un coche.'),
            ),
          ],
        ),
        ),
    );
      }
//No he sido capaz de hacer el que cambie de icono :(
```

---

**Ampliació (nivell Expert):** Afegeix un `FutureBuilder` que cridi al mètode `CarHttpService().getCarsPage(1, 5)` i mentre espera les dades mostri un `CircularProgressIndicator`. Quan les dades estiguin llestes, mostra un `ListView.builder` amb el `make` de cada cotxe. Si hi hagués un error, mostra un `Text` en color vermell amb el missatge de l'error.

```dart
//Escriu la teva ampliació aquí:
```

---

## BLOC 4 · EXTENSIÓ DEL SERVEI HTTP *(RA 2 — 10 punts)*

### Exercici 4.1 – Mètode parametritzat a `CarHttpService` *(10 punts)*

El servidor mock local té disponible un  endpoint de cerca:

```
GET http://localhost:8080/v1/cars/search?make=Toyota&model=Corolla
```

- El paràmetre `make` filtra per marca (coincidència parcial, insensible a majúscules).
- El paràmetre `model` filtra per model (coincidència parcial, insensible a majúscules).
- Tots dos paràmetres són opcionals: si no s'envien, retorna tots els cotxes.

Exemples vàlids:

- `/v1/cars/search?make=Toyota` → tots els Toyota
- `/v1/cars/search?model=X5` → tots els cotxes amb "X5" al model
- `/v1/cars/search?make=BMW&model=X` → BMW amb "X" al model

**Implementa** el mètode `getCarsByFilter` a la classe `CarHttpService` existent, seguint el mateix patrons que `getCarsPage`:

```dart
// Afegeix aquest mètode a car_http_service.dart:
```

Requisits:

1. Utilitza el mètode privat `_buildUri(String path, Map<String, String> queryParams)` ja existent.
2. Només afegeix els paràmetres `make` i/o `model` al mapa si el valor no és `null` ni buit (`isEmpty`).
3. Gestiona errors i timeout amb el mateix mecanisme que `getCarsPage`.

**Resposta:**

```dart
// Escriu aquí la teva implementació completa del mètode:

//Este no me va a dar tiempo en hacer... d
```

---

## Resum de l'examen

| Bloc | RA | Punts màxims |
|------|----|:------------:|
| Bloc 1 – Arquitectura i Cicle de vida | RA 2 | 25 |
| Bloc 2 – Comunicació, Persistència i Proves | RA 2  | 35 |
| Bloc 3 – `CarDetailPage` (base) | RA 3 | 20 |
| Bloc 3 – Ampliació `FutureBuilder`  | RA 3 | 10 |
| Bloc 4 – Extensió del servei HTTP | RA 2 | 10 |
| **TOTAL** | | **100** |

---
