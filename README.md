# Boxing Punch Machine Dashboard

Dashboard local para registrar y comparar los puntajes de una máquina de golpes entre amigos. Está pensado para ejecutarse como un archivo HTML en un solo equipo; no necesita servidor ni base de datos.

## Inicio rápido

1. Abre `punch_machine_arcade_dashboard.html` en un navegador.
2. En **Máquinas**, agrega cada equipo una vez con un nombre/lugar y su máximo de pantalla (999 o 9999).
3. Usa **+ Amigo / Boxeador** para agregar participantes.
4. Usa **Registrar Golpe** y selecciona la máquina para guardar fecha, mano, intento y puntaje original.
5. Exporta periódicamente la base de datos JSON desde **Respaldos**.

La aplicación inicia sin participantes ni golpes de demostración. La fecha del formulario se establece con la fecha local del equipo. El número de intento comienza en 1 y aumenta después de cada registro; se puede ajustar manualmente antes de guardar.

## Estandarización de puntajes entre máquinas

Cada golpe se asocia a una máquina del catálogo, identificada por **nombre/lugar** (por ejemplo, “Mall Vivo, fuera del gym”) y máximo de pantalla (**999** o **9999**). El campo de puntaje acepta 0 hasta el máximo elegido y conserva el valor bruto.

El filtro **Máquina** controla las comparaciones del dashboard:

- **Todas · comparar %:** cada golpe se compara como `puntaje original / máximo de esa máquina × 100`. Por ejemplo, 800/999 ≈ **80,1%** y 8000/9999 ≈ **80,0%**. Rankings, podio, trofeos, gráfico y tendencias usan ese porcentaje para que la diferencia de escala no domine la comparación.
- **Una máquina seleccionada:** las estadísticas usan y muestran los puntos originales de ese equipo.

La vista global permite un ranking total aunque sea una comparación imperfecta. El porcentaje solo ajusta la escala máxima; **no corrige diferencias de calibración, sensibilidad, estado o algoritmo** entre máquinas. Para una comparación más justa y una tendencia personal confiable, usen el mismo equipo y condiciones similares.

En **Todas · comparar %**, el ranking combinado suma los mejores porcentajes de ambas manos y puede llegar a 200%. Al filtrar una máquina concreta, suma sus puntajes brutos.

## Datos y almacenamiento

Cada golpe tiene esta estructura:

```json
{
  "id": "p_...",
  "date": "AAAA-MM-DD",
  "player": "Nombre del participante",
  "hand": "Derecha",
  "attempt": 1,
  "score": 752,
  "machineId": "machine_..."
}
```

El catálogo de máquinas exportado incluye `id`, `name` y `maxScore`. Golpes, participantes y máquinas se guardan en `localStorage` del navegador y equipo actuales. No se sincronizan entre navegadores ni la aplicación envía los registros a un servidor; los recursos externos descritos abajo sí se solicitan a sus proveedores. Exportar JSON crea un respaldo completo; importar restaura los tres conjuntos. Los respaldos anteriores sin máquinas se asignan a **“Máquina anterior sin identificar”**, con máximo 999, para conservar los registros y permitir revisarlos. **Borrar todos los datos** elimina participantes, golpes y máquinas locales.

Ejemplo de entrada del catálogo:

```json
{
  "id": "machine_...",
  "name": "Mall Vivo, fuera del gym",
  "maxScore": 9999
}
```

## Estadísticas disponibles

- **Podio diario:** campeón por mayor puntaje o porcentaje del máximo de la fecha elegida, mejor golpe derecho y mejor golpe izquierdo.
- **Ranking:** día seleccionado o histórico; filtros por máquina, mano derecha, izquierda o combinado. En vista global, compara porcentajes del máximo; por equipo, puntajes brutos.
- **Ficha individual:** máximos por mano, máquina y escala del récord, cantidad de golpes, promedio normalizado o bruto e historial borrable.
- **Balance de manos:** `min(mejor derecha, mejor izquierda) / max(mejor derecha, mejor izquierda) × 100`. Se muestra solo cuando hay puntajes de ambas manos; el resultado va de 0% a 100%.
- **Gráfico comparativo:** mejor golpe por mano y participante, en porcentaje global o puntos de la máquina filtrada.
- **El “Ahora sí”:** diferencia promedio entre los intentos derechos 1 y 2, emparejados por participante, fecha y máquina; muestra cuántos mejoraron, bajaron o empataron.
- **El Promedio Miente:** media y mediana de todos los golpes, además de cuánto se separan.
- **Asamblea de Brazos:** brecha promedio entre el mejor golpe de cada mano de participantes con ambas manos registradas; indica quién tuvo la brecha menor.
- **Índice de Temblor:** desviación estándar de los puntajes; describe dispersión, pero no progreso.
- **La Curva del Saco:** promedio por fecha separado por mano. En vista global usa porcentaje del máximo; con una máquina muestra puntaje bruto. Con una sola fecha indica que todavía no existe tendencia.
- **Récord histórico:** mayor golpe registrado y mejor balance.
- **Trofeos:** golpe más bajo, golpe más alto, mayor diferencia entre manos, mayor diferencia entre el golpe más alto y el más bajo de una persona, mejor simetría y mayor cantidad de intentos. El balance se calcula solo cuando hay registros de ambas manos; el trofeo de inconsistencia requiere al menos dos golpes.

## Ejemplo histórico con seudónimos ficticios

Antes de limpiar el dashboard, se verificó el conjunto original correspondiente al 30-10-2026. Estos valores se documentan como referencia histórica; no se cargan automáticamente. Los nombres siguientes son seudónimos ficticios inspirados en la estética de dibujos animados de los años 80 y no identifican a los participantes originales:

- 5 participantes y 15 golpes: 10 derechos y 5 izquierdos.
- Suma de puntajes: **7.690**; promedio redondeado: **513 puntos**.
- Campeón, mejor derecho, mejor izquierdo y récord histórico: **Garra Galáctica**, con 752 puntos a la derecha y 435 a la izquierda.
- Mano de Pluma: **Tigre Cósmico**, con 273 puntos.
- Mayor brecha entre manos: **Capitán Meteoro**, con 401 puntos (677 derecha y 276 izquierda).
- Mayor diferencia entre intentos: **Capitán Meteoro**, con 401 puntos.
- Mejor simetría: **Robot Rayo**, con 71% (429 / 605).
- En intentos, todos registraron 3; el desempate del listado de Donante VIP usa el puntaje acumulado.
- En el análisis de revancha, el primer intento derecho promedió **604** y el segundo **564** (**−40 puntos**); 2 participantes mejoraron y 3 bajaron.
- La brecha media entre mejores manos fue de **295 puntos**. La media global (**513**) superó la mediana (**472**) por **41 puntos**.
- La desviación estándar de los 15 puntajes fue aproximadamente **144 puntos**.

Estas métricas usan los datos ingresados y son recreativas, no inferencias sobre fuerza o condición física. La vista global normaliza por máximo, pero no elimina sesgos del equipo. Para tendencias temporales confiables se necesitan varias fechas por participante y la misma máquina. Los pares de revancha comparan solo intentos derechos 1 y 2 de la misma persona, fecha y máquina.

## Ideas para futuras estadísticas

- **El “¿Subí o me creo Rocky?”:** variación porcentual respecto de la sesión anterior, separada por mano y máquina.
- **La Media que Entrena:** promedio móvil de los últimos 3 o 5 intentos por participante y mano, para distinguir progreso sostenido de un golpe excepcional.
- **El Récord que te Mira Feo:** cuánto falta para superar el récord personal reciente de cada mano, en la misma máquina.
- **El Calendario del Saco:** sesiones asistidas, días consecutivos jugando y evolución del número de intentos por sesión.

Para interpretar tendencias conviene registrar varias sesiones por participante y mano en el mismo equipo. Un promedio móvil reduce el efecto de un único puntaje extremo.

## Dependencias

El HTML carga Tailwind CSS, Chart.js y fuentes de Google mediante CDN. Se requiere conexión a internet para cargar esos recursos. Chart.js y Tailwind tienen versión fijada e integridad SRI; las fuentes de Google no están incluidas localmente.
