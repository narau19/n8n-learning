# n8n-learning

**Proyecto Integrador - Automatización con IA**

//Descripción del proyecto

Este repositorio contiene el desarrollo progresivo de mi proyecto integrador del curso de Automatización con IA.

El proyecto comenzó en el Módulo 1 con un agente comercial base para la empresa ficticia Soluciones Globales Tech. A partir del Módulo 2, la arquitectura evoluciona hacia un sistema multi-agente compuesto por un workflow Manager y dos Workers especializados.

La idea es mantener un solo proyecto e ir ampliándolo en cada checkpoint, conservando lo realizado en los módulos anteriores y sumando las nuevas funcionalidades solicitadas.

**M1 - Agente Base y Motor de Razonamiento**
En el primer módulo desarrollé la base del asistente comercial.

El workflow de M1 está compuesto por:
- Chat Trigger: recibe los mensajes ingresados por el usuario.
- AI Agent: actúa como Asesor Comercial Senior de Soluciones Globales Tech.
- System Prompt: define su rol, objetivos, límites y forma de respuesta.
- Guardrail de iteraciones: se estableció un máximo de 5 iteraciones para evitar ejecuciones indefinidas.
- Herramienta de inventario: simula una consulta a un sistema interno con información sobre productos, stock y precios.
- Observabilidad: la salida final queda estructurada para facilitar el seguimiento de la ejecución.

Este workflow constituye la primera versión funcional del proyecto y se conserva como referencia de la evolución posterior.

**M2 - Orquestación Multi-Agente Distribuida**
En el segundo módulo amplié la arquitectura separando las responsabilidades del agente principal.

En lugar de utilizar un único agente para resolver todas las solicitudes, ahora existe un Manager que analiza la intención del mensaje y decide qué especialista debe procesarlo.

La arquitectura de M2 está formada por tres workflows independientes:
1. Manager: Es el punto de entrada del sistema.
Recibe la consulta del usuario, identifica su intención y la clasifica dentro de una taxonomía cerrada:
- INVENTORY
- LEAD_QUALIFICATION
- FALLBACK

Una vez identificada la categoría, un nodo de enrutamiento dirige la ejecución al Worker correspondiente.

El Manager no realiza directamente el trabajo de los especialistas. Su responsabilidad principal es decidir a quién delegar la tarea.

2. Worker de Inventario:
Procesa las solicitudes relacionadas con:
- stock;
- disponibilidad;
- productos;
- precios;
- cotizaciones preliminares.

Este Worker trabaja solamente con la información de inventario disponible y devuelve una respuesta estructurada al Manager.

3. Worker de Calificación de Leads: Procesa las solicitudes relacionadas con oportunidades comerciales.
Analiza información como intención de compra, necesidad, presupuesto y urgencia para realizar una calificación preliminar del prospecto.

Las posibles clasificaciones son:
- ALTO
- MEDIO
- BAJO
- INFORMACION_INSUFICIENTE

Si una solicitud no corresponde claramente a ninguno de los dos especialistas, el Manager utiliza la ruta FALLBACK para indicar que el caso requiere revisión humana.

**Comunicación entre Manager y Workers**
Antes de ejecutar un Worker, el Manager reduce la información enviada a un conjunto mínimo de datos.

El contrato de entrada utilizado entre workflows contiene:

{
  "request": "Mensaje del usuario",
  "session_id": "Identificador de la conversación",
  "route": "Ruta seleccionada"
}

Cada Worker procesa esa solicitud y devuelve una respuesta estructurada con el estado de la ejecución, el especialista utilizado y el resultado obtenido.

Ejemplo:

{
  "status": "success",
  "worker": "inventory_worker",
  "route": "INVENTORY",
  "request": "¿Hay stock del Producto A?",
  "session_id": "session_id",
  "result": "Resultado generado por el Worker"
}

Esto permite mantener una interfaz predecible entre el Manager y los distintos especialistas.

------------------------------------------------------------------------------------------------------------------------

**Estructura de los workflows**

//workflows/
- M1/
-- checkpoint1_noelia_rausch.json
- M2/
-- manager_noelia_rausch.json
-- worker_inventario_noelia_rausch.json
-- worker_calificacion_lead_noelia_rausch.json

Los Workers de M2 funcionan como sub-workflows independientes y son llamados por el Manager solamente cuando la intención detectada corresponde con su especialidad.

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
- M3 | Memoria persistente por Session ID | ⏳ Próximo
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

