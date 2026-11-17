# 1.5 Entorno: instalar, compilar y ejecutar

## Qué hace falta

| Pieza | Para qué |
|---|---|
| **GnuCOBOL** | El compilador libre: la orden `cobc` |
| **Un editor** | Cualquiera con resaltado de sintaxis para COBOL (VS Code con la extensión *COBOL Language Support*, por ejemplo) |

GnuCOBOL es el compilador COBOL de código abierto más usado; no hay un compilador oficial único como el `javac` de Java. En Linux y macOS se instala con el gestor de paquetes del sistema; en Windows, lo más sencillo es instalarlo dentro de **WSL** (Subsistema de Windows para Linux), ya que no hay un instalador nativo de Windows mantenido de forma oficial.

## Instalar en Windows (con WSL)

```bash
wsl --install           # si WSL no está instalado todavía (pide reiniciar)
wsl                     # entra en la distribución de Linux
sudo apt update
sudo apt install gnucobol4
```

## Instalar en Linux (Debian/Ubuntu) o macOS

```bash
sudo apt install gnucobol4      # Debian/Ubuntu
brew install gnu-cobol          # macOS, con Homebrew
```

## Comprobar la instalación

```bash
cobc --version
```

## Compilar y ejecutar

```bash
cobc -x -o hola hola.cob      # -x: genera un ejecutable (no una librería)
./hola
```

| Opción | Qué hace |
|---|---|
| `-x` | Compila a un programa ejecutable (sin ella, genera un módulo para que lo llame otro programa) |
| `-o nombre` | Nombre del ejecutable de salida |
| `-free` | Acepta formato libre, sin las columnas de [1.1](01-programa.md) (no se usa en esta web, pero existe) |
| `-Wall` | Muestra todos los avisos del compilador |

Si ejecutas todo esto **dentro de WSL**, pero el archivo `.cob` está en tu carpeta de Windows (por ejemplo `C:\Users\tú\practica`), puedes llegar a ella desde WSL en `/mnt/c/Users/tú/practica`.

!!! note "Errores frecuentes"
    * `cobc: command not found`: GnuCOBOL no está instalado, o estás en una consola de Windows en vez de dentro de WSL.
    * `invalid indicator 'X' at column 7`: el código empieza antes de la columna 8 (ver la tabla de columnas de [1.1](01-programa.md)).
    * `continuation character expected`: una línea se pasa de la columna 72 y corta un literal de texto a mitad; parte la línea antes de llegar ahí.
    * El programa no hace nada al ejecutarlo: falta `STOP RUN`, o el programa sigue esperando un `ACCEPT` por teclado.
