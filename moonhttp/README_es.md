# Manual de usuario: moonhttp (`moonhttp-v1.0.6`)

**moonhttp** (también denominado `moonhttp`) es un servidor HTTP local ligero, rápido y orientado a la visualización. Desarrollado en Lua, está diseñado para ejecutarse como un ejecutable binario independiente y multiplataforma (compatible con Linux, Windows y macOS).

Su objetivo principal es agilizar el desarrollo local de páginas web estáticas, ofreciendo una experiencia sólida mediante la interfaz de línea de comandos (CLI) y proporcionando información inmediata sobre la integridad de los archivos del proyecto.

---

## 1. Módulos y tecnologías utilizados

Para construir y empaquetar el binario independiente, se utilizaron únicamente bibliotecas esenciales del ecosistema Lua:

* **LuaJIT / Lua 5.1**
---

## 2. Características del servidor HTTP local

**moonhttp** implementa las funciones esenciales requeridas para un entorno de desarrollo en *localhost*:

* **Vinculación de red flexible:** Admite la escucha en interfaces de bucle local (*loopback*: `127.0.0.1`, `localhost`), resolución de IP de red local y hosts personalizados.
* **Resolución automática de directorios:** Redirección `301 Moved Permanently` para rutas de carpetas que carecen de barra diagonal final (p. ej., `/about` -> `/about/`).
* **Gestión de página de índice:** Búsqueda automática de documentos `index.html`, `index.htm` o `index.lwa`.
* **Identificación de tipos MIME:** Reconocimiento automático de formatos `.html`, `.htm`, `.lwa`, `.css`, `.js`, `.ico` y `.png`, con un valor predeterminado (*fallback*) a `text/plain`.
* **Control de caché para desarrollo:** Transmisión automática de encabezados `Cache-Control: no-cache` para evitar que el navegador almacene versiones obsoletas de los archivos durante la edición.
* **Seguridad de métodos:** Restricción estricta a los métodos `GET` y `HEAD`, bloqueando intentos de modificación de archivos mediante `POST`, `PUT` o `DELETE` (devolviendo el código `405 Method Not Allowed`). * **Liberación inmediata del puerto:** Configuración `SO_REUSEADDR` para evitar el bloqueo del puerto a nivel del sistema operativo al finalizar la aplicación con `CTRL+C`.
* **Listado de archivos opcional:** Listado de directorios deshabilitado por defecto (devuelve un error `404 Not Found` por seguridad), pero puede habilitarse bajo demanda.

---

## 3. Inspector de archivos y soporte para aplicaciones web

### Inspección automática de recursos (Frontline)
Al servir cualquier documento HTML o `.lwa`, **moonhttp** analiza el código en tiempo real e inspecciona las etiquetas `<link>`, `<script>`, `<img>` y `<a href>`. Si un archivo referenciado no existe en la carpeta local, el servidor emite una **alerta visual en la terminal** notificando al usuario sobre el recurso faltante.

### Soporte para frameworks JS y aplicaciones web

* **Proyectos compatibles:** Sitios estáticos HTML5/CSS3/JS, páginas de aterrizaje (*landing pages*), plantillas locales, aplicaciones creadas con **Vanilla JS** y archivos `.lwa` (tratados como HTML estándar).
* **Builds de frameworks JS (React, Vue, Svelte, Angular):** **moonhttp** sirve sin problemas las carpetas de exportación estática generadas por estas herramientas (como `dist/` o `build/`) una vez ejecutado el comando de compilación (*build*) del framework.
* **Limitaciones (Lo que NO se admite):**
* No reemplaza al *DevServer* con *Hot Module Replacement* (HMR) utilizado durante el desarrollo activo con frameworks JS (Vite, Webpack, Next.js). * No ejecuta código dinámico del lado del servidor (Node.js, PHP, Python, etc.) ni interactúa con bases de datos.

---

## 4. Historial de versiones

* **v1.0.0:** Lanzamiento inicial con soporte para CWD (directorio de trabajo actual), vinculación de puerto/host mediante CLI, resolución de `index.html`/`index.htm`/`index.lwa`, registro de errores 404 y apagado ordenado (*graceful shutdown*).
* **v1.0.1:** Se añadió el **Inspector de dependencias estáticas**, visualización del logotipo en arte ASCII y enlaces en la terminal clicables mediante códigos de color ANSI.
* **v1.0.2:** Se añadió el manejo de rutas de carpetas/directorios sin barra diagonal final (*trailing slash*), con redirección HTTP `301`. * **v1.0.3:** Se añadieron restricciones de seguridad para los métodos HTTP (`GET`/`HEAD`), encabezados para evitar el almacenamiento en caché durante el desarrollo y soporte para la opción `--dir-list`.
* **v1.0.4:** modificaciones de seguridad.
* **v1.0.5:** modificaciones de seguridad.
* **v1.0.5:** modificaciones de seguridad.
---

## 5. Guía completa de comandos de la CLI

La sintaxis básica para ejecutar el binario es:

```bash
moonhttp [opciones]
```

### Parámetros y argumentos/opciones

| Descripción | Valor predeterminado |
| :--- | :--- |
|**-h:<host>**  | Establece la interfaz de red o el host en el que escuchar (predeterminado: 127.0.0.1)|
|**-p:<port>** | Establece el puerto de red del servidor (predeterminado: 7777, o 8888 para los perfiles moon/moonip)|
|**--dir-list** | * Habilita el listado de directorios si no existe un archivo de índice.|
|**-help o --help**| Muestra la ayuda de la aplicación con los comandos disponibles.|
|**-manual o --manual**| Muestra el enlace a manual.md en GitHub.|
|**-v, -version, o --version**| Muestra la versión de moonhttp.|

```
Deshabilitado (Devuelve 404)
Ejemplos prácticos de uso

1. Ejecución estándar (la más básica): Inicia el servidor en el directorio de trabajo actual (CWD),
utilizando la IP 127.0.0.1 y el puerto 7777. Bash: ./moonhttp | Windows: moonhttp.exe
```

#### 2. Uso del atajo de host 'localhost'

> Establece el host en 127.0.0.1 en el puerto 7777
```bash
./moonhttp -h:localhost
```

#### 3. Cambiar solo el puerto
> Inicia el servidor en el puerto personalizado 5677 manteniendo la IP 127.0.0.1.
```bash
./moonhttp -p:5677
```

#### 4. Uso del perfil 'moon'
> Utiliza el alias preconfigurado 'moon', que inicia el servidor en la IP 127.0.0.1 usando el puerto 8888.

```bash
./moonhttp -h:moon
```

#### 5. Compartir en la red local con 'moonip'
> Identifica automáticamente la IP de red local de la máquina (p. ej., 192.168.1.15) en el puerto 8888, permitiendo que otros dispositivos en la misma red Wi-Fi o Ethernet accedan al sitio.

```bash
./moonhttp -h:moonip
```

#### 6. Configuración de host y puerto personalizados
> Inicia el servidor en una dirección IP específica de la máquina y en un puerto de su elección.

```bash
./moonhttp -h:192.168.0.50 -p:3000
```

#### 7. Habilitación del listado de directorios
> Permite explorar la estructura de archivos en el navegador si la carpeta no contiene un archivo index.html.

```bash
./moonhttp --dir-list
```

#### 8. Comando avanzado (Red local + Puerto personalizado + Listado de directorios)

Inicia el servidor en la IP local de la máquina, utilizando un puerto definido manualmente y con el listado de directorios habilitado.

```bash
./moonhttp -h:moonip -p:9000 --dir-list
```
