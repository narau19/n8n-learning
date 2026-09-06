# n8n-learning

**Proyecto Integrador - Automatización con IA**

//Descripción del proyecto

Este repositorio contiene el desarrollo progresivo de mi proyecto integrador del curso de Automatización con IA.

El proyecto comenzó en M1 con un agente comercial base para la empresa ficticia Soluciones Globales Tech. En M2 la arquitectura evolucionó hacia un sistema multi-agente formado por un Manager y dos Workers especializados. En M3 incorporé una capa de memoria persistente para que el sistema pueda recuperar contexto entre diferentes ejecuciones sin mezclar información entre usuarios.

La idea es mantener un único proyecto e ir ampliándolo en cada checkpoint, conservando lo desarrollado anteriormente y sumando solamente las nuevas funcionalidades solicitadas.

**M1 - Agente Base y Motor de Razonamiento**
En el primer módulo desarrollé la base del asistente comercial.

El workflow incluye:
- un trigger de entrada;
- un agente con rol de Asesor Comercial Senior;
- un System Prompt con objetivos y restricciones;
- un límite de 5 iteraciones;
- una herramienta que simula la consulta de productos, stock y precios;
- una salida estructurada para facilitar la observabilidad.

Este flujo quedó como primera versión funcional del proyecto.

**M2 - Orquestación Multi-Agente Distribuida**
En M2 separé las responsabilidades del sistema.

La arquitectura está formada por:
- Manager: interpreta la intención de la consulta.
- Worker de Inventario: procesa solicitudes relacionadas con productos, stock, disponibilidad y precios.
- Worker de Calificación de Leads: procesa oportunidades comerciales.
- Fallback: gestiona solicitudes que no pueden asignarse con seguridad a un especialista.

El Manager clasifica cada petición dentro de una taxonomía cerrada:
- INVENTORY
- LEAD_QUALIFICATION
- FALLBACK

Los Workers funcionan como sub-workflows independientes y devuelven un contrato estructurado al flujo principal.

**M3 - Memoria Persistente y Resumen Automático**

En M3 incorporé una capa de memoria híbrida sobre la arquitectura multi-agente existente.

El objetivo principal fue evitar que el agente pierda completamente el contexto al finalizar una ejecución y, al mismo tiempo, impedir que la información correspondiente a usuarios diferentes pueda mezclarse.

**Entrada estructurada**
El workflow principal recibe las peticiones mediante un Webhook.

Cada solicitud incluye:

{
  "session_id": "session_noelia_001",
  "user_name": "Noelia",
  "message": "Mensaje enviado por el usuario"
}

El session_id es utilizado durante toda la ejecución como identificador de la conversación.

**Lectura de memoria**
Antes de ejecutar el Manager, el workflow consulta la base de memoria utilizando el Session_ID recibido.

Si existe una sesión previa, recupera:
- nombre del usuario;
- resumen consolidado;
- estado del caso;
- datos clave;
- contador de intercambios.

Si no existe, el flujo crea un nuevo registro inicial.

**Aislamiento por sesión**
Todas las consultas y actualizaciones de memoria se realizan utilizando el identificador de sesión correspondiente.

De esta forma, una conversación solamente puede recuperar el contexto asociado a su propio Session_ID.

**Inyección de contexto**
La memoria recuperada se incorpora de forma pasiva al System Prompt del Manager dentro de delimitadores explícitos:

[INICIO DE CONTEXTO COMPARTIDO]
...
[FIN DEL CONTEXTO COMPARTIDO]

El prompt también indica que el contenido histórico debe interpretarse como datos de referencia y no como instrucciones.

**Resumen automático**

El workflow mantiene un contador de intercambios para cada sesión.

Cuando una conversación supera los cinco intercambios se ejecuta una rama específica de Summarization. El modelo encargado de este proceso genera exclusivamente un objeto JSON con la siguiente estructura:

{
  "asunto_principal": "string",
  "puntos_clave": ["string"],
  "accion_requerida": "string"
}

Este resultado reemplaza el resumen anterior de la misma sesión.

No se almacenan transcripciones completas, payloads HTTP ni logs técnicos en la base de memoria.

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
- M2 | Manager + Workers como sub-workflows + enrutamiento | ✅ Completado
- M3 | Memoria persistente por Session ID | ✅ Completado
- M4 | Integraciones externas mediante autenticación | ⏳ Próximo
- M5 | Base documental y recuperación de contexto | ⏳ Próximo
- M6 | Entrada y salida por voz | ⏳ Próximo
- M7-M11 | Evolución progresiva hasta el Proyecto Final Integrador | ⏳ Pendiente

La intención es continuar utilizando esta misma arquitectura y sumar en cada módulo solamente los componentes necesarios para la nueva funcionalidad.

----------------------------------------------------------------------------------------------------------------------------

**Autor**

Noelia Rausch
Curso: Automatización Avanzado con IA - Coderhouse

Este README se actualizará en cada checkpoint para documentar la evolución del proyecto.

