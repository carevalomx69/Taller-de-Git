# Preguntas Frecuentes y Modelos Mentales
### Taller de Git — tarjeta de referencia rápida

Este documento no enseña comandos paso a paso (eso vive en la Guía de Laboratorio y en las Tarjetas de Tarea) — está pensado para consultarse cuando te surge una duda de "¿por qué funciona así?" o "¿qué se supone que debo hacer aquí?". Tenlo a la mano durante todo el taller, especialmente en la Sesión 4.

---

## El Protocolo del Equipo (resumen ejecutivo)

Si solo vas a recordar una cosa de este documento, que sea esto:

**Nadie hace `merge` ni `push` directo a `main`. Nunca, ni local ni en la nube.**

El flujo correcto, siempre:

```
1. git checkout main  →  git pull            (parte de lo más reciente)
2. git checkout -b tu-tarea                  (crea tu rama)
3. trabaja, haz commits en tu rama
4. git push origin tu-tarea                  (sube TU RAMA, no main)
5. abre un Pull Request en GitHub
6. un compañero (no tú) lo revisa y aprueba
7. se fusiona desde GitHub — con el botón, no desde ninguna terminal
8. vuelve al paso 1 antes de tu siguiente tarea
```

No hay un "líder" con permiso especial de hacer merge — la revisión es entre compañeros, rotando, igual que el espíritu auto-organizado de un equipo Scrum.

---

## Bloque 1 — Fundamentos: ¿dónde vive mi trabajo?

**¿Necesito internet para hacer un commit?**
No. `git commit` es una operación 100% local. Internet (GitHub) solo entra cuando quieres respaldar o compartir tu trabajo con `git push`.

**Cerré la terminal / apagué mi computadora. ¿Perdí mi trabajo?**
No. La terminal es solo la ventana desde la que escribes comandos — no es donde vive nada. Tu trabajo está guardado en archivos reales dentro de la carpeta oculta `.git` de tu proyecto, de forma permanente, igual que cualquier otro archivo en tu computadora. Vuelve a la carpeta (`cd ruta/a/tu/carpeta`) y sigue donde ibas.

**¿Cuál es la diferencia entre Git y GitHub, otra vez?**
Git es el programa de control de versiones — vive en tu computadora, funciona sin internet. GitHub es una plataforma web que aloja repositorios de Git y agrega colaboración (Pull Requests, revisión de código). Puedes usar Git toda tu vida sin tocar GitHub jamás.

---

## Bloque 2 — Ramas y colaboración en equipo

**¿Debo hacer `merge` en mi computadora antes de subir mi trabajo?**
No — ver el Protocolo del Equipo arriba. Subes tu rama tal cual; la fusión a `main` ocurre en GitHub, vía Pull Request, después de revisión.

**¿Cuándo uso Fork y cuándo uso Branch?**
- **Branch**: cuando tienes permiso de escritura directa sobre el repositorio — el caso normal de tu equipo de clase.
- **Fork**: cuando no tienes permiso de escritura — copias el repositorio a tu propia cuenta para proponer cambios desde ahí (típico de proyectos open source ajenos, no de tu equipo).

**Necesito lo que un compañero ya subió, antes de terminar mi propia tarea. ¿Qué hago?**
Trae los cambios de `main` hacia tu rama (no es lo mismo que fusionar tu rama a main — es al revés):
```bash
git checkout tu-rama
git merge main
```

**¿Qué tan grande debe ser un Pull Request?**
Pequeño y frecuente. Un PR de 500 líneas casi nadie lo revisa con cuidado real; uno de 20-50 líneas sí. Como beneficio adicional, PRs chicos y frecuentes te dan más historial real para la trazabilidad de autoría del equipo.

**¿Qué hago con mi rama después de que se fusiona?**
Bórrala — en GitHub (botón "Delete branch" que aparece justo después del merge) y localmente:
```bash
git branch -d tu-rama
```
Evita que el repositorio se llene de ramas viejas conforme avanza el semestre.

**`main` o `master` — ¿cuál es el correcto?**
Ambos existen en la práctica; `main` es el default moderno. Configura esto una sola vez para que todos tus repositorios nuevos usen `main`:
```bash
git config --global init.defaultBranch main
```

---

## Bloque 3 — Conflictos y errores comunes

**Me sale "Permission denied" al crear una carpeta.**
Casi siempre es que estás parado en una carpeta protegida del sistema, no en la tuya. Revisa con `pwd`, y muévete a tu carpeta de usuario con `cd ~` antes de reintentar.

**Me sale un warning sobre "LF will be replaced by CRLF".**
No es un error — es Git avisando que va a normalizar los finales de línea entre Windows y Unix. Resuélvelo de raíz una vez por repositorio:
```bash
echo "* text=auto" > .gitattributes
git add .gitattributes && git commit -m "Agrega .gitattributes"
```

**Creo que inicialicé un repositorio por accidente en el lugar equivocado (por ejemplo, en `~`).**
Revisa con `cd ~` y `ls -la` (la bandera `-a` es indispensable — sin ella, no ves carpetas ocultas como `.git`, exista o no). Si aparece un `.git` donde no debería, bórralo con `rm -rf ~/.git` — no borra tus archivos, solo el registro de Git. Ver la sección "Solución de problemas" de la Guía de Laboratorio para el procedimiento completo de reinicio.

**Tengo un conflicto de fusión. ¿Qué hago, y de quién es la responsabilidad?**
La responsabilidad de resolverlo es de quien abre el Pull Request **más tarde** (el "segundo en llegar" a modificar esa línea) — no de quien lo revisa, y no es un castigo ni una señal de que alguien hizo algo mal. Git te va a marcar el conflicto directamente en el archivo:
```
<<<<<<< HEAD
tu versión
=======
la versión de tu compañero
>>>>>>> nombre-de-la-rama
```
Decides manualmente qué se queda (o combinas ambas), borras las marcas `<<<<<<<` `=======` `>>>>>>>`, y haces un commit normal para cerrarlo.

**Hice `git push` y me sale `! [rejected] main -> main (fetch first)`. ¿Qué hice mal?**
Probablemente nada "mal" — lo más común es que el repositorio remoto avanzó por un camino que tu copia local no conoce. Esto pasa seguido cuando alguien sube o edita archivos directamente en la interfaz web de GitHub (incluyendo arrastrar y soltar) y después, desde otra computadora, alguien más intenta subir cambios locales sin haber traído primero esa actualización. **Importante: subir por arrastrar y soltar en la web SÍ crea un commit real** — tan válido como uno hecho por terminal — solo que tu Git local no se entera automáticamente. La solución es la que sugiere el propio mensaje de error:
```bash
git pull
git push
```
`git pull` trae lo que le falta a tu copia local (puede pedirte confirmar un mensaje de "merge commit" — acéptalo tal cual). Después, tu `push` ya se acepta sin problema. Lección de fondo: si tu equipo va a usar más de un método para subir archivos (terminal y web), usa uno solo por consistencia — mezclar ambos sin sincronizar entre medio es la causa más común de este rechazo.

---

## Bloque 4 — Preguntas de "por qué así y no de otra forma"

**¿Por qué no simplemente todos trabajamos directo en `main`?**
Porque `main` debe representar siempre una versión estable y funcional. Si todos escriben ahí directamente, un error de cualquiera rompe el trabajo de todos, todo el tiempo. Las ramas existen exactamente para evitar eso.

**¿Por qué pasar por un Pull Request en vez de fusionar directo?**
Dos razones: (1) fuerza a que al menos otra persona del equipo entienda cada parte del código, no solo quien lo escribió — esto es, de hecho, la mejor defensa contra que "solo uno o dos programen y los demás ayuden"; (2) crea un registro permanente de qué se revisó, cuándo, y quién lo aprobó.

**¿Por qué la Retrospectiva y el Daily Scrum no viven dentro de ninguna herramienta (ni Git ni Jira)?**
Porque son conversaciones humanas de coordinación y mejora — las herramientas registran QUÉ se hizo, no reemplazan la conversación de CÓMO se está trabajando como equipo. Confundir "reunión registrada en una app" con "reunión que realmente pasó" es un error común en equipos que apenas empiezan con metodologías ágiles.

---

*¿No encuentras tu pregunta aquí? Probablemente la respuesta paso a paso está en el Curso Relámpago, la Guía de Laboratorio, o las Tarjetas de Tarea de la Sesión 4 — este documento es el complemento conceptual, no el sustituto.*
