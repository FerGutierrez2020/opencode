# Guía de instalación y ejecución de OpenCode

Documento creado a partir de las especificaciones de recursos de esta VM.
Fecha: 12 de septiembre de 2026.

## 1. Especificaciones de la VM

| Recurso          | Valor                                        |
|------------------|----------------------------------------------|
| Sistema operativo| Ubuntu 20.04 LTS (Focal Fossa)               |
| Kernel           | 5.4.0-37-generic (x86_64)                    |
| CPU              | 2 vCPU (Intel Core i5-9300H @ 2.40 GHz)      |
| Memoria RAM      | 3.8 GiB (aprox. 2.4 GiB disponibles)         |
| Swap             | 2.0 GiB                                      |
| Disco            | 31 GB total, aprox. 18 GB disponibles        |
| Entorno          | VMware (virtualización completa)             |
| Node.js / npm    | v10.19.0 / 6.14.4                            |
| VS Code          | Instalado (comando `code`, vía snap)         |

Con estas características (solo 2 vCPU y 3.8 GiB de RAM) el equipo es
suficiente para ejecutar OpenCode en modo terminal/TUI sin problema. No se
requiere un entorno gráfico ni recursos adicionales.

> Nota: en esta VM OpenCode ya se encuentra instalado en
> `~/.opencode/bin/opencode` (versión 1.18.30). La sección 2 documenta igualmente
> el proceso para repetirlo o instalarlo en otra máquina.

## 2. Instalación de OpenCode

### 2.1 Prerrequisitos

- Un emulador de terminal moderno (el terminal GNOME incluido en Ubuntu sirve).
- Una clave de API de un proveedor de LLM (p. ej. OpenCode Zen, OpenAI, Anthropic,
  etc.). OpenCode redirige la autenticación del proveedor a la página
  oficial si aún no tienes una.
- `npm` (Node Package Manager) instalado en el sistema. En caso de no tenerlo,
  se instala con el siguiente comando:

  ```bash
  sudo pip install npm
  ```

  > Nota: en esta VM `npm` ya se encuentra instalado (versión 6.14.4), por lo
  > que no es necesario ejecutar el comando anterior.

### 2.2 Método recomendado: script de instalación

El método más sencillo y el que funciona con Node.js antiguos (como el
v10.19.0 de esta VM) es el script oficial, que instala el binario en
`~/.opencode/bin`:

```bash
curl -fsSL https://opencode.ai/install | bash
```

Al terminar, asegúrate de que la ruta esté disponible en el `PATH`:

```bash
export PATH="$HOME/.opencode/bin:$PATH"
```

Para hacerlo permanente, agrega la línea anterior al final de `~/.bashrc`:

```bash
echo 'export PATH="$HOME/.opencode/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### 2.3 Métodos alternativos de instalación

- **Con npm** (requiere Node.js 18 o superior; en esta VM con Node 10.19.0 el
  script de la sección 2.2 es la opción adecuada):

  ```bash
  npm install -g opencode-ai
  ```

- **Con Homebrew** (solo si tienes Homebrew instalado):

  ```bash
  brew install anomalyco/tap/opencode
  ```

- **Con Docker**:

  ```bash
  docker run -it --rm ghcr.io/anomalyco/opencode
  ```

- **Arch Linux**:

  ```bash
  sudo pacman -S opencode
  ```

### 2.4 Verificar la instalación

```bash
which opencode
opencode --version
```

Salida esperada en esta VM:

```
/home/devasc/.opencode/bin/opencode
1.18.30
```

## 3. Configurar un proveedor de LLM (autenticación)

Para utilizar OpenCode es necesario configurar un proveedor:

1. Ejecuta `opencode`.
2. Dentro de la interfaz, ejecuta el comando `/connect`.
3. Selecciona un proveedor (por ejemplo **opencode** / OpenCode Zen) y abre la
   página de autenticación que se muestra.
4. Copia la clave de API y pégala cuando OpenCode la solicite.
5. Alternativamente, puedes configurar la clave vía variable de entorno:

   ```bash
   export OPENCODE_PROVIDER_API_KEY="tu_clave_aqui"
   ```

## 4. Ejecución desde consola (terminal)

1. Abre una terminal e ingresa a la carpeta del proyecto:

   ```bash
   cd /ruta/a/tu/proyecto
   ```

2. Inicia OpenCode:

   ```bash
   opencode
   ```

3. La primera vez, inicializa OpenCode para el proyecto con `/init`.
   Esto analiza el código y crea un archivo `AGENTS.md` con las reglas y
   convenciones del proyecto. Se recomienda guardarlo en Git.

4. Comienza a trabajar escribiendo solicitudes en lenguaje natural, por ejemplo:

   - *"How is authentication handled in [archivo]?"* → para preguntar sobre el código.
   - *"Add a new REST endpoint that lists the books"* → para agregar funcionalidad.
   - Usa la tecla `@` para buscar archivos del proyecto.

### 4.1 Comandos útiles dentro del TUI

| Comando  | Acción                                        |
|----------|-----------------------------------------------|
| `/init`  | Inicializa OpenCode para el proyecto          |
| `/undo`  | Deshace el último cambio realizado            |
| `/redo`  | Rehace un cambio deshecho                     |
| `<TAB>`  | Alternar entre modo Plan (solo plan) y Build  |
| `/share` | Crear un enlace para compartir la conversación|
| `/connect` | Configurar el proveedor / API key          |

## 5. Ejecución con Visual Studio Code

OpenCode se integra con VS Code mediante una extensión. El proceso es:

### 5.1 Instalación (automática)

1. Abre Visual Studio Code:

   ```bash
   code .
   ```

2. Abre la terminal integrada (`Ctrl+ù` o menú *Terminal* → *New Terminal*).
3. Ejecuta `opencode` en la terminal integrada. La primera vez, la extensión
   **OpenCode** se instala automáticamente.

Nota: es necesario que el comando `code` esté disponible en el `PATH`. En esta
VM está en `/snap/bin/code`. Si no lo tienes, dentro de VS Code presiona
`Ctrl+Shift+P`, busca "Shell Command: Install 'code' command in PATH" y
ejecútalo.

### 5.2 Instalación manual

Alternativamente, busca la extensión **OpenCode** en el *Marketplace de
extensiones* (icono de cuadrícula en la barra lateral) y pulsa *Install*.

### 5.3 Uso dentro de VS Code

- **Apertura rápida**: pulsa `Ctrl+Esc` para abrir OpenCode en un panel de
  terminal dividido o enfocar una sesión ya abierta.
- **Nueva sesión**: pulsa `Ctrl+Shift+Esc` para iniciar una nueva sesión de
  terminal de OpenCode (aunque ya exista una). También puedes pulsar el botón
  de OpenCode en la interfaz.
- **Contexto automático**: la extensión comparte automáticamente la selección
  o la pestaña activa con OpenCode.
- **Referencias a archivos**: usa `Alt+Ctrl+K` para insertar referencias de
  archivos como `@Archivo#L37-42`.

## 6. Referencias

- Documentación oficial: https://opencode.ai/docs
- Guía de instalación: https://opencode.ai/docs (sección *Install*)
- Integración con IDE: https://opencode.ai/docs/ide
- Repositorio: https://github.com/anomalyco/opencode