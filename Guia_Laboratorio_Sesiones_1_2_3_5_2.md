# Taller de Git — Guía de Laboratorio
### Sesiones 1, 2, 3 y 5

Esta guía se trabaja en clase, un bloque a la vez. Cada sesión termina con un **checkpoint**: un comando o resultado que debes poder mostrar antes de pasar a la siguiente sesión. Si algo no funciona, pregunta antes de avanzar — cada sesión se construye sobre la anterior.

> La Sesión 4 (colaboración en equipo) se trabaja con un documento aparte: **Tarjetas de Tarea — Sesión 4**, junto con el proyecto de práctica que tu profesor te compartirá.

---

## Sesión 1 — Fundamentos y Configuración

### ¿Qué es Git? ¿Qué es GitHub?

- **Git** es un programa de control de versiones. Vive en tu computadora y funciona sin necesidad de internet.
- **GitHub** es una plataforma web que aloja repositorios de Git y agrega herramientas de colaboración (Pull Requests, revisión de código, etc.).

Son cosas distintas: puedes usar Git toda tu vida sin tocar GitHub jamás. En este taller usarás ambos.

### Verifica que Git esté instalado

```bash
git --version
```

Si no aparece un número de versión, avisa a tu profesor antes de continuar.

### Notas si usas Windows

**Usa Git Bash, no la terminal CMD.** Al instalar Git en Windows, se instala una terminal llamada Git Bash que entiende comandos estilo Unix (`ls`, `cat`, etc.), que son los que vas a usar en este taller. Búscala en el menú de inicio y ábrela ahora.

Si vienes de usar CMD, esta tabla te ayuda a ubicarte:

| CMD | Git Bash |
|---|---|
| `dir` | `ls` |
| `cd carpeta` | `cd carpeta` (igual) |
| `type archivo.txt` | `cat archivo.txt` |
| *(no existe)* | `pwd` → te dice dónde estás parado |

Tus discos de Windows aparecen con la letra en minúscula: `C:\Users\...` es `/c/Users/...` en Git Bash.

### Configura tu identidad en Git

```bash
git config --global user.name "Tu Nombre Completo"
git config --global user.email "tu_correo@ejemplo.com"
git config --global init.defaultBranch main
```

**Por qué importa:** cada commit que hagas de aquí en adelante queda firmado con este nombre y correo. Así es como, más adelante, se puede ver quién hizo qué dentro de un equipo — escribe tu nombre real, no un apodo.

### ✅ Checkpoint de la Sesión 1

```bash
git config --list
```

Confirma que tu nombre, correo, y `init.defaultbranch=main` aparecen correctamente en la lista antes de pasar a la Sesión 2.

---

## Solución de problemas: reiniciar tu práctica desde cero

Si en algún momento tu carpeta de práctica queda en un estado confuso (una rama que no reconoces, un repositorio inicializado donde no debía, o simplemente quieres empezar de nuevo), sigue este procedimiento. No es un error grave ni algo exclusivo tuyo — le pasa a la mayoría al menos una vez.

**Regla de oro, antes que nada — nunca corras `rm -rf` sin confirmar dónde estás parado:**

```bash
pwd
```

**1. Revisa si tu carpeta de usuario (`~`) se convirtió en repositorio por accidente**

Esto pasa si en algún momento corriste `git init` estando parado en `~` en vez de dentro de una carpeta de proyecto específica.

```bash
cd ~
ls -la
```

**Importante:** `ls` sin la bandera `-a` no muestra carpetas que empiezan con punto (como `.git`) — están ocultas por convención. Si no usaste `-la`, tu revisión no es concluyente — vuelve a correrlo así. Si aparece un `.git` directamente dentro de `~` (no dentro de una subcarpeta tuya), bórralo — esto no borra tus archivos, solo el registro de Git:

```bash
rm -rf ~/.git
```

**2. Borra tu carpeta de práctica específica y créala de nuevo**

```bash
cd ~
rm -rf practica-personal
mkdir practica-personal
cd practica-personal
```

**3. Inicia limpio**

```bash
git init
git status
```

Debe decir `On branch main` y `No commits yet` — ese es tu punto de partida real, sin rastros de intentos anteriores.

**Advertencia:** `rm -rf` borra de forma permanente, sin papelera de reciclaje. El hábito correcto es siempre `pwd` primero, para confirmar exactamente dónde estás parado antes de borrar cualquier cosa.

---

## Sesión 2 — Flujo Local Básico

### Los tres estados de Git

```
Directorio de trabajo  →  Área de preparación  →  Repositorio (historial)
    (tus archivos)            (git add)             (git commit)
```

`git commit` es una operación completamente local — no necesitas internet para hacerlo.

### Práctica: crea tu primer repositorio

```bash
mkdir practica-personal
cd practica-personal
git init
echo "# Mi práctica de Git" > README.md
git status              # README.md aparece como "untracked"
git add README.md       # lo mueves al área de preparación
git status              # ahora aparece como "staged"
git commit -m "Primer commit: agrega README"
git log                 # tu commit, con autor y fecha
```

### Comandos que vas a usar todo el tiempo

| Comando | Qué hace |
|---|---|
| `git status` | Qué cambió, qué está listo para guardar, qué falta |
| `git add archivo` | Prepara un archivo específico |
| `git commit -m "mensaje"` | Guarda una "fotografía" permanente de los cambios |
| `git log --oneline` | Historial compacto, un commit por línea |
| `git diff` | Qué cambió exactamente, línea por línea, antes de `add` |

### ✅ Checkpoint de la Sesión 2

Modifica tu `README.md` tres veces más. Antes de cada `git add`, corre `git diff` para ver el cambio resaltado. Al final, corre:

```bash
git log --oneline
```

Debes ver **al menos 4 commits** en tu historial. Muéstraselo a tu profesor o a un compañero antes de pasar a la Sesión 3.

---

## Sesión 3 — Ramas (Branches)

### ¿Por qué usar ramas?

`main` debe representar siempre una versión estable del proyecto. Una rama es una línea de trabajo paralela: puedes experimentar y equivocarte ahí sin afectar `main`, hasta que estés listo para fusionar (`merge`) tus cambios.

### Práctica

```bash
git branch                        # ver en qué rama estás
git checkout -b mi-primera-rama   # crea la rama y cámbiate a ella

echo "cambio de prueba" >> README.md
git add . 
git commit -m "Cambio de prueba en mi rama"

git checkout main                 # regresa a main
cat README.md                     # el cambio NO está aquí — vive solo en la rama

git merge mi-primera-rama         # fusiona los cambios a main
cat README.md                     # ahora sí aparece
```

*(Si usas CMD en vez de Git Bash, usa `type README.md` en vez de `cat README.md`.)*

### Nota — sobre un warning que probablemente ya viste

Al hacer `git add .`, es muy probable que te haya salido esto:

```
warning: in the working copy of 'README.md', LF will be replaced by CRLF the next time Git touches it
```

**No es un error.** Es sobre cómo se representa el "final de línea" de un archivo de texto: Linux/macOS usan **LF**, Windows tradicionalmente usa **CRLF**. Git for Windows normaliza automáticamente entre ambos — el warning solo te avisa que esa conversión va a pasar. Le va a salir a prácticamente todos tus compañeros con Windows; es el comportamiento default, no un síntoma de que algo esté mal en tu computadora.

Resuélvelo de una vez, antes de la Sesión 4 (donde vas a colaborar con compañeros que pueden usar sistemas distintos al tuyo):

```bash
echo "* text=auto" > .gitattributes
git add .gitattributes
git commit -m "Agrega .gitattributes para normalizar finales de línea"
```

Esto le dice a Git que normalice los finales de línea de forma consistente para todo el equipo, sin importar qué sistema operativo use cada quien.

### ✅ Checkpoint de la Sesión 3

```bash
git branch
git log --oneline --graph --all
```

Debes ver ambas ramas (`main` y `mi-primera-rama`), y el historial debe mostrar el punto donde se fusionaron.

### Conecta tu repositorio a GitHub (antes de trabajar en equipo)

Hasta ahora, todo tu trabajo ha vivido solo en tu computadora. Antes de la Sesión 4, donde vas a colaborar en un repositorio **compartido** con tu equipo, practica una vez por tu cuenta cómo se conecta un repositorio local con uno en la nube — así, en la sesión de equipo, ya conoces la mecánica y solo te concentras en la parte de colaborar.

1. Crea una cuenta gratuita en [github.com](https://github.com) si aún no tienes una.
2. En GitHub, crea un repositorio nuevo **vacío** (sin README, sin `.gitignore` — para evitar conflictos con lo que ya tienes localmente). Ponle de nombre, por ejemplo, `practica-personal`.
3. GitHub te va a mostrar una URL como `https://github.com/tu-usuario/practica-personal.git`. Úsala aquí:

```bash
git remote add origin https://github.com/tu-usuario/practica-personal.git
git push -u origin main
```

4. Refresca la página de tu repositorio en GitHub — deberías ver tu `README.md` y todo tu historial de commits, ya en la nube.

**Qué acaba de pasar:** `git remote add origin` le dice a tu Git local "este es tu repositorio en la nube correspondiente"; `git push` sube tus commits ahí. De aquí en adelante, cada vez que quieras respaldar o compartir tu trabajo, es un `git push` — nada más.

### ✅ Checkpoint final de la Sesión 3

Comparte con tu profesor el enlace a tu repositorio en GitHub (`https://github.com/tu-usuario/practica-personal`). Debe mostrar tu README y tu historial de commits — esa es la prueba de que la conexión funcionó, y de que estás listo para la Sesión 4, donde este mismo mecanismo se usará en equipo.

---

## Sesión 5 — Rastreo de Autoría y Cierre

*(Esta sesión se trabaja después de completar el ejercicio de equipo de la Sesión 4.)*

### Revisa el historial que construyó tu equipo

Sobre el repositorio compartido de tu equipo (el del proyecto de práctica o el de tu proyecto real):

```bash
git log --author="Nombre de un compañero"    # solo los commits de esa persona
git shortlog -sn                              # cuántos commits hizo cada quien
git log --stat                                # qué archivos y cuántas líneas cambió cada commit
```

### En GitHub: Insights → Contributors

Dentro del repositorio en GitHub, la pestaña **Insights → Contributors** muestra una gráfica de commits por persona a lo largo del tiempo. Es una forma visual y rápida de ver la participación de cada integrante del equipo.

### Buenas prácticas para lo que sigue del semestre

- **Mensajes de commit claros**: describe qué cambiaste, no solo "cambios" o "fix".
- **`.gitignore`**: evita subir archivos que no deberían estar en el repositorio (configuración local, credenciales, carpetas de dependencias).
- **README actualizado**: qué es el proyecto y cómo correrlo — es la primera impresión de tu trabajo.

### Reflexión de cierre (discusión en grupo)

- ¿Qué fue lo más confuso de todo el taller, y en qué momento dejó de serlo?
- Sobre el conflicto de fusión provocado en la Sesión 4: ¿cómo lo resolvieron, y qué harían distinto si les pasara sin avisar, a media entrega?
- ¿Cómo creen que esto va a cambiar la forma en que su equipo reparte el trabajo de aquí en adelante?

---

*A partir de aquí, cada historia de usuario del proyecto con cliente se trabajará en su propia rama, siguiendo exactamente el mismo flujo que practicaste hoy: rama → commits → Pull Request → revisión → fusión.*
