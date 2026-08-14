# Sesión 4 — Tarjetas de Tarea
### Práctica de colaboración en equipo con Git y GitHub

## Antes de empezar (una sola vez por equipo)

1. **Un solo integrante** crea un repositorio nuevo y **vacío** en GitHub (sin README, sin `.gitignore` — así evitas archivos generados por la web que después choquen con tu propia subida).
2. Ese mismo integrante clona el repositorio vacío, copia ahí los 4 archivos del proyecto de práctica que compartió el profesor, y los sube **desde la terminal** — no arrastrándolos y soltándolos en la interfaz web de GitHub:

```bash
git clone https://github.com/USUARIO/NOMBRE-DEL-REPO.git
cd NOMBRE-DEL-REPO
# copia aquí index.html, style.css, script.js y README.md
git add .
git commit -m "Agrega archivos iniciales del proyecto de práctica"
git push
```

**Por qué importa usar un solo método para esto:** subir archivos por arrastrar y soltar en la web SÍ crea un commit real — tan válido como uno hecho por terminal — pero tu Git local no se entera de que ese commit existe. La siguiente vez que intentes hacer `git push`, GitHub lo va a rechazar (`! [rejected] main -> main (fetch first)`), porque tu copia local y el repositorio remoto avanzaron por caminos separados sin sincronizarse. Usa terminal de principio a fin para esta configuración inicial, y el problema no aparece.

3. Agrega un archivo `.gitattributes` para evitar problemas de finales de línea entre compañeros que usen Windows y compañeros que usen Mac (ver la nota de la Sesión 3 si no recuerdas de qué se trata):

```bash
echo "* text=auto" > .gitattributes
git add .gitattributes
git commit -m "Agrega .gitattributes para normalizar finales de línea"
git push
```

4. Agrega a **todos** los demás como colaboradores: `Settings` → `Collaborators` → `Add people`.
5. **Cada uno de los demás integrantes** clona el repositorio a su propia computadora — esto también se hace **una sola vez**, la primera vez que te unes al proyecto:

```bash
git clone https://github.com/USUARIO/NOMBRE-DEL-REPO.git
cd NOMBRE-DEL-REPO
```

---

## Antes de CADA tarea (esto se repite todo el semestre — no es parte de la configuración inicial)

`git clone` solo trae el repositorio la primera vez. Pero tus compañeros van a seguir subiendo trabajo nuevo a `main` mientras tú avanzas en la tuya — si no vuelves a sincronizar, tu punto de partida se queda "congelado" en el día que clonaste, y cualquier rama nueva que crees a partir de ahí nace desactualizada.

Por eso, **cada vez que vayas a empezar una tarea nueva**, sin excepción, corre primero:

```bash
git checkout main
git pull
```

---

## Reglas del ejercicio

- Cada integrante trabaja **únicamente** en la sección marcada con `TODO` para su rama — no toques otras secciones del código.
- Tu rama debe llamarse exactamente como se indica en tu tarjeta (esto facilita que el profesor identifique de quién es cada Pull Request).
- Al terminar, sube tu rama y abre un Pull Request. **No lo fusiones tú mismo** — otro integrante del equipo debe revisarlo y aprobarlo primero.

---

## Tarjeta 1 — Sección de Alumnos

**Rama:** `feature-alumnos`

**Archivo a modificar:** `index.html` (sección `<section id="alumnos">`)

**Tarea:** Reemplaza el texto `(Sección en construcción)` por una tabla HTML simple con al menos 3 alumnos de ejemplo, con columnas: Nombre, Matrícula, Semestre.

```bash
git checkout -b feature-alumnos
# edita index.html
git add index.html
git commit -m "Agrega tabla de alumnos de ejemplo"
git push origin feature-alumnos
```

---

## Tarjeta 2 — Sección de Materias

**Rama:** `feature-materias`

**Archivo a modificar:** `index.html` (sección `<section id="materias">`)

**Tarea:** Reemplaza el texto `(Sección en construcción)` por una lista (`<ul>`) con al menos 4 materias de ejemplo, cada una con su clave (ej. "ISW-204 — Diseño de Software").

```bash
git checkout -b feature-materias
# edita index.html
git add index.html
git commit -m "Agrega lista de materias de ejemplo"
git push origin feature-materias
```

---

## Tarjeta 3 — Validación del Formulario

**Rama:** `feature-validacion`

**Archivo a modificar:** `script.js`

**Tarea:** Antes de mostrar el mensaje de éxito, valida que:
- Ningún campo (nombre, correo, mensaje) esté vacío.
- El correo contenga al menos un `@` y un `.`

Si algo falla, muestra un mensaje de error en vez del mensaje de éxito.

```bash
git checkout -b feature-validacion
# edita script.js
git add script.js
git commit -m "Agrega validación básica al formulario de contacto"
git push origin feature-validacion
```

---

## Tarjeta 4 — Paleta de Colores

**Rama:** `feature-estilo`

**Archivo a modificar:** `style.css` (bloque `:root` al inicio del archivo)

**Tarea:** Cambia los valores de `--color-primario`, `--color-secundario` y `--color-acento` por una paleta distinta, pero que mantenga buen contraste de lectura.

```bash
git checkout -b feature-estilo
# edita style.css
git add style.css
git commit -m "Actualiza paleta de colores del sistema"
git push origin feature-estilo
```

---

## Ejercicio 5 — El Conflicto Provocado (después de fusionar las 4 tarjetas anteriores)

Este ejercicio es **intencional**: van a provocar un conflicto de fusión a propósito, en un entorno controlado, para aprender a resolverlo antes de que les pase solos, sin ayuda, a media entrega.

**Instrucciones para el profesor:** elige a dos integrantes del equipo (llámalos Alumno A y Alumno B).

1. **Ambos**, al mismo tiempo, corren:
```bash
git checkout main
git pull
git checkout -b conflicto-a    # Alumno A
git checkout -b conflicto-b    # Alumno B (en su propia máquina)
```

2. **Ambos** editan la misma línea exacta: la etiqueta `<p class="tagline">` en `index.html`, cambiando el texto del ciclo escolar, pero cada uno pone un texto distinto.

3. **Ambos** hacen commit y push de su rama.

4. **Alumno A** abre su Pull Request y lo fusiona primero — sin problema, es el primero en llegar.

5. **Alumno B** intenta fusionar su Pull Request — GitHub le va a marcar un conflicto, porque su rama modificó una línea que ya cambió en `main` desde que él creó su rama.

6. **Alumno B**, con ayuda del profesor o del equipo, resuelve el conflicto: decide (o combina) qué texto se queda, quita las marcas `<<<<<<<`, `=======`, `>>>>>>>`, hace commit, y completa la fusión.

**Pregunta de cierre para el grupo:** ¿qué hubiera pasado si, en vez de hacerlo en una demostración controlada, esto les hubiera pasado la noche antes de una entrega real con el cliente?
