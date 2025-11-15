# GitHub Desktop: Limpiar ramas "Current Branch"

Si GitHub Desktop muestra varias ramas como "Current branch" simultáneamente, normalmente se debe a ramas locales sin sincronizar o a repositorios clonados en diferentes carpetas. Para normalizar la vista:

1. **Confirma la rama activa**
   - Abre una terminal en la carpeta del proyecto y ejecuta `git status`.
   - El encabezado `On branch ...` indica cuál es tu rama real.

2. **Actualiza el listado de ramas en GitHub Desktop**
   - En GitHub Desktop ve a **Branch → Refresh list**. Esto vuelve a consultar las ramas remotas y debería limpiar duplicados temporales.

3. **Elimina ramas locales obsoletas**
   - Desde la terminal puedes borrar ramas que ya no necesitas con `git branch -d nombre-rama` (o `-D` para forzar).
   - Vuelve a GitHub Desktop y presiona **Fetch origin**.

4. **Reclona si es necesario**
   - Si el problema continúa, haz un respaldo de tus cambios, elimina la carpeta y vuelve a clonar el repositorio desde GitHub Desktop. Luego aplica tus cambios manualmente o con `git cherry-pick` desde el respaldo.

5. **Evita múltiples carpetas del mismo repo**
   - Verifica que no tengas varias copias del repositorio abiertas en GitHub Desktop, porque cada una puede registrar su propia rama “current”. Cierra las instancias duplicadas desde **File → Close repository**.

6. **Sincroniza cambios pendientes**
   - Realiza `Commit` de los cambios locales, y luego usa **Push origin** para alinearlos con el remoto. GitHub Desktop mostrará una única rama activa una vez que todo esté sincronizado.

Estos pasos suelen resolver los escenarios en los que GitHub Desktop mantiene varias entradas en la lista de ramas actuales.

## Consolidar el trabajo en una única rama

Si cada actualización en Codex o en tu editor termina generando ramas nuevas, seguí este flujo para reagrupar todo en la rama principal (por ejemplo `work` o `main`):

1. **Identificá la rama que querés conservar**
   - En la terminal ejecutá `git branch` y anotá el nombre de la rama donde vive el trabajo más reciente (supongamos `work`).

2. **Actualizá la rama base**
   - Cambiá a la rama destino con `git switch work` (sustituí `work` por el nombre correcto).
   - Traé los últimos cambios remotos sin mezclar automáticamente: `git fetch origin` y luego `git pull --ff-only`.
   - Si `--ff-only` falla, usá `git pull --rebase` para aplicar tus commits encima del remoto evitando merges extras.

3. **Integrá commits sueltos de otras ramas**
   - Para cada rama temporal creada por Codex o GitHub Desktop, ejecutá `git switch nombre-rama` y luego `git log` para confirmar que los commits sean los que necesitás.
   - Volvé a la rama principal (`git switch work`) y traé esos commits con `git merge nombre-rama --ff-only` si es posible, o `git cherry-pick <hash>` si solo querés uno específico.

4. **Eliminá las ramas residuales**
   - Una vez fusionadas, borrá las ramas locales con `git branch -d nombre-rama`.
   - Eliminá las ramas remotas que ya no se usarán con `git push origin --delete nombre-rama` para que tampoco aparezcan en GitHub Desktop.

5. **Publicá todo desde la rama única**
   - Verificá el historial limpio con `git log --oneline --graph --decorate --all`.
   - En GitHub Desktop asegurate de tener seleccionada la rama deseada en el selector de la parte superior, escribe tu mensaje de commit y presioná **Push origin**.
   - Las siguientes actualizaciones se harán sobre esa misma rama, evitando la creación de ramas automáticas.

Con este flujo tendrás un repositorio ordenado, sin merges innecesarios y con un solo branch activo tanto en tu máquina como en la nube.

## Mantener los cambios de Codex en `main`

Si querés que absolutamente todo viva en `main` y evitar que GitHub Desktop genere ramas nuevas al sincronizar lo que hiciste en Codex:

1. **Elegí `main` antes de pegar los archivos**
   - Abrí GitHub Desktop, seleccioná el repositorio y asegurate de que el selector superior diga `main`.
   - Si aparece otra rama activa, hacé clic y cambiá a `main` antes de reemplazar archivos o copiar contenido desde Codex.

2. **Pega los cambios directamente sobre `main`**
   - Descargá el zip o copia los archivos que te entrega Codex sobre tu carpeta de trabajo local.
   - Git detectará las diferencias como modificaciones de la rama actual (`main`).

3. **Commit + Push en el mismo flujo**
   - Revisa los cambios en la pestaña **Changes**, escribe un resumen y pulsa **Commit to main**.
   - Luego usa **Push origin** para subirlos; esto dispara el deploy de Cloudflare si Pages está escuchando `main`.

4. **Elimina ramas accidentales**
   - Si al sincronizar se creó una rama `codex/<algo>`, cámbiate a ella, ejecuta `git log` para anotar el último hash y vuelve a `main`.
   - Usa `git cherry-pick <hash>` para traer cualquier commit faltante a `main`, confirmá que todo esté actualizado y borra la rama con `git branch -D codex/<algo>` y `git push origin --delete codex/<algo>`.

5. **Desactiva la creación de branches automáticos**
   - En GitHub Desktop, ve a **File → Options → Git** y marca "Fetch only the current branch" para que Desktop no genere ramas de seguimiento extra cuando se detecten cambios remotos.

6. **Verificá la rama de producción en Cloudflare Pages**
   - En el panel del proyecto, pestaña **Settings → Build & deployments**, confirma que la *Production branch* sea `main`.
   - Si cambiaste la rama por error, vuelve a `main` y dispara un nuevo despliegue con **Retry deployment**.

Así cada actualización que recibas de Codex quedará en la misma rama y la URL pública siempre mostrará la última versión.

## Pasar todos los cambios actuales a `main`

Si ya venías trabajando en una rama auxiliar (por ejemplo `work`) y querés dejar todo consolidado en `main` sin mantener ramas extras, podés hacerlo desde la terminal con estos pasos:

1. **Asegurate de tener la rama auxiliar al día**
   - `git switch work` (reemplazá `work` por la rama que concentra tus cambios).
   - `git status` para confirmar que no haya archivos sin commitear. Si los hay, creá un commit antes de continuar.

2. **Creá/actualizá `main` con el contenido de `work`**
   - Ejecutá `git switch main` si la rama ya existe. Si Git avisa que no existe, corré `git branch -M work main` para renombrar `work` a `main` directamente.
   - En caso de que `main` exista pero esté desactualizada, estando en `main` corré `git merge work --ff-only`. Si la opción `--ff-only` falla porque los historiales divergen, usá `git merge work` normal y resolvé los conflictos siguiendo las instrucciones de GitHub Desktop.

3. **Sincronizá con el remoto**
   - Desde la rama `main`, publicá los commits con `git push origin main`.
   - Si renombraste la rama, es posible que debas ejecutar `git push origin --set-upstream main` la primera vez.

4. **Borrá la rama auxiliar (opcional)**
   - Una vez que confirmes que `main` tiene todo, podés borrar la rama vieja con `git branch -d work` y `git push origin --delete work`.
   - GitHub Desktop mostrará únicamente `main` como rama activa y Cloudflare Pages desplegará desde ahí si la tenés configurada como rama de producción.

Este flujo te deja con un único branch (`main`) que contiene la versión más reciente del ecommerce y simplifica los futuros deployments.
