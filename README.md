# Bot Mastodon con IA

Bot de moderación automática para una instancia de Mastodon.

Este proyecto forma parte de nuestro proyecto final relacionado sobre redes sociales de código abierto y descentralizadas. Después de probar diferentes alternativas, se creó una instancia de Mastodon y se desarrolló este bot para ayudar en la moderación de publicaciones mediante inteligencia artificial local.

El bot escucha las publicaciones nuevas de la instancia, analiza texto e imágenes usando un modelo local mediante Ollama y, si detecta una posible infracción de las normas de la comunidad, reporta automáticamente la publicación para que un moderador humano tome la decisión final.

---

## Objetivo del proyecto

El objetivo principal del bot es reducir la carga de trabajo de moderación en una instancia Mastodon.

El bot no elimina publicaciones ni aplica sanciones directamente. Su función es detectar contenido sospechoso y generar un reporte dentro de Mastodon.

La decisión final siempre corresponde a una persona moderadora.

---

## Funcionamiento general

El flujo del bot es el siguiente:

```text
Publicacion nueva en Mastodon
        |
        v
Bot recibe el evento desde el stream local
        |
        v
Extrae texto e imagenes
        |
        v
Envia el contenido a Ollama
        |
        v
La IA responde en formato JSON
        |
        v
Si hay infraccion -> reporta la publicacion
Si no hay infraccion -> registra el post como correcto
