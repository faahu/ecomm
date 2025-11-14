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
