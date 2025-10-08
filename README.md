# flutter-docs-printing-guide
Este repositorio explica cómo implementar la lógica para generar documentos en PDF y tickets en Flutter, utilizando las librerías pdf, printing y blue_thermal_printer.

---
## 📖 Introducción

El proyecto flutter_print_demo es una guía práctica para implementar la generación de documentos en PDF y tickets en Flutter, utilizando las librerías pdf, printing y blue_thermal_printer.

El objetivo es brindar una solución multiplataforma para:
    - Crear PDFs personalizados desde Flutter.
    - Visualizar e imprimir documentos PDF directamente desde la aplicación.
    - Imprimir tickets físicos en impresoras térmicas Bluetooth.

Este repositorio sirve como punto de partida para aplicaciones que requieran reportes, facturas o tickets generados de forma dinámica y enviados a impresoras.

## ⚙️ Instalación y Configuración
### 1. Dependencias Principales
En el archivo pubspec.yaml se encuentran las librerías necesarias:
```yaml
dependencies:
  flutter:
    sdk: flutter
  blue_thermal_printer:
    path: ./packages/blue_thermal_printer # Impresora Bluetooth (local, versión 1.2.3)
  pdf: ^3.11.3 # Generación de PDFs
  printing: ^5.14.2 # Impresión y previsualización de PDFs
  android_intent_plus: ^6.0.0 # Manejo de Intents en Android
  intl: ^0.20.2 # Manejo de fechas y números
  flutter_image_compress: ^2.4.0 # Compresión de imágenes
  provider: ^6.1.5+1 # Manejo de estado
  path_provider: ^2.1.5 # Acceso a directorios locales
```
⚠️ Nota: 
- Se está usando la versión **1.2.3**: [blue_thermal_printer en Pub.dev](https://pub.dev/packages/blue_thermal_printer/versions) para descargarla.
- Al descargar el paquete, extraer los archivos, luego crear una carpeta con el nombre **blue_thermal_printer** y ubicar los archivos, por último mover la carpeta a:

```yaml
<proyecto_flutter>/packages/
```

- Luego de eso, debe quedar asi el paquete **blue_thermal_printer**:

```yaml
<proyecto_flutter>/packages/blue_thermal_printer
```

## 📦 Instalación de paquetes

Ejecutar en consola dentro del proyecto:
```sh
flutter pub get
```

O agregar paquetes manualmente con:
```sh
flutter pub add pdf printing intl flutter_image_compress provider path_provider android_intent_plus
```
⚠️ Nota: blue_thermal_printer está configurado como dependencia local (path: ./packages/blue_thermal_printer).
Es necesario tener el paquete dentro de la carpeta packages/ o sustituirlo por la versión publicada en pub.dev.

## 2. Configuración de Activos (Assets)

Se han definido recursos en la carpeta assets/ (ejemplo: logotipos):
```yaml
flutter:
  uses-material-design: true
  assets:
    - assets/logos/
```
Los recursos deben ubicarse dentro de la ruta assets/logos/ para poder ser cargados en los PDFs o tickets.

## 3. Configuración Adicional por Plataforma
### 📱 Android
Agregar permisos para Bluetooth en AndroidManifest.xml:
```xml
<uses-permission android:name="android.permission.BLUETOOTH"/>
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT"/>
<uses-permission android:name="android.permission.BLUETOOTH_SCAN"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```

## 🗂️ Arquitectura del Proyecto

El proyecto **`flutter_print_demo`** sigue una estructura organizada por módulos que facilita la **escalabilidad, mantenibilidad y reutilización del código**.  

La estructura de carpetas es la siguiente:
```text
FLUTTER_PRINT_DEMO/
├── 📁 .dart_tool/
├── 📁 android/
├── 📁 assets/
├── 📁 build/
├── 📁 ios/
├── 📁 lib/
│   ├── 📁 models/
│   │   └── 📄 estado_cuenta.dart
│   ├── 📁 service/
│   │   ├── 📄 generar_pdf.dart
│   │   └── 📄 impression_ticket.dart
│   ├── 📁 utils/
│   │   ├── 📄 pdf_utils.dart
│   │   ├── 📄 ticket_utils.dart
│   │   └── 📄 utils.dart
│   ├── 📁 widgets/
│   │   ├── 📄 tabla_demo.dart
│   │   ├── 📄 vista_previa.dart
│   └── 📄 main.dart
├── 📁 linux/
├── 📁 macos/
├── 📁 packages/
├── 📁 test/
├── 📁 web/
├── 📁 windows/
├── 📄 .flutter-plugins-dependencies
├── 📄 .gitignore
├── 📄 .metadata
├── 📄 analysis_options.yaml
├── 📄 pubspec.lock
├── 📄 pubspec.yaml
└── 📄 README.md
```
## 📂 Descripción de Carpetas Clave

### `lib/`
Contiene la lógica central del proyecto. Es el directorio más importante porque organiza el **modelo, servicios, utilidades y widgets**.

- **`models/`**  
  Define las entidades de datos que se utilizan en la aplicación.  
  - `estado_cuenta.dart` → Representa los datos de un estado de cuenta para imprimir en PDF o ticket.  

- **`service/`**  
  Contiene los servicios principales de impresión y generación de documentos.  
  - `generar_pdf.dart` → Lógica para construir documentos PDF con la librería `pdf`.  
  - `impresion_ticket.dart` → Lógica para generar e imprimir tickets en impresoras Bluetooth.  

- **`utils/`**  
  Agrupa funciones auxiliares y clases de soporte.  
  - `pdf_utils.dart` → Funciones de ayuda para manipular PDFs (formato de texto, cabeceras, estilos).  
  - `ticket_utils.dart` → Funciones de ayuda para tickets (alineación, tamaños de fuente, saltos de línea).  
  - `utils.dart` → Funciones genéricas que pueden ser reutilizadas en diferentes partes del proyecto.  

- **`widgets/`**  
  Widgets reutilizables para la interfaz de usuario.  
  - `tabla_demo.dart` → Ejemplo de tabla que puede renderizarse en PDF .  
  - `vista_previa.dart` → Componente para previsualizar documentos  antes de imprimir.  
  - `main.dart` → Widget raíz de la interfaz dentro de la carpeta `widgets/`.  

- **`main.dart`**  
  Punto de entrada de la aplicación Flutter (`runApp()`).

## 📦 Modelo: `EstadoCuenta`

El modelo **`EstadoCuenta`** representa la estructura de datos de un reporte financiero **simulado** dentro de la aplicación.  
Se utiliza principalmente para **generar documentos PDF** y **tickets impresos** que contienen información de movimientos contables.

---

## 📄 Definición

```dart
class EstadoCuenta {
  final String fecha;
  final String detalle;
  final double debito;
  final double credito;
  final double saldo;
  final String documento;
  final String tipo;
  final String referencia;

  EstadoCuenta({
    required this.fecha,
    required this.detalle,
    required this.debito,
    required this.credito,
    required this.saldo,
    required this.documento,
    required this.tipo,
    required this.referencia,
  });
}
```
### 🔗 Relación con Otros Componentes

**generar_pdf.dart** → Usa instancias de EstadoCuenta para construir tablas con los movimientos en documentos PDF.
**impresion_ticket.dart** → Convierte cada EstadoCuenta en una línea de ticket para impresión térmica.

## 🛠️ Servicios

### 1. Servicio: `GenerarPdf`

El servicio **`GenerarPdf`** se encarga de generar documentos PDF a partir de una lista de movimientos contables (`EstadoCuenta`).  
Soporta **formato A4** y **tickets térmicos** con ajuste automático de fuentes, tablas y pies de página.

#### 📄 Código principal
```dart
class GenerarPdf {
  static Future<Uint8List> generar({
    required List<EstadoCuenta> movimientos,
    PdfPageFormat format = PdfPageFormat.a4,
  }) async { ... }
}
```
#### 🔹 Parámetros
| Parámetro     | Tipo                 | Descripción                                                    |
|---------------|----------------------|----------------------------------------------------------------|
| `movimientos` | `List<EstadoCuenta>` | Lista de movimientos a incluir en el PDF.                      |
| `format`      | `PdfPageFormat`      | Formato de página (`A4` por defecto, puede ser tamaño ticket). |

#### 🔹 Flujo de trabajo

1. Se crea un documento PDF (pw.Document).
2. Determina si el formato es A4 o ticket usando PdfUtils.esA4().
3. Carga la fuente principal usando PdfGoogleFonts.openSansRegular().
4. Genera el encabezado (encabezado para A4, encabezadoTicket para tickets).
5. Construye el contenido:
    - Para A4: usa pw.MultiPage y tablas con encabezados y filas de movimientos.
    - Para ticket: usa pw.Page con pw.Column y detalle de cada movimiento.
6. Agrega pie de página con PdfUtils.piePaginaPdf().
7. Devuelve el PDF generado como Uint8List.

#### 🔹 Ejemplo de uso
```dart
final pdfBytes = await GenerarPdf.generar(
  movimientos: listaMovimientos,
  format: PdfPageFormat.a4,
);
await Printing.layoutPdf(
  onLayout: (_) => pdfBytes,
  format: PdfPageFormat.a4,
);
```
#### 🔹 Relación con otros componentes
Usa PdfUtils para generar encabezados, tablas y pies de página.
Consume modelos EstadoCuenta como base de datos temporal para el PDF.

### 2. Servicio: ImpresionTicket

El servicio ImpresionTicket permite imprimir tickets directamente en impresoras térmicas Bluetooth usando la librería blue_thermal_printer.
#### 📄 Código principal
```dart
class ImpresionTicket {
  static Future<void> imprimirTicket({
    required BuildContext context,
    required List<EstadoCuenta> movimientos,
  }) async { ... }
}
```
#### 🔹 Parámetros
| Parámetro     | Tipo                 | Descripción                                                    |
|---------------|----------------------|----------------------------------------------------------------|
| `context` | `BuildContext` | Contexto de la app para mostrar diálogos de selección de impresora.                      |
| `movimientos`      | `List<EstadoCuenta>`      | Lista de movimientos a imprimir en el ticket. |

#### 🔹 Flujo de trabajo

1. Crea instancias de TicketUtils y UtilitiesService.
2. Verifica que el Bluetooth esté disponible (verificarBluetoothDisponible).
3. Permite al usuario seleccionar la impresora (seleccionarImpresora).
4. Conecta con la impresora seleccionada (conectarImpresora).
5. Imprime:
    - Logo de la empresa (opcional, con UtilitiesService.loadLogoImage).
    - Encabezado con nombre de empresa, NIT y dirección.
    - Cada movimiento (EstadoCuenta) con detalles: fecha, detalle, débito, crédito, saldo, documento, tipo y referencia.
    - Pie de ticket con información de la aplicación (nombreEmpresa, versión).
6. Agrega saltos de línea y desconecta la impresora.

#### 🔹 Ejemplo de uso
```dart
await ImpresionTicket.imprimirTicket(
  context: context,
  movimientos: listaMovimientos,
);
```
#### 🔹 Relación con otros componentes
Utiliza TicketUtils para manejar conexión, impresión de texto y manejo de dispositivos Bluetooth.
Utiliza UtilitiesService para obtener información de la empresa y preparar imágenes para impresión.
Consume modelos EstadoCuenta como base de datos temporal para tickets.

#### 🔹 Consideraciones

La impresión en ticket depende de una impresora Bluetooth compatible.
Para PDF, se puede previsualizar con la librería printing antes de enviar a impresión.
Ambos servicios están diseñados para integrarse con widgets de previsualización o generación automática de documentos.


## 🛠️ Utilidades: `PdfUtils`

La clase **`PdfUtils`** proporciona funciones auxiliares para **generar PDFs** en Flutter, incluyendo:

- Encabezados y pies de página.
- Tablas responsivas y clave-valor.
- Layouts de dos columnas.
- Adaptación de contenido a **A4** o **tickets**.
- Carga de logos desde assets o archivos locales.


## 📄 Métodos Principales

### 1. `esA4`

```dart
static bool esA4(PdfPageFormat format)
```
Descripción:
Determina si un formato de página corresponde a A4.
**Parámetros:**
`format`: `PdfPageFormat` → Formato de página a evaluar.
**Retorno:**
`bool` → `true `si es A4, `false `en otro caso.

### 2. `encabezadoPagina`

```dart
static Future<pw.Widget> encabezadoPagina({
  double nombreFontSize = 14,
  double infoFontSize = 10,
  pw.Font? font,
})
```
Descripción:
Genera un encabezado para PDF en A4, incluyendo:
- Logo de empresa a la izquierda.
- Logo de demo a la derecha.
- Datos de empresa centrados (nombre, NIT, dirección).
**Parámetros opcionales:**
- `nombreFontSize`: tamaño de fuente del nombre de la empresa.
- `infoFontSize`: tamaño de fuente de NIT y dirección.
- `font`: fuente a usar en el texto.

**Retorno:**
`pw.Widget` →  Widget listo para agregar al PDF.

### 3. `piePaginaPdf`

```dart
static pw.Widget piePaginaPdf({pw.Font? font, double fontSize = 8})
```
Descripción:
Genera el pie de página del PDF con información de la aplicación:
```less
Generado por: [nombreEmpresa] | Versión: [version]
```

**Parámetros opcionales:**
- `fontSize`: tamaño de fuente del texto.
- `font`: fuente a usar en el texto.

**Retorno:**
`pw.Widget` →  Widget para pie de página.

### 4. `tablaClaveValor`

```dart
static pw.Widget tablaClaveValor<T>({
  required List<T> data,
  required String Function(T item) getKey,
  required String Function(T item) getValue,
  double fontSize = 10,
  pw.Font? font,
})
```
Descripción:
Crea una tabla de 2 columnas tipo clave-valor para cualquier lista de objetos.

**Parámetros:**
- `data`: lista de objetos T.
- `getKey`: función para obtener la clave.
- `getValue`: función para obtener el valor.
- `fontSize` y `font`: opcionales.

**Retorno:**
`pw.Widget` →  Tabla lista para PDF.

### 5. `encabezadoTicket`

```dart
static Future<pw.Widget> encabezadoTicket({
  double nombreFontSize = 7,
  double infoFontSize = 6,
  pw.Font? font,
  Uint8List? logoEmpresaBytes,
})
```
Descripción:
Encabezado adaptado a tickets térmicos, con altura infinita.

**Parámetros:**
- `nombreFontSize` y `infoFontSize`: tamaños de fuente.
- `logoEmpresaBytes`: logo opcional de la empresa.
- `font`: fuente para texto.

**Retorno:**
`pw.Widget` →  Widget de encabezado para tickets.

### 6. `detalleTicket`

```dart
static pw.Widget detalleTicket<T>({
  required List<T> items,
  required Map<String, String Function(T)> campos,
  pw.Font? font,
  double fontSize = 6,
})
```
Descripción:
Muestra información de forma vertical, genérico para cualquier objeto.
Ideal para tickets.

**Parámetros:**
- `items`: lista de objetos a imprimir.
- `campos`: mapa de nombre de campo → función que devuelve el valor del objeto.
- `font` y `fontSize`: opcionales.

**Retorno:**
`pw.Widget` →  Column con todos los campos de cada item.

### 7. `tablaResponsive`

```dart
static pw.Widget tablaResponsive({
  required List<List<String>> rows,
  List<String>? headers,
  double fontSize = 10,
  pw.Font? font,
})
```
Descripción:
Genera una tabla adaptable a cualquier tamaño de página, útil para listas largas.

**Parámetros:**
- `rows`: filas de datos como listas de strings.
- `headers`: encabezados opcionales.
- `font` y `fontSize`: opcionales.

**Retorno:**
`pw.Widget` →  Tabla responsiva lista para PDF.

### 8. `dosColumnas`

```dart
static pw.Widget dosColumnas({
  required pw.Widget izquierda,
  required pw.Widget derecha,
  double spacing = 10,
})
```
Descripción:
Crea un layout de dos columnas ajustable con espacio entre ellas.

**Parámetros:**
- `izquierda` ,`derecha`: widgets a mostrar en cada columna.
- `spacing`: espacio entre columnas.

**Retorno:**
`pw.Widget` →  Row con dos columnas.

### 9. `cargarLogos`

```dart
static Future<Map<String, pw.ImageProvider?>> cargarLogos({
  String? logoEmpresaPath = "assets/logos/yourLogoHere.png",
})
```
Descripción:
Carga logos para PDF desde:
- Assets del paquete (logo demo).
- Archivo local o assets (logo empresa).

**Parámetros:**
- `logoEmpresaPath`: ruta del logo de la empresa.

**Retorno:**
`Map<String, pw.ImageProvider?>` →  Claves: 'empresa', 'demo'.

#### 🔹 Consideraciones
- Todos los widgets generados con PdfUtils son compatibles tanto con PDF A4 como con tickets térmicos.
- Funciones como detalleTicket y tablaClaveValor permiten adaptabilidad a diferentes tipos de datos.
- La carga de logos se maneja de forma segura, con fallback si no se encuentra el archivo.

## 🛠️ Utilidades: `TicketUtils`

La clase **`TicketUtils`** facilita la **impresión de tickets térmicos** usando la librería `blue_thermal_printer`.  
Incluye funciones para **verificar Bluetooth**, **conectar impresoras**, **imprimir texto** y manejar errores de conexión.

## 📄 Propiedades

| Propiedad  | Tipo                  | Descripción |
|------------|---------------------|-------------|
| `bluetooth` | `BlueThermalPrinter` | Instancia única de la impresora Bluetooth. |

## 📄 Métodos Principales

### 1. `verificarBluetoothDisponible`

```dart
Future<bool> verificarBluetoothDisponible(BuildContext context)
```
Descripción:
Verifica si el dispositivo tiene Bluetooth disponible y muestra un mensaje si no es así.

**Parámetros:**
`context`: `BuildContext` → Contexto de la app para mostrar SnackBar.

**Retorno:**
`bool` → `true `si Bluetooth está disponible, `false `si no.

### 2. `desconectarSiConectado`

```dart
Future<void> desconectarSiConectado()
```
Descripción:
Desconecta la impresora si ya estaba conectada previamente.

**Parámetros:**
Ninguno.

**Retorno:**
`void`

### 3. `seleccionarImpresora`

```dart
Future<BluetoothDevice?> seleccionarImpresora(BuildContext context)
```
Descripción:
Obtiene las impresoras vinculadas y permite seleccionar una.

**Parámetros:**
`context`: `BuildContext` → CPara mostrar diálogo de selección

**Retorno:**
`BluetoothDevice?` → Impresora seleccionada o null si no hay dispositivos.

**Comportamiento adicional**:
- Si solo hay una impresora, se selecciona automáticamente.
- Si hay varias, se muestra un SimpleDialog con la lista.

### 4. `conectarImpresora`

```dart
Future<bool> conectarImpresora(BuildContext context, BluetoothDevice device)
```

Descripción:
Conecta con la impresora seleccionada y valida la conexión.

**Parámetros:**
`context`: `BuildContext`
`device`: `BluetoothDevice` → Impresora a conectar.

**Retorno:**
`bool` → true si se conectó correctamente, false en caso de error.

**Errores**:
Si falla la conexión, muestra un AlertDialog con opción de abrir configuración de Bluetooth.

### 5. `_mostrarErrorConexion`

```dart
void _mostrarErrorConexion(BuildContext context)
```

Descripción:
Muestra un diálogo de error de conexión y opción de ir a configuración Bluetooth.

**Parámetros:**
`context`: `BuildContext`

**Retorno:**
`void`

### 6. `abrirConfiguracionBluetooth`

```dart
void abrirConfiguracionBluetooth()
```
Descripción:
Abre la configuración de Bluetooth en Android usando AndroidIntent.

**Parámetros:**
Ninguno.

**Retorno:**
`void`

### 7. `removeAccents`

```dart
String removeAccents(String input)
```
Descripción:
Elimina acentos y caracteres especiales de una cadena para evitar problemas en la impresión.

**Parámetros:**
`input`: `String` → Texto original.

**Retorno:**
`String` → Texto sin acentos.

### 8. `imprimirTexto`

```dart
Future<void> imprimirTexto(String texto, {int size = 1, int align = 0})
```
Descripción:
Imprime texto en la impresora Bluetooth, con tamaño y alineación personalizados.

**Parámetros:**
`texto`: `String` → Texto a imprimir.
`size`: `int` → Tamaño de fuente (1-3).
`align`: `int` → Alineación (0=izquierda, 1=centro, 2=derecha).

**Flujo de trabajo:**
1. Sanitiza el texto eliminando acentos.
2. Envía el texto a la impresora usando printCustom.

**Retorno:**
`Future<void>`

####  Consideraciones
- La clase está diseñada para trabajar con tickets térmicos Bluetooth en Android.
- Incluye manejo de errores, selección de impresora y desconexión segura.
- removeAccents garantiza compatibilidad con la mayoría de impresoras térmicas.

## 🖥️ Widgets de UI en flutter_print_demo

Los widgets en lib/widgets/ permiten visualizar datos de estado de cuenta, generar PDFs y tickets, e interactuar con servicios de impresión.

### 1. TablaDemoPage

Ubicación: lib/widgets/tabla_demo.dart
Propósito:
- Mostrar los movimientos de EstadoCuenta en una tabla interactiva.
- Permitir navegación hacia la vista previa PDF (VistaPrevia).

#### Componentes principales:
- Lista de movimientos (movimientos): Simula datos de estado de cuenta para la tabla.
- DataTable: Muestra filas y columnas con información detallada (Fecha, Detalle, Débito, Crédito, Saldo, Documento, Tipo, Referencia).
- Botón "Generar Reporte": Navega a VistaPrevia para PDF/ticket.

#### Relación con servicios y utilidades:
- UtilitiesService.onWillPop() → intercepta botón de regreso para mostrar confirmación.
- Flujo hacia VistaPrevia, donde se llama a GenerarPdf e ImpresionTicket.

#### Flujo de interacción:
- Usuario visualiza la tabla con movimientos.
- Presiona Generar Reporte → navega a VistaPrevia.

#### Detalles de diseño:
- Color de encabezado y filas alternadas.
- Scroll horizontal si la tabla excede ancho de pantalla.
- Card con borde redondeado y sombra.

### 2. VistaPrevia

Ubicación: lib/widgets/vista_previa.dart
#### Propósito:
- Mostrar previsualización del PDF o ticket generado.
- Permitir imprimir en impresora térmica o PDF.

#### Componentes principales:
- PdfPreview (paquete printing): Renderiza el PDF en memoria y permite compartirlo.
- Botones de acción:
    - Imprimir Ticket → ImpresionTicket.imprimirTicket().
    - Imprimir PDF → GenerarPdf.generar() y Printing.layoutPdf().
- Selector de formato de página: A4 o ticket (57 mm ancho, altura infinita).

#### Relación con modelos y servicios:
- Recibe lista de EstadoCuenta desde TablaDemoPage.
- Usa GenerarPdf para crear PDFs con encabezado, tabla y pie de página.
- Usa ImpresionTicket para enviar datos a impresora Bluetooth.
- Utiliza UtilitiesService para lógica de alerta antes de salir de la pantalla.

#### Flujo de interacción:

- Usuario selecciona vista previa del documento.
- Puede elegir imprimir en ticket o PDF.
- Selecciona formato de página en PdfPreview.
- Se llama a servicios para generar documento y enviar a impresora o compartir.

#### Diseño UI:
- AppBar con botón de regreso que muestra alerta antes de salir.
- Botones con iconos (receipt, print) para acciones rápidas.
- Layout flexible con Expanded para previsualización del PDF.

### 🔹 Notas de integración de widgets
- Separación de responsabilidades:
    - TablaDemoPage → UI de tabla.
    - VistaPrevia → UI de documento (PDF/Ticket).
    - Servicios (GenerarPdf, ImpresionTicket) → lógica de negocio.
- Uso de utilidades:
    - UtilitiesService → manejo de alertas y confirmaciones.
    - PdfUtils → construcción de encabezados, tablas y pies de página.
- Escalabilidad:
    - Se puede reutilizar VistaPrevia con otras listas de datos.
    - Se pueden agregar nuevas acciones de PDF (guardar, enviar correo) sin modificar la UI base.

---



