# Proyecto: Mi primer agente personal con inteligencia artificial local

**Modalidad:** trabajo cooperativo por grupos  
**Contexto:** proyecto de iniciación a IA local y agentes personales, independiente de otros contenidos.  
**Producto final:** prototipo funcional de agente personal, encapsulado en contenedores Docker, que utiliza un modelo de lenguaje ejecutado localmente, interactúa mediante Telegram y consulta servicios externos de prueba.  
**Presupuesto obligatorio:** 0 €.

## 1. Reto

Investigar las soluciones actuales de agentes personales (*personal agents*), comparar modelos de lenguaje que se ejecutan localmente y construir un prototipo propio a partir de herramientas gratuitas, ejecutado íntegramente en contenedores Docker. El agente deberá comunicarse por Telegram, consultar Gmail y Google Calendar mediante cuentas creadas exclusivamente para este proyecto y demostrar un uso responsable y seguro de las herramientas externas.

No se exige desarrollar una aplicación con interfaz propia, programar un agente desde cero ni implementar pruebas automatizadas. El aprendizaje se centra en comprender, instalar, configurar, experimentar, integrar, documentar y explicar las tecnologías utilizadas. No obstante, si ninguna plataforma resulta viable en el hardware o la red disponibles, se admitirá un script propio y sencillo que conecte el motor de inferencia con Telegram y las herramientas (por ejemplo, en Python), ejecutado también en un contenedor, siempre que cumpla las mismas condiciones de seguridad y se justifique la decisión.

## 2. Objetivos de aprendizaje

Al finalizar, el alumnado deberá ser capaz de:

1. Distinguir un modelo LLM, un motor de inferencia, una plataforma de agentes y una herramienta externa.
2. Explicar tokens, ventana de contexto, inferencia, cuantización, temperatura, *top-p*, *top-k* y límite de tokens de salida.
3. Instalar y utilizar modelos de IA locales, eligiendo el más adecuado según el hardware disponible; comparar sus resultados y limitaciones.
4. Investigar y evaluar soluciones actuales de agentes personales con criterios técnicos y de seguridad.
5. Configurar una plataforma de agentes conectada a un modelo local y a Telegram.
6. Comprender el protocolo MCP y utilizarlo en una integración sencilla cuando sea compatible con la solución elegida.
7. Integrar servicios externos de prueba, en particular Gmail y Google Calendar, mediante MCP, conectores nativos o APIs gratuitas.
8. Identificar riesgos de privacidad, exposición de credenciales, instrucciones maliciosas y ejecución de acciones no autorizadas.
9. Encapsular el prototipo en contenedores Docker orquestados con Docker Compose y mantener un repositorio colaborativo que permita reproducirlo en distintos equipos.

## 3. Condiciones generales

- **Sin coste:** no se admitirán suscripciones, APIs de modelos de pago, contratación de infraestructura, tarjetas bancarias ni servicios que exijan añadir métodos de pago. Deben revisarse las cuotas y condiciones gratuitas de cada integración, así como las condiciones de licencia de las herramientas utilizadas (incluido Docker).
- **IA local:** la inferencia principal del modelo LLM se realizará en un equipo local, por ejemplo mediante Ollama ejecutado en un contenedor. Telegram y Google son servicios externos: el sistema no es completamente desconectado.
- **Contenedores:** todo el prototipo —motor de inferencia, plataforma del agente y herramientas— se ejecutará en contenedores Docker y se levantará con un único fichero `docker-compose.yml`. Durante la exploración de las Fases 2 y 3 pueden probarse otras herramientas de escritorio, pero la solución final debe funcionar en contenedores. Los pesos de los modelos se guardarán en volúmenes de Docker, nunca dentro de las imágenes ni en el repositorio.
- **Hardware y entornos:** el proyecto debe poder completarse íntegramente con los ordenadores Windows del aula, sin GPU dedicada. No se establecen requisitos mínimos de RAM o CPU, no se penalizará la lentitud y no se exigirá adquirir hardware. El prototipo podrá desarrollarse y demostrarse en el aula o en casa: si un grupo dispone legítimamente de un equipo doméstico más potente, puede usarlo, incluso para la demostración final. Parte del reto es descubrir el mejor modelo que cada grupo puede ejecutar con el hardware del que realmente dispone. La aceleración por GPU dentro de contenedores requiere configuración adicional, que el grupo documentará si la utiliza.

  Durante la Fase 1 se comprobará que la solución elegida funciona en contenedores en los equipos del aula y con la red del centro. En Windows, Docker se ejecuta sobre WSL2, que limita por defecto la memoria disponible para los contenedores; este límite condiciona qué modelos pueden cargarse y debe documentarse. Algunos servicios (Telegram, autenticación de Google, descarga de imágenes y modelos) pueden verse afectados por el filtrado de red. Los bloqueos detectados se documentarán y se comunicarán al profesor cuanto antes.
- **Colaboración:** cada grupo tendrá un repositorio Git remoto compartido. No se subirán modelos descargados, secretos ni datos privados.
- **Elección tecnológica:** cada grupo justificará el modelo, el motor de inferencia y la plataforma de agentes. OpenClaw, OpenCode u otras soluciones son opciones, no requisitos obligatorios.
- **Alcance:** se comenzará con un agente único. La arquitectura multiagente es una ampliación opcional.

## 4. Conceptos y arquitectura

**Modelo local (LLM):** interpreta instrucciones, redacta respuestas y puede proponer llamadas a herramientas. Ejemplos a investigar: familias Qwen, Gemma o Llama, verificando versión, licencia y compatibilidad con herramientas.

**Motor de inferencia:** carga y ejecuta el modelo en el ordenador. Ejemplos: Ollama y LM Studio. Para el prototipo final debe poder ejecutarse en un contenedor.

**Plataforma u orquestador de agentes:** mantiene la conversación, ofrece herramientas al modelo, ejecuta las llamadas autorizadas y conecta los canales de comunicación. Ejemplos a investigar: OpenClaw, OpenCode y otras soluciones apropiadas.

**MCP (Model Context Protocol):** protocolo que permite a clientes compatibles descubrir e invocar herramientas ofrecidas por servidores MCP. MCP no sustituye al modelo ni al orquestador, y no es obligatorio para conectar Telegram.

**Contenedores (Docker y Docker Compose):** cada componente se ejecuta en su propio contenedor, con sus dependencias aisladas del sistema. Docker Compose define en un solo fichero qué contenedores se levantan, cómo se comunican entre sí, qué volúmenes usan y qué variables de entorno reciben. Además de facilitar la reproducción en distintos equipos, el aislamiento limita lo que el agente puede tocar del sistema anfitrión.

Flujo orientativo:

```text
Usuario en Telegram
        |
        v
+------------------ Docker Compose (equipo local) ------------------+
|                                                                   |
|   Plataforma del agente / orquestador                             |
|          |                            |                           |
|          v                            v                           |
|   Motor de inferencia local     Herramientas autorizadas          |
|   + modelo LLM (volumen)        (MCP / conectores / APIs)         |
|                                       |                           |
+---------------------------------------|---------------------------+
                                        v
                            Gmail / Calendar / otros
                          (servicios externos de prueba)

La respuesta vuelve al usuario por Telegram.
```

El modelo **propone** acciones; el software que lo rodea controla su ejecución. La compatibilidad real con modelos locales, MCP, Telegram y la ejecución en contenedores deberá verificarse para cada plataforma.

## 5. Fases de trabajo

### Fase 1. Investigación y comparación de agentes personales

Investigar al menos **tres soluciones actuales**, de las cuales **al menos dos** deben ser agentes personales propiamente dichos, y distinguir entre asistente conversacional, agente personal, agente de programación, framework de agentes y plataforma de automatización. Pueden considerarse OpenClaw, OpenCode, n8n, Flowise, Open WebUI y otras, aclarando que no todas pertenecen a la misma categoría.

Comparar: propósito, arquitectura, licencia, gratuidad real, ejecución local, compatibilidad con modelos locales, Telegram, MCP, Gmail y Calendar, disponibilidad de imágenes Docker oficiales o facilidad para contenerizarla, seguridad, permisos, documentación y mantenimiento.

**Entregables:** tabla comparativa con fuentes, explicación de las diferencias y elección razonada de la solución que intentarán utilizar. La gratuidad efectiva es un requisito indispensable de la solución elegida.

### Fase 2. Instalación y comparación de modelos locales

Instalar un motor de inferencia gratuito y ejecutar al menos **dos modelos o configuraciones de modelo**. Aplicar un conjunto común de instrucciones y comparar calidad, consistencia, velocidad percibida, consumo de recursos y capacidad de *tool calling*, si está disponible.

La prueba de *tool calling* es especialmente relevante: un modelo que conversa bien puede no ser capaz de invocar herramientas de forma fiable, y esa capacidad condiciona las Fases 4 y 5. Se recomienda incluir en el conjunto común de instrucciones al menos una petición que requiera usar una herramienta sencilla. Si ningún modelo ejecutable en el hardware disponible lo hace de forma fiable, se documentará como resultado de la investigación, y se acordará con el profesor cómo continuar.

**Entregables:** identificación exacta de modelos y cuantizaciones, instrucciones de instalación, tabla de observaciones y selección justificada en función del hardware disponible por el grupo (aula o equipo doméstico). Un modelo grande o rápido no es necesariamente la mejor opción: lo importante es que esté bien elegido para el hardware con el que se ha trabajado.

### Fase 3. Experimentación con parámetros

Investigar y experimentar, cuando el motor lo permita, con:

| Parámetro | Actividad sugerida |
|---|---|
| Temperatura | Repetir una instrucción con valores bajos y altos y comparar variabilidad. |
| Top-p | Modificar el umbral de muestreo y observar posibles diferencias. |
| Top-k | Comparar respuestas al restringir el número de candidatos. |
| Ventana de contexto | Comprobar el valor **realmente configurado** en el motor (no el máximo teórico del modelo), realizar una conversación larga y observar límites y pérdida de información. Algunos motores usan por defecto un contexto reducido y recortan la conversación sin avisar, lo que afecta especialmente a los agentes. |
| Cuantización | Comparar variantes del mismo modelo, si están disponibles, en consumo y calidad. |
| Máximo de tokens de salida | Comprobar cómo afecta el límite a respuestas largas. |

Mantener constantes las demás variables en la medida de lo posible. Los resultados pueden ser variables y algunos parámetros no estarán expuestos por todas las herramientas.

**Entregable:** guía breve de parámetros con definiciones comprensibles, configuración utilizada, ejemplos propios y conclusiones.

### Fase 4. Construcción del agente personal

Elegir y configurar una plataforma u orquestador gratuito, conectarlo al **modelo que se ejecuta localmente** y configurar instrucciones y herramientas básicas. Integrar Telegram mediante la Bot API o un conector compatible, preferentemente con *long polling* para evitar infraestructura pública. Motor de inferencia y plataforma se definirán como servicios en el `docker-compose.yml` del grupo.

**Entregable:** demostración de una conversación por Telegram atendida por el modelo local, fichero `docker-compose.yml` que levante el prototipo y documentación de los componentes. No se exige programar una interfaz ni desarrollar un agente desde cero.

### Fase 5. Conexión con herramientas y servicios externos

Estudiar la función de MCP y conectar, si la plataforma lo permite, una herramienta MCP sencilla. Integrar **Gmail y Google Calendar de prueba**, utilizando MCP, conectores nativos o APIs gratuitas según la compatibilidad real. Aplicar autenticación y permisos mínimos. Los servidores MCP o servicios auxiliares que se añadan se ejecutarán también como contenedores del mismo `docker-compose.yml`.

Ejemplos de tareas: consultar próximos eventos, localizar un correo ficticio por asunto, relacionar un correo con una cita y **proponer** un nuevo evento antes de solicitar confirmación. Las operaciones de escritura requieren confirmación explícita y controles efectivos de autorización.

**Ampliaciones voluntarias:** RSS, meteorología con API gratuita, archivos ficticios, SQLite, repositorios de prueba, gestores de tareas o simuladores domésticos. Se admitirán otras conexiones justificadas siempre que sean gratuitas, seguras, aisladas y autorizadas. Se **considerará la originalidad o innovación** de la ampliación voluntaria.

**Entregable:** demostración de Telegram y de las integraciones con Gmail y Calendar, indicando si se utiliza MCP o un conector nativo. Si alguna integración resulta técnicamente imposible bajo las restricciones del centro, se documentará el bloqueo y se acordará con el profesor una alternativa gratuita de alcance equivalente.



### Fase 6. Seguridad, privacidad y revisión final

Analizar qué datos recibe el modelo, qué sale del equipo, qué herramientas existen, qué permisos tienen, qué ve cada contenedor del sistema anfitrión, dónde se guardan los secretos y cómo se impiden acciones no autorizadas. Comprobar con datos ficticios un caso de *prompt injection* (por ejemplo, un correo que intenta ordenar al agente que divulgue información) sin permitir acciones peligrosas. La prueba deberá mostrar que la acción no se ejecuta aunque el modelo llegue a proponerla.

Al finalizar el proyecto, cada grupo realizará un cierre seguro: revocar el token del bot de Telegram, revocar las autorizaciones OAuth y eliminar el proyecto de Google Cloud si ya no se necesita, borrar los secretos y los volúmenes con datos de todos los equipos utilizados (aula y domésticos) y decidir si la cuenta de prueba se elimina o se conserva. El cierre quedará registrado en `seguridad/riesgos-y-medidas.md`, sin incluir ningún secreto.

**Entregables:** análisis de riesgos, medidas aplicadas, demostración final y explicación individual de las decisiones del grupo.

## 6. Seguridad: condición esencial y no negociable

La seguridad es un **requisito obligatorio en todas las fases**, no un añadido opcional. Un prototipo funcional que exponga secretos o ponga en riesgo cuentas y datos reales no se considerará correctamente realizado.

1. **Cuenta de Google exclusiva:** cada grupo creará una cuenta nueva para el proyecto, destinada a Gmail y Calendar, con correos, contactos y eventos exclusivamente ficticios. No se utilizarán cuentas personales habituales ni cuentas del centro. Se respetarán los requisitos de edad, registro y verificación del proveedor; si no es viable crear la cuenta, el profesor determinará una alternativa segura.
2. **No reutilizar credenciales:** nunca utilizar contraseñas, tokens, claves o cuentas de servicios personales, educativos, laborales o domésticos que ya se estén usando.
3. **Sin tarjetas ni pagos:** no introducir tarjetas de crédito o débito, datos bancarios ni métodos de pago; no vincular cuentas financieras.
4. **Secretos fuera de Git y de las imágenes:** no subir contraseñas, tokens de Telegram, credenciales OAuth, tokens de acceso o actualización, archivos `.env` reales ni historiales privados. Emplear plantillas sin secretos y reglas `.gitignore`. Los secretos se pasarán a los contenedores en tiempo de ejecución (fichero `.env` local o *secrets* de Docker Compose), nunca copiados dentro de una imagen ni escritos en un `Dockerfile`.
5. **Equipos compartidos:** los ordenadores del aula los utilizan otros grupos y otras personas. Los secretos se guardarán únicamente en el perfil de usuario de quien trabaja, nunca en carpetas compartidas ni en el escritorio común, y se eliminarán al terminar cada sesión si el equipo no es de uso exclusivo. Al finalizar el proyecto se revocarán el token del bot de Telegram y las credenciales OAuth.
6. **Permisos mínimos:** autorizar solo las operaciones necesarias; preferir lectura inicialmente y solicitar confirmación para enviar, crear, modificar o borrar. **La confirmación debe imponerla la plataforma** (aprobación manual de herramientas, listas de herramientas permitidas o herramientas de solo lectura), no únicamente una instrucción en el prompt del modelo: una instrucción puede ignorarse o ser anulada mediante *prompt injection*.
7. **Acceso limitado:** restringir el bot a usuarios o chats autorizados y evitar que herramientas del agente tengan acceso general al sistema de archivos, a la terminal o a servicios reales. Los contenedores solo montarán los volúmenes imprescindibles, nunca el disco completo del equipo ni el socket de Docker, y los puertos que se publiquen quedarán limitados a `127.0.0.1`.
8. **Servidores MCP, *skills*, plugins y conectores:** revisar procedencia, mantenimiento, permisos y tratamiento de datos antes de instalarlos. Esto se aplica también a las extensiones de repositorios comunitarios de las propias plataformas de agentes, donde se han detectado complementos maliciosos, y a las imágenes Docker de terceros: preferir imágenes oficiales y fijar sus versiones. No instalar componentes desconocidos ni con privilegios excesivos.
9. **Datos ficticios:** no cargar información privada, documentos sensibles, datos de terceros ni información del centro.
10. **Registro seguro:** documentar acciones y errores sin publicar secretos ni datos personales.
11. **Incidentes:** si se filtra un secreto, revocarlo y sustituirlo; si una integración es insegura, desactivarla antes de la demostración.

La autenticación OAuth de Google, las cuotas y los requisitos de verificación pueden limitar determinadas integraciones. En particular, las aplicaciones de Google Cloud en modo de prueba emiten autorizaciones que caducan periódicamente (actualmente, a los pocos días), por lo que **la autorización deberá renovarse antes de la demostración final** y el procedimiento quedará documentado en `integraciones/gmail.md` y `integraciones/calendar.md`. No se deben sortear restricciones de seguridad ni contratar servicios para completarlas.

## 7. Trabajo en grupo y repositorio

Cada grupo mantendrá un repositorio Git remoto, preferentemente privado y con acceso controlado, para compartir documentos, configuraciones reproducibles, prompts, instrucciones y resultados. Podrán utilizarse equipos del aula y equipos domésticos, cada uno con su propia instalación local de Docker y sus propias credenciales.

Estructura orientativa:

```text
personal-agent/
├── README.md
├── .gitignore
├── .env.example
├── docker-compose.yml
├── docker/
│   └── (Dockerfiles propios, si se necesitan)
├── investigacion/
│   ├── comparativa-agentes.md
│   └── decisiones.md
├── modelos/
│   ├── comparativa.md
│   └── parametros.md
├── configuracion/
│   ├── agente.example.json
│   └── herramientas.example.json
├── integraciones/
│   ├── telegram.md
│   ├── gmail.md
│   └── calendar.md
├── seguridad/
│   └── riesgos-y-medidas.md
└── demo/
    └── (vídeos cortos de respaldo, opcional; véase el apartado 8.4)
```

Los archivos exactos dependerán de la plataforma. **No** se versionarán pesos de modelos, volúmenes de Docker, credenciales, bases de datos con contenido privado ni información personal.

El repositorio debe permitir que otra persona del grupo reconstruya el prototipo siguiendo el `README.md`: clonar, crear su `.env` a partir de `.env.example`, ejecutar `docker compose up` y descargar el modelo indicado. Si se comparte un bot de Telegram entre equipos, evitar ejecutar simultáneamente varias instancias de *long polling* con el mismo token.

## 8. Entrega final

La entrega se realiza a través del repositorio Git remoto compartido del grupo, con el que se trabajará de forma profesional durante todo el proyecto, no solo al final.

### 8.1 Organización del trabajo: tablero Kanban

- Cada grupo creará un tablero en Trello (plan gratuito) con, al menos, tres columnas: **TO DO**, **DOING** y **DONE**.
- Cada tarea será una tarjeta con su responsable asignado. El tablero debe reflejar el reparto real de tareas entre los integrantes y su evolución a lo largo del proyecto, no reconstruirse al final.
- El tablero se compartirá con el profesor en modo de solo lectura, utilizando el usuario de Trello que él facilitará.

### 8.2 Uso profesional del repositorio

- **Todos los integrantes** realizarán commits propios y regulares desde su propia cuenta. El historial debe mostrar la contribución de cada miembro: no es válido un repositorio en el que una sola persona sube el trabajo del resto.
- Commits pequeños y coherentes, con mensajes descriptivos que expliquen qué cambia y por qué, en lugar de grandes subidas al final del proyecto.
- Trabajo en ramas por tarea e integración en la rama principal mediante *pull requests*, preferentemente revisadas por otro integrante. La rama principal debe mantenerse siempre en un estado funcional.
- Cuando sea posible, se relacionarán ramas o *pull requests* con su tarjeta de Trello.
- Las reglas de seguridad del apartado 6 se aplican a todo el historial: si un secreto llega a subirse, borrarlo en un commit posterior no basta, porque sigue en el historial; debe revocarse y sustituirse.

### 8.3 Versión entregada: etiqueta y *release*

- Al terminar, el grupo creará una etiqueta anotada (por ejemplo, `v1.0.0`) sobre el commit que entrega y publicará una *release* en GitHub a partir de ella.
- La descripción de la *release* incluirá: componentes y versiones utilizados (modelo, cuantización, motor de inferencia, plataforma), instrucciones resumidas de puesta en marcha, limitaciones conocidas y enlaces a la documentación del repositorio.
- La versión entregada es la de la *release*; los commits posteriores no forman parte de la entrega.
- Si el repositorio es privado, el profesor deberá tener acceso a él.

### 8.4 Grabaciones de respaldo (opcional)

El grupo puede grabar vídeos cortos que muestren el funcionamiento del prototipo, en especial la petición combinada de la demostración (apartado 9). Antes de subirlos se revisará que solo aparecen datos ficticios y que no se ven tokens, ficheros `.env`, códigos de autorización ni ninguna otra credencial.

GitHub impone estos límites (consultar la documentación oficial, ya que pueden cambiar):

| Vía de subida | Límite |
|---|---|
| Archivo en el repositorio (`git push`) | Aviso a partir de 50 MiB; bloqueo a partir de 100 MiB por archivo. |
| Archivo subido desde la web de GitHub | Máximo 25 MiB por archivo. |
| Tamaño total del repositorio | Recomendado por debajo de 1 GB; muy recomendado por debajo de 5 GB. |
| Archivo adjunto a una *release* | Sin límite total por *release*; cada archivo, hasta el tamaño máximo de Git LFS del plan (2 GB en el plan gratuito). No cuenta para el tamaño del repositorio. |

Pautas:

- Clips cortos, de uno a tres minutos, en MP4 comprimido. Si se versionan en el repositorio, cada uno debe quedar por debajo de 25 MiB y guardarse en la carpeta `demo/`.
- Los vídeos de mayor tamaño se adjuntarán a la *release*, no al repositorio.
- Un vídeo versionado permanece en el historial aunque se borre después, así que solo se subirá su versión definitiva.
- No se utilizará Git LFS: su cuota gratuita es limitada y ampliarla requiere pago.

## 9. Demostración final

Cada grupo presentará, en la medida en que las herramientas elegidas lo permitan:

1. El prototipo levantado con Docker Compose y el modelo local en funcionamiento con su configuración.
2. Una comparación de modelos y un experimento sobre parámetros.
3. Una conversación con el agente desde Telegram.
4. Una consulta a eventos ficticios de Google Calendar.
5. Una búsqueda de correos ficticios en Gmail.
6. Una petición combinada: «Busca el correo sobre la reunión del proyecto, comprueba cuándo es y si tengo algún compromiso a esa hora».
7. Una operación propuesta que requiera confirmación, sin ejecutarse automáticamente.
8. Una explicación de las medidas de seguridad y de los límites del sistema.
9. La innovación que hayan implementado.

Dado que en equipos sin GPU algunas peticiones pueden tardar varios minutos, el grupo podrá preparar una grabación propia de la petición combinada (punto 6) como respaldo, siguiendo las pautas del apartado 8.4. La grabación complementa la demostración en vivo, no la sustituye: el resto de puntos se mostrarán en directo y, si el tiempo lo permite, también la petición combinada.

La demostración debe mostrar comprensión y justificación técnica de las decisiones tomadas, más allá de la velocidad de respuesta del sistema. Se incluirá una breve defensa individual para comprobar la participación y el aprendizaje de cada miembro del grupo.

## 10. Criterios de éxito

El proyecto tendrá éxito cuando el grupo pueda explicar la diferencia entre modelo y agente, demostrar la ejecución local de un LLM, justificar los parámetros y herramientas elegidos, interactuar mediante Telegram, consultar servicios de prueba, identificar riesgos de seguridad, reproducir el entorno desde su repositorio con Docker Compose y mostrar, mediante el tablero y el historial de commits, un trabajo repartido entre todos sus integrantes.

La finalidad no es crear un producto comercial ni un sistema autónomo permanente: es **aprender cómo se construye y controla un agente personal basado en IA local mediante experimentación práctica, gratuita y segura**.

Los grupos que tengan éxito en todo lo pedido en el proyecto, tendrán un **0,5 puntos** de más en la nota final. De todos los proyectos presentados los alumnos votarán por el proyecto con la **innovación o ampliación voluntaria que mas les haya gustado**, el equipo con más votaciones recibirá un **0,75 puntos** en la nota final. El equipo ganador será aquel que más voto
