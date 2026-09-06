# n8n-learning

**Proyecto Integrador - Automatización con IA**

**//Descripción del proyecto**
Este repositorio contiene el desarrollo progresivo de mi proyecto integrador del curso de Automatización con IA.

El proyecto está basado en la empresa ficticia Soluciones Globales Tech y evoluciona módulo a módulo sobre una misma arquitectura.

En M1 desarrollé un agente comercial base.
En M2 lo transformé en una arquitectura multi-agente con un Manager y Workers especializados.
En M3 incorporé memoria persistente y recuperación de contexto por sesión.
En M4 conecté el sistema con herramientas externas para gestionar correos comerciales, memoria, contactos y notificaciones internas.

La idea del proyecto es conservar lo construido en cada etapa y ampliar progresivamente sus capacidades hasta llegar al Proyecto Final.

**M1 - Agente Base y Motor de Razonamiento**
En el primer módulo desarrollé la base del asistente comercial.

El workflow incluye:
- un trigger de entrada;
- un agente con rol de Asesor Comercial Senior;
- un System Prompt con objetivos y restricciones;
- un límite de 5 iteraciones;
- una herramienta que simula la consulta de productos, stock y precios;
- una salida estructurada para facilitar la observabilidad.

Este workflow representa la primera versión funcional del proyecto.

**M2 - Orquestación Multi-Agente Distribuida**
En M2 separé las responsabilidades del sistema.

La arquitectura quedó formada por:
- Manager: interpreta la intención de la consulta y decide qué especialista debe procesarla.
- Worker de Inventario: gestiona solicitudes relacionadas con productos, stock, disponibilidad y precios.
- Worker de Calificación de Leads: analiza oportunidades comerciales.
- Fallback: deriva las solicitudes que no pueden clasificarse con seguridad.

El Manager clasifica cada petición dentro de una taxonomía cerrada:
- INVENTORY
- LEAD_QUALIFICATION
- FALLBACK

Los Workers funcionan como sub-workflows independientes y devuelven al Manager una respuesta estructurada.

Esta separación permite mantener responsabilidades claras y evitar que un único agente concentre toda la lógica del sistema.

**M3 - Memoria Persistente y Resumen Automático**
En M3 incorporé una capa de memoria persistente sobre la arquitectura multi-agente creada en M2.

El objetivo fue permitir que el sistema pudiera recuperar información de conversaciones anteriores sin mezclar datos entre usuarios.

Identificación de sesiones

Cada conversación utiliza un identificador único:

Session_ID

La memoria se consulta utilizando este valor antes de ejecutar el Manager.

Si existe información previa, el sistema recupera:

nombre del usuario;
resumen consolidado;
estado del caso;
datos clave;
contador de intercambios.

Si la sesión no existe, el workflow crea un nuevo registro inicial.

Memoria persistente

La tabla Memoria_Sesiones contiene:

Campo	Función
Session_ID	Identificador único de la conversación
User_Name	Nombre conocido del usuario
Fecha_Actualizacion	Fecha de la última escritura
Resumen_Consolidado	Memoria semántica resumida
Estado_Caso	Estado comercial
Datos_Clave	Información útil para próximas interacciones
Contador_Intercambios	Control del ciclo de resumen

El Session_ID actúa como clave de aislamiento para evitar que una conversación recupere información perteneciente a otro usuario.

Inyección de contexto

La memoria recuperada se incorpora al System Prompt dentro de delimitadores explícitos:

[INICIO DE CONTEXTO COMPARTIDO]
...
[FIN DEL CONTEXTO COMPARTIDO]

El contenido recuperado se interpreta exclusivamente como contexto histórico y no como instrucciones para el agente.

Resumen automático

Cuando una conversación supera los cinco intercambios, el workflow activa una rama específica de Summarization.

El resumen generado mantiene esta estructura:

{
  "asunto_principal": "string",
  "puntos_clave": ["string"],
  "accion_requerida": "string"
}

El resultado reemplaza la memoria anterior de la sesión y el contador vuelve a comenzar.

No se guardan transcripciones completas, objetos binarios, payloads HTTP ni logs técnicos.

**M4 - Integraciones con Ecosistemas de Negocio**
En M4 amplié el sistema conectándolo con herramientas externas utilizadas para simular un entorno comercial real.

La arquitectura integra:
- una casilla de correo para recibir consultas comerciales;
- Airtable como CRM simulado;
- la memoria persistente desarrollada en M3;
- Gmail para generar respuestas en formato borrador;
- Telegram como canal interno de notificación para operaciones.

El objetivo principal de este módulo fue incorporar integraciones reales manteniendo controles de seguridad, limpieza de datos y supervisión humana.

**Entrada mediante correo electrónico**
El workflow principal comienza cuando se recibe un nuevo correo.
A partir de M4, el correo del remitente normalizado en minúsculas funciona también como identificador de sesión.
De esta forma, diferentes mensajes enviados desde la misma dirección recuperan la misma memoria persistente.

**Control de correos automáticos**
Inmediatamente después del trigger se ejecuta un filtro determinista.

El workflow descarta correos relacionados con:
- no-reply
- noreply
- auto-reply
- automatic reply
- out of office
- undeliverable
- security alert
- alerta de seguridad

Si alguna de estas condiciones se cumple, la ejecución se detiene.
Esto evita que el sistema procese respuestas automáticas y reduce el riesgo de generar bucles de correo.

**Clasificación inicial por asunto**
Los correos que superan el primer filtro pasan por una segunda condición.
Para ingresar al circuito comercial, el asunto debe contener:

VENTAS

Ejemplo válido:

VENTAS - Consulta Producto A

Los mensajes que no cumplen esta condición se detienen antes de utilizar agentes o herramientas externas.

**Limpieza del payload**
Antes de enviar información a los agentes o integraciones externas, el workflow utiliza un nodo de limpieza.

Se conservan únicamente los campos necesarios:
- from_email
- user_name
- session_id
- subject
- snippet
- message_id
- thread_id
- source

El sistema no propaga:
- HTML completo
- attachments
- binary data
- headers completos
- payload bruto del correo

El contenido utilizado por el Manager corresponde principalmente al snippet del mensaje.
Esta reducción evita transferir información innecesaria entre nodos y simplifica el procesamiento.

**Memoria de M3 reutilizada en M4**
Después de limpiar el correo, el workflow consulta la tabla Memoria_Sesiones.

El identificador utilizado ahora es:
- Session_ID = correo normalizado del remitente

Si el usuario ya existe, se recupera su contexto anterior.
Si no existe, se crea una nueva sesión.

Después continúa la arquitectura desarrollada en M3:
Memoria
↓
Manager
↓
Switch
├── Worker Inventario
├── Worker Calificación de Leads
└── Fallback
↓
Persistencia de memoria
↓
Summarization cuando corresponde

**CRM simulado**
Para M4 incorporé una segunda tabla independiente: Contactos_CRM
La memoria conversacional y el CRM permanecen separados.

La tabla CRM contiene:
- Campo |	Función
- Email |	Identificador principal del contacto
- Nombre |	Nombre del cliente
- Fecha_Creacion |	Primer contacto registrado
- Fecha_Ultimo_Contacto |	Última interacción
- Estado_Lead	| Estado comercial
- Ultimo_Asunto	| Último asunto recibido
- Ultimo_Snippet |	Fragmento del último correo
- Ultima_Ruta |	Ruta seleccionada por el Manager
- Origen	| Canal de entrada
- Ultimo_Message_ID |	Identificador para trazabilidad

**Control de contactos duplicados**
Antes de crear un nuevo contacto, el workflow siempre realiza una búsqueda por correo.

La lógica es:

Buscar contacto por Email
        ↓
¿Existe?
 ├── Sí → Update
 └── No → Create

El sistema nunca ejecuta directamente una creación sin realizar primero la búsqueda.
Esto evita duplicar contactos cuando un mismo cliente envía múltiples correos.

**Human-in-the-loop**
Las respuestas generadas por el sistema no se envían automáticamente.
Después del procesamiento se crea únicamente un borrador de correo.

La secuencia es:

Consulta recibida
↓
Agente procesa
↓
Se genera respuesta
↓
Create Draft
↓
Revisión humana
↓
La persona decide si envía el correo

De esta forma, la IA puede asistir en la redacción sin tener autorización para enviar mensajes directamente al cliente.

**Tratamiento de FALLBACK**
Cuando el Manager clasifica una solicitud como: FALLBACK
el workflow no utiliza una respuesta improvisada por el agente.

Se genera un borrador seguro indicando que la solicitud fue recibida y necesita revisión humana.

Ejemplo conceptual:

Gracias por contactarnos.

Recibimos correctamente tu solicitud.

En este caso necesitamos que una persona de nuestro equipo la revise antes de brindarte una respuesta definitiva.

Tu consulta fue registrada y un integrante del equipo se comunicará contigo a la brevedad.

Además, el caso se marca para intervención humana en el canal interno de operaciones.

**Canal interno de operaciones**
Para las notificaciones internas utilizo Telegram como canal equivalente de mensajería operativa.
Antes de enviar información al canal, otro nodo de limpieza conserva únicamente:

customer_email
customer_name
subject
route
status
summary
requires_human

El resumen también se limita para evitar mensajes excesivamente largos.

No se envían:

correos completos
HTML
attachments
memoria completa
objetos binarios
headers
payloads técnicos



------------------------------------------------------------------------------------------------------------------------

**Estructura de los workflows**

//workflows/
- M1/
-- checkpoint1_noelia_rausch.json
- M2/
-- manager_noelia_rausch.json
-- worker_inventario_noelia_rausch.json
-- worker_calificacion_lead_noelia_rausch.json
- M3/
-- manager_memoria_noelia_rausch.json
-- worker_inventario_noelia_rausch.json
-- worker_calificacion_lead_noelia_rausch.json
- M4/
-- checkpoint4_noelia_rausch.json
-- worker_inventario_noelia_rausch.json
-- worker_calificacion_lead_noelia_rausch.json


La documentación correspondiente a cada checkpoint se conserva separada para poder revisar la evolución del proyecto.

**¿Cómo importar los workflows?**
- Descargar los archivos .json correspondientes al módulo.
- Abrir una instancia de n8n.
- Importar cada workflow desde archivo.
- Configurar las credenciales propias en los nodos que las requieran.
- En M2, importar tanto el Manager como los dos Workers.
- Verificar que los nodos que ejecutan los sub-workflows estén asociados a los Workers importados.
- Ejecutar pruebas desde el workflow Manager.

Importante: Las credenciales utilizadas durante el desarrollo no forman parte de este repositorio.

Después de importar los workflows, cada usuario debe configurar sus propias conexiones y credenciales antes de realizar una ejecución.

Los archivos exportados contienen únicamente la definición necesaria de los workflows. Las conexiones externas deben configurarse nuevamente en la instancia donde se importen.


**🗺️ Evolución del proyecto**

// Módulo |	Implementación | Estado
- M1 | Agente base + System Prompt + herramienta + observabilidad	| ✅ Completado
- M2 | Manager + Workers + enrutamiento multi-agente | ✅ Completado
- M3 | Memoria persistente + Session ID + Summarization | ✅ Completado
- M4 | Correo + CRM + borradores HITL + canal interno + controles preventivos | ✅ Completado
- M5 | Recuperación de información documental | ⏳ Próximo
- M6 | Entrada y salida por voz | ⏳ Próximo
- M7-M11 | Evolución progresiva hasta el Proyecto Final Integrador | ⏳ Pendiente

La intención es continuar utilizando esta misma arquitectura y sumar en cada módulo solamente los componentes necesarios para la nueva funcionalidad.

----------------------------------------------------------------------------------------------------------------------------

**Autor**

Noelia Rausch
Curso: Automatización Avanzado con IA - Coderhouse

Este README se actualizará en cada checkpoint para documentar la evolución del proyecto.

