# 1. Jetpack Compose

Jetpack Compose es el sistema moderno de UI declarativa de Android.
En lugar de usar XML + Activities, la interfaz se construye solo con funciones Kotlin llamadas composables.

### Ventajas principales:

* Todo se hace en Kotlin, sin XML.
* Es reactivo: si el estado cambia → la UI se actualiza sola.
* Menos código y más rápido de escribir.
* Mucho más flexible para diseños complejos.

---

# 2. Composable

Un composable es simplemente una función con la anotación ``@Composable``:

```kotlin
@Composable
fun MiComponente() {
    Text("Hola Compose")
}
```

Y desde el ``setContent`` (dentro de una Activity), cargas la UI:

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MiComponente()
        }
    }
}
```

---

# 3. Esqueleto básico

La estructura más típica de una pantalla moderna en Compose es:

- 1º Scaffold → estructura general

- 2º TopAppBar / BottomBar

- 3º Contenido dentro de un Column o Box

```kotlin
@Composable
fun PantallaPrincipal() {
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Mi App") }
            )
        },
        bottomBar = {
            BottomAppBar {
                Text("Barra inferior")
            }
        },
        floatingActionButton = {
            FloatingActionButton(onClick = { /* acción */ }) {
                Text("+")
            }
        }
    ) { padding ->
        // Contenido principal
        Column(
            modifier = Modifier
                .padding(padding)
                .fillMaxSize()
                .padding(16.dp)
        ) {
            Text("Hola desde el contenido")
        }
    }
}
```

### Explicación:

``Scaffold {}`` → Estructura toda la pantalla.

``topBar`` → Barra superior.

``bottomBar`` → Barra inferior.

``floatingActionButton`` → Botón flotante (opcional).

Dentro del ``Scaffold``, recibes ``padding`` para evitar que el contenido se solape con la barra.

---

# 4. Layouts fundamentales:

* **Column**
* **Row**
* **Box**

Estos tres son el core del layout en Compose.

## 4.1. Column

Coloca los elementos uno debajo del otro.

```kotlin
Column(
    modifier = Modifier
        .fillMaxSize()
        .padding(16.dp),
    verticalArrangement = Arrangement.spacedBy(12.dp),
    horizontalAlignment = Alignment.CenterHorizontally
) {
    Text("Elemento 1")
    Text("Elemento 2")
    Text("Elemento 3")
}
```

### Para qué sirve:

* Pantallas completas
* Formularios
* Listas cortas
* Contenido vertical

## 4.2. Row

Coloca los elementos en horizontal, uno al lado del otro.

```kotlin
Row(
    modifier = Modifier.fillMaxWidth(),
    horizontalArrangement = Arrangement.SpaceBetween,
    verticalAlignment = Alignment.CenterVertically
) {
    Text("Izquierda")
    Text("Derecha")
}
```

## Para qué sirve:

* Cabeceras de listas
* Filas con icono + texto
* Alinear botones en una fila

## 4.3. Box

Permite superponer elementos o alinear dentro del contenedor.

```kotlin
Box(
    modifier = Modifier
        .fillMaxSize()
        .background(Color.LightGray)
) {
    Text(
        "Centro",
        modifier = Modifier.align(Alignment.Center)
    )

    Text(
        "Abajo derecha",
        modifier = Modifier.align(Alignment.BottomEnd)
    )
}
```

## Para qué sirve:

* Fondos + contenido encima
* Overlays
* Posicionar elementos en esquinas

---
