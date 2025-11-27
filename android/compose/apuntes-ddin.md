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


## 4.1. ``Column``

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


## 4.2. ``Row``

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


## 4.3. ``Box``

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


# 5. ``Spacer``

Se usa para añadir espacio vacío donde quieras.

```kotlin
Column {
    Text("Arriba")
    Spacer(modifier = Modifier.height(16.dp))
    Text("Abajo")
}
```

---

# 6. Arrangement & Alignment

## Arrangement

En un ``Column``:

### ``verticalArrangement``

Ordena elementos verticalmente:

* Top
* Center
* Bottom
* SpaceBetween
* SpaceAround
* spacedBy(…)


En un ``Row``:

### ``horizontalArrangement``

Igual pero en horizontal.


## Alignment

``horizontalAlignment`` *(Column)* → alinea hijos horizontalmente  
``verticalAlignment`` *(Row)* → alinea hijos verticalmente  
``contentAlignment`` *(Box)* → alinea TODO el contenido dentro de la Box  

Estas **NO** son modifiers. Son parámetros del layout.

```kotlin
Column(
    verticalArrangement = Arrangement.Center,
    horizontalAlignment = Alignment.CenterHorizontally
) {
    Text("Centrado")
}
```

---

# 7. Estados en compose

Compose es declarativo:
**si el estado cambia → la UI se cambia sola**

## 7.1. ``remember``

Guarda un valor en memoria mientras el composable siga vivo.
Si el composable se recompone, el valor permanece.
Si sales de la pantalla o se destruye la actividad → se pierde.

```kotlin
@Composable
fun ContadorRemember() {
    var contador by remember { mutableStateOf(0) }

    Button(onClick = { contador++ }) {
        Text("Clicks: $contador")
    }
}
```

### Para qué sirve

* Contadores
* Flags (isOpen, visible, etc.)
* Estados temporales

## 7.2. ``rememberSaveable``

Igual que remember, pero sobrevive a rotaciones o recreaciones de la actividad.

```kotlin
@Composable
fun Formulario() {
    var nombre by rememberSaveable { mutableStateOf("") }

    TextField(
        value = nombre,
        onValueChange = { nombre = it },
        label = { Text("Nombre") }
    )
}
```

### Para qué sirve

* TextFields
* Selección de pestañas
* Contadores importantes
* Formularios

## 7.3. ``mutableStateOf``, ``mutableIntStateOf``, ``mutableFloatStateOf``

Crean una variable observable.
Cuando cambian → Compose redibuja los composables que la usan.

```kotlin
var contador by rememberSaveable { mutableIntStateOf(0) }
```

### Para qué sirve

* Todo tipo de estados que quieres que afecten a la UI.

## 7.4. ``derivedStateOf``

Calcula un estado basado en otros, pero de forma eficiente.
Solo se recalcula cuando sus dependencias cambian.

```kotlin
@Composable
fun ListaFiltrada(lista: List<String>) {
    val filtrada by remember(lista) {
        derivedStateOf { lista.filter { it.startsWith("A") } }
    }

    LazyColumn {
        items(filtrada) {
            Text(it)
        }
    }
}
```

### Para qué sirve

* Listas filtradas
* Contadores derivados
* Estados calculados pesados

---

# 8. Modifiers por Scope

Muchos modifiers solo funcionan dentro de ciertos layouts.

## 8.1. RowScope / ColumnScope

### ``Modifier.weight(factor)``

Reparte espacio proporcional entre hijos.

#### Para qué sirve

* Hacer diseños tipo 30% / 70%
* Fila responsive
  
```kotlin
Row(Modifier.fillMaxWidth()) {
    Text("1 parte", Modifier.weight(1f))
    Text("2 partes", Modifier.weight(2f))
}
```

### ``Modifier.align()`` (dentro de Row o Column)

Alinea solo ese hijo dentro de la fila/columna.

#### Para qué sirve

* Centrar o mover hijos específicos

```kotlin
Column(Modifier.fillMaxWidth()) {
    Text("Inicio")
    Text("Centro", Modifier.align(Alignment.CenterHorizontally))
    Text("Fin")
}
```

## 8.2. BoxScope

### ``Modifier.align(Alignment.X)``

Alinea un hijo dentro de la Box.

#### Para qué sirve

* Overlays
* Posicionamiento absoluto

```kotlin
Box(Modifier.size(150.dp)) {
    Text("Centro", Modifier.align(Alignment.Center))
    Text("Esquina", Modifier.align(Alignment.BottomEnd))
}
```

### ``Modifier.matchParentSize()``

El hijo ocupa TODO el tamaño de la Box.

#### Para qué sirve

* Fondos
* Capas completas

```kotlin
Box(Modifier.size(120.dp)) {
    Box(Modifier.matchParentSize().background(Color.Gray))
    Text("Encima", Modifier.align(Alignment.Center))
}
```

## 8.3. LazyItemScope

### ``Modifier.fillParentMaxWidth()``

El item ocupa todo el ancho de la celda.

#### Para qué sirve

* Items uniformes en listas

```kotlin
LazyColumn {
    items(lista) { texto ->
        Text(texto, Modifier.fillParentMaxWidth().padding(16.dp))
    }
}
```

## 8.4. Scaffold Content Padding

Padding automático que evita que el contenido tape la TopBar/BottomBar.

### Para qué sirve

* Ajustar contenido internamente
* Evitar solapamientos

```kotlin
Scaffold(
    topBar = { TopAppBar(title = { Text("Ejemplo") }) }
) { padding ->
    Column(
        modifier = Modifier
            .padding(padding)
            .fillMaxSize()
    ) {
        Text("Contenido seguro")
    }
}
```

# 9. Modifiers Generales

Los modifiers generales son los que puedes aplicar a casi cualquier composable, siempre que acepte el parámetro modifier.
Son la base del diseño visual en Compose.

## 9.1. ``Modifier.padding()``

Añade espacio interno alrededor del composable.

### Para qué sirve

* Separar elementos
* Evitar que el contenido se pegue a los bordes
* Ajustar estructura visual

```kotlin
Text(
    "Hola",
    modifier = Modifier.padding(16.dp)
)
```

## 9.2. ``Modifier.background(color)`` 

Coloca un color de fondo detrás del composable.

### Para qué sirve

* Resaltar secciones
* Crear cajas visuales sin Card
* Fondos simples

```kotlin
Text(
    "Con fondo",
    modifier = Modifier
        .background(Color.Yellow)
        .padding(8.dp)
)
```

## 9.3. ``Modifier.border()``

Dibuja un borde alrededor del composable.

### Para qué sirve

* Resaltar un elemento
* Delimitar áreas
* Diseños de tarjetas personalizadas

```kotlin
Box(
    Modifier
        .size(80.dp)
        .border(2.dp, Color.Black)
)
```

## 9.4. ``Modifier.clip(shape)``

Recorta el contenido con una forma (círculo, esquinas redondeadas…).

### Para qué sirve

* Avatares circulares
* Imágenes con formas personalizadas
* Tarjetas con esquinas curvas

```kotlin
Image(
    painter = painterResource(R.drawable.avatar),
    contentDescription = null,
    modifier = Modifier
        .size(64.dp)
        .clip(CircleShape)
)
```

## 9.5. ``Modifier.size()`` / ``width()`` / ``height()``
Qué es

Define un tamaño fijo para el composable.

### Para qué sirve

* Iconos
* Cajas
* Imágenes con tamaño exacto

```kotlin
Box(
    Modifier
        .size(80.dp)  // 80dp x 80dp
        .background(Color.Red)
)
```

## 9.6. ``Modifier.fillMaxSize()`` / ``fillMaxWidth()`` / ``fillMaxHeight()``
Qué es

El composable ocupa todo el espacio disponible.

### Para qué sirve

* Botones grandes
* Pantallas enteras
* Contenedores que deben adaptarse

```kotlin
Button(
    onClick = {},
    modifier = Modifier.fillMaxWidth()
) {
    Text("Aceptar")
}
```

## 9.7. ``Modifier.alpha(valor)``

Ajusta la opacidad del composable (0f = invisible, 1f = opaco).

### Para qué sirve

* Estados deshabilitados
* Efectos visuales
* Animaciones

```kotlin
Text("Mitad visible", Modifier.alpha(0.5f))
```

## 9.8. ``Modifier.offset(x, y)``

Desplaza al composable de su posición original.

### Para qué sirve

* Microajustes de posición
* Efectos flotantes
* Animaciones manuales

```kotlin
Text(
    "Movido",
    Modifier.offset(x = 10.dp, y = 4.dp)
)
```

## 9.9. ``Modifier.shadow(elevation)``

Añade una sombra debajo del composable.

### Para qué sirve

* Efecto de tarjeta “flotante”
* Profundidad visual
* Diseño Material 3 personalizado

```kotlin
Box(
    Modifier
        .shadow(6.dp)
        .background(Color.White)
        .padding(16.dp)
) {
    Text("Con sombra")
}
```

## 9.10. ``Modifier.clickable { }``

Convierte cualquier composable en clicable.

### Para qué sirve

* Items de lista
* Textos clicables
* Cajas personalizadas
  
```kotlin
Text(
    "Púlsame",
    Modifier.clickable { /* acción */ }
)
```

# 10. Listas

## 10.1. ``LazyColumn``

Una lista vertical eficiente que solo renderiza los elementos visibles.

```kotlin
LazyColumn {
    items(lista) { item ->
        Text(item)
    }
}
```

### Para qué sirve

* Listas de datos largas
* Menú de opciones
* Chats, listados, ajustes, etc.

# 10.2. ``items`` y ``item``

Funciones de LazyColumn para renderizar contenido.

```kotlin
LazyColumn {
    item {
        Text("Cabecera")
    }
    items(lista) { item ->
        Text(item)
    }
    item {
        Text("Pie de lista")
    }
}
```

## Para qué sirve

* ``items(lista)`` → para listas completas
* ``item { ... }`` → para un elemento puntual (cabecera, pie, etc.)

# 10.3. Lista Horizontal (``LazyRow``)

Versión horizontal de LazyColumn.

```kotlin
LazyRow(
    horizontalArrangement = Arrangement.spacedBy(12.dp),
    contentPadding = PaddingValues(horizontal = 16.dp)
) {
    items(lista) { item ->
        Text(item)
    }
}
```

## Para qué sirve

* Menús horizontales
* Tarjetas desplazables
* Carruseles tipo Netflix

# 10.4. ``LazyVerticalGrid`` 

Layout eficiente en forma de grid (rejilla vertical).

## Para qué sirve

* Galerías de imágenes
* Catálogos
* Pantallas tipo e-commerce

```kotlin
LazyVerticalGrid(
    columns = GridCells.Fixed(2),
    modifier = Modifier.fillMaxSize(),
    contentPadding = PaddingValues(16.dp),
    verticalArrangement = Arrangement.spacedBy(8.dp),
    horizontalArrangement = Arrangement.spacedBy(8.dp)
) {
    items(lista) { item ->
        Text(
            text = item,
            modifier = Modifier
                .background(Color.LightGray)
                .padding(8.dp)
        )
    }
}
```

# 10.5. ``Sticky Headers``

Cabeceras que se quedan pegadas arriba al hacer scroll.

```kotlin
LazyColumn {
    stickyHeader {
        Text(
            "Sección A",
            modifier = Modifier
                .fillMaxWidth()
                .background(Color.DarkGray)
                .padding(8.dp),
            color = Color.White
        )
    }

    items(listaA) { item ->
        Text(item, Modifier.padding(16.dp))
    }

    stickyHeader {
        Text(
            "Sección B",
            modifier = Modifier
                .fillMaxWidth()
                .background(Color.DarkGray)
                .padding(8.dp),
            color = Color.White
        )
    }

    items(listaB) { item ->
        Text(item, Modifier.padding(16.dp))
    }
}
```

## Para qué sirve

* Lista de contactos por letra
* Ajustes con secciones
* Índices agrupados

---

# 11. COMPONENTES MATERIAL 3

Material 3 es la biblioteca oficial de UI de Android.
Incluye botones, tarjetas, barras, cajas de texto y estructuras modernas.

## 11.1. ``Text``

Componente para mostrar texto.

### Para qué sirve

```kotlin
Text(
    text = "Hola, Compose!",
    style = MaterialTheme.typography.titleLarge
)
```

* Títulos
* Descripciones
* Etiquetas

## 11.2. ``Button``

Botón con estilo Material 3.

```kotlin
Button(onClick = { /* acción */ }) {
    Text("Aceptar")
}
```

## 11.3. ``TextField``

Campo de texto editable.

```kotlin
var nombre by rememberSaveable { mutableStateOf("") }

TextField(
    value = nombre,
    onValueChange = { nombre = it },
    label = { Text("Nombre") }
)
```

### Para qué sirve

* Inputs de usuario
* Formularios
* Búsquedas

## 11.4. ``Card``

Contenedor con fondo, elevación o borde.

```kotlin
Card(
    modifier = Modifier
        .fillMaxWidth()
        .padding(8.dp),
    onClick = { /* acción */ }
) {
    Column(Modifier.padding(16.dp)) {
        Text("Título")
        Text("Descripción")
    }
}
```

### Para qué sirve

* Items de lista
* Productos
* Tarjetas informativas

## 11.8. ``Icon``

Elemento visual para mostrar iconos.

```kotlin
Icon(Icons.Default.Info, contentDescription = null)
```

### Para qué sirve

* Buttons
* Listas
* Navegación

## 11.9. Divider

Línea horizontal que separa contenido.

```kotlin
Column {
    Text("Ajuste 1")
    Divider()
    Text("Ajuste 2")
}
```

### Para qué sirve

* Listas
* Ajustes
* Secciones


## 11.13. ``TopAppBar``

Barra superior al estilo Material.

```kotlin
TopAppBar(
    title = { Text("Mi App") },
    navigationIcon = {
        IconButton(onClick = { }) {
            Icon(Icons.Default.ArrowBack, contentDescription = null)
        }
    }
)
```

### Para qué sirve

* Títulos
* Iconos de navegación
* Acciones rápidas

## 11.14. ``BottomAppBar``

Barra inferior simple.

```kotlin
BottomAppBar {
    IconButton(onClick = {}) {
        Icon(Icons.Default.Home, contentDescription = null)
    }
}
```

### Para qué sirve

* Navegación básica
* Iconos y acciones inferiores


## 11.15. ``FloatingActionButton``

Botón flotante circular.

```kotlin
FloatingActionButton(onClick = { }) {
    Icon(Icons.Default.Add, contentDescription = null)
}
```

### Para qué sirve

* Acción principal (añadir, crear, mensaje…)




