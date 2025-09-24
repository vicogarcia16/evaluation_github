# 🤖 Evaluación de Respuestas de IA con Azure OpenAI y GitHub Actions 🚀

Este proyecto utiliza GitHub Actions para automatizar la evaluación de un modelo de lenguaje de Azure OpenAI. El proceso lee una serie de preguntas (prompts), genera una respuesta para cada una y luego las evalúa en función de métricas de calidad como la coherencia y la fluidez.

## ⚙️ ¿Cómo funciona?

El corazón de este proyecto es el workflow de GitHub Actions definido en `.github/workflows/evaluate.yml`. Este workflow se ejecuta automáticamente con cada `push` a la rama `main` y sigue estos pasos:

1.  ▶️ **Disparo Automático**: El workflow se inicia automáticamente cada vez que se sube un commit a la rama `main`.

2.  🛠️ **Configuración del Entorno**: Se configura el entorno de ejecución, instalando Python y las dependencias necesarias (`openai`, `pydantic`).

3.  ✍️ **Generación de Respuestas**: Un script de Python utiliza las credenciales de Azure OpenAI para:
    *   Leer las preguntas del archivo `data/eval-input.jsonl`.
    *   Para cada pregunta, invocar al modelo de chat de Azure OpenAI para generar una respuesta.
    *   Guardar las preguntas junto con sus respuestas generadas en un nuevo archivo: `data/eval-input-generated.jsonl`.

4.  📊 **Evaluación de Calidad**: Se utiliza la action `microsoft/genai-evals` para evaluar las respuestas generadas. Esta action:
    *   Utiliza un modelo de IA (el "juez") para evaluar la calidad de las respuestas del modelo de chat.
    *   Las métricas de evaluación utilizadas son `coherence` (coherencia) y `fluency` (fluidez).
    *   La configuración de la evaluación se define dinámicamente en el archivo `evaluate-config.json`.

5.  📦 **Publicación de Resultados**: Los resultados de la evaluación se guardan en un archivo `results.jsonl`, que se sube como un artefacto de la ejecución del workflow. Este artefacto se puede descargar desde la página de resumen de la ejecución.

## 🚀 Uso

Para utilizar este proyecto, sigue estos pasos:

### 🔑 1. Configurar los Secretos

Este workflow requiere que configures los siguientes secretos en tu repositorio de GitHub (`Settings` > `Secrets and variables` > `Actions`):

*   `OIDC_AZURE_CLIENT_ID`: El ID de cliente de una identidad de Azure con permisos para autenticarse.
*   `OIDC_AZURE_TENANT_ID`: El ID del tenant de Azure.
*   `OIDC_AZURE_SUBSCRIPTION_ID`: El ID de la suscripción de Azure.
*   `AZURE_OPENAI_ENDPOINT`: La URL del endpoint de tu servicio Azure OpenAI.
*   `AZURE_OPENAI_API_KEY`: La clave de API para acceder al servicio.
*   `AZURE_OPENAI_API_VERSION`: La versión de la API de Azure OpenAI (ej. `2024-02-01`).
*   `AZURE_OPENAI_CHAT_DEPLOYMENT`: El nombre del despliegue de tu modelo de chat que quieres evaluar.

### 📝 2. Modificar los Datos de Entrada

Puedes añadir tus propias preguntas de evaluación en el archivo `data/eval-input.jsonl`. El archivo debe ser de tipo [JSON Lines](https://jsonlines.org/), donde cada línea es un objeto JSON con, como mínimo, una clave `"query"`:

```json
{"query": "¿Cuál es la capital de Francia?"}
{"query": "¿Quién desarrolló la teoría de la relatividad?"}
```

### ▶️ 3. Funcionamiento Automático

Simplemente haz un `git push` a la rama `main` después de tus commits. El workflow se ejecutará de forma automática. Puedes ver el progreso y los resultados en la pestaña **Actions** de tu repositorio.

### 📄 4. Revisar los Resultados

Una vez que la ejecución del workflow haya finalizado, ve a la página de resumen de la ejecución. En la sección de **Artifacts**, encontrarás un archivo llamado `evaluation-results` que puedes descargar. Este archivo contiene los resultados detallados de la evaluación en formato JSON Lines.