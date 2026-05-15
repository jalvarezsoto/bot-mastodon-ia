# Bot de moderación para Mastodon con IA

Este parte del proyecto implementa un bot de moderación automática para Mastodon. Su objetivo es escuchar nuevas publicaciones, preparar su contenido para análisis y usar un modelo de IA mediante Ollama para detectar posibles incumplimientos de las normas de la comunidad.

El bot trabaja en **modo estricto**, por lo que ante una infracción clara o razonable tiende a marcar la publicación como infractora y reportarla para revisión por moderación humana.

---

## Funcionamiento general

El bot se conecta a una instancia de Mastodon usando las credenciales definidas en variables de entorno. Una vez iniciado, permanece escuchando el flujo de publicaciones. Cuando detecta una publicación nueva, extrae su contenido textual, procesa posibles imágenes adjuntas y envía esa información a un modelo de IA configurado mediante Ollama.

La IA analiza el contenido siguiendo un conjunto de normas de comunidad definidas en el propio código. Si el análisis devuelve que existe una posible violación, el bot genera un reporte en Mastodon. Si no se detecta infracción, no realiza ninguna acción y continúa escuchando nuevas publicaciones.

El sistema no elimina publicaciones directamente: únicamente reporta contenido sospechoso para que pueda ser revisado por moderación.

---

## Variables de configuración

El bot necesita varias variables de entorno para funcionar correctamente. Normalmente se definen en un fichero `.env` ubicado en la raíz del proyecto.

| Variable | Descripción |
|---|---|
| `MASTODON_URL` | URL de la instancia de Mastodon a la que se conecta el bot. |
| `ACCESS_TOKEN` | Token de acceso usado para autenticar el bot en Mastodon. |
| `OLLAMA_HOST` | Dirección del servidor Ollama que ejecuta el modelo de IA. |
| `MODELO_IA` | Nombre del modelo de IA que se usará para analizar las publicaciones. |

Si alguna de estas variables no está definida, el programa detiene la ejecución lanzando un error.

---

## Normas de comunidad analizadas

El bot utiliza una lista de normas internas para decidir si una publicación debe ser reportada. Entre las categorías contempladas se incluyen:

- Discriminación u odio.
- Contenido sexual o desnudos.
- Violencia, gore o sangre.
- Spam, publicidad o estafas.
- Desinformación dañina.
- Doxxing, acoso, insultos o lenguaje hostil.
- Infracciones claras de derechos de autor.

Estas normas se incluyen dentro del prompt que recibe la IA, junto con el contenido de la publicación.

---

## Flujo de funcionamiento

```mermaid
flowchart TD
    A([Inicio del bot]) --> B[Se cargan las variables de entorno]
    B --> C{¿Configuración completa?}

    C -- No --> D[Se detiene la ejecución con error]
    C -- Sí --> E[Se configura el cliente de IA con Ollama]

    E --> F[Se configura el sistema de logs]
    F --> G[Se inicia la conexión con Mastodon]
    G --> H[El bot empieza a escuchar publicaciones]

    H --> I[Se recibe una nueva publicación]
    I --> J[Se limpia el HTML y se obtiene texto plano]
    J --> K{¿Tiene imágenes adjuntas?}

    K -- Sí --> L[Se descargan y convierten las imágenes a Base64]
    K -- No --> M[Se continúa solo con el texto]

    L --> N[Se prepara el contenido para la IA]
    M --> N

    N --> O[La IA analiza texto e imágenes]
    O --> P[La respuesta se extrae como JSON]
    P --> Q[Se normaliza el resultado]

    Q --> R{¿Existe posible infracción?}
    R -- No --> S[No se realiza ninguna acción]
    R -- Sí --> T[Se reporta la publicación en Mastodon]

    S --> U[El bot sigue escuchando]
    T --> U
    U --> H
