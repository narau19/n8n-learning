# Proyecto Integrador - Automatización con IA

## Descripción

Este repositorio contiene el desarrollo progresivo de mi proyecto integrador del curso de Automatización Avanzada con IA.

El proyecto está basado en la empresa ficticia **Soluciones Globales Tech** y evoluciona módulo a módulo sobre una misma arquitectura, incorporando nuevas capacidades sin reemplazar las implementaciones anteriores.

La solución parte de un agente comercial básico y evoluciona hacia una arquitectura multi-agente con memoria persistente, integraciones de negocio, recuperación documental, voz, evaluación comercial, análisis de riesgo, auditoría y controles Human-in-the-Loop.

---

## M1 - Agente Base

En el primer módulo desarrollé la base del asistente comercial.

Se incorporaron:

* agente con rol comercial definido;
* System Prompt con objetivos y restricciones;
* límite de iteraciones;
* herramienta simulada para consultar productos, stock y precios;
* salida estructurada para facilitar pruebas y observabilidad.

Este workflow representa la primera versión funcional del proyecto.

---

## M2 - Arquitectura Multi-Agente

En M2 separé responsabilidades mediante una arquitectura formada por un **Manager** y Workers especializados.

El Manager clasifica las solicitudes dentro de una taxonomía cerrada y deriva cada caso hacia:

* Inventario;
* Calificación de Leads;
* Fallback.

Los Workers funcionan como sub-workflows independientes y devuelven resultados estructurados al workflow principal.

---

## M3 - Memoria Persistente

En M3 incorporé persistencia de contexto entre conversaciones.

Cada sesión utiliza un `Session_ID` como identificador único para recuperar:

* resumen de la conversación;
* estado del caso;
* datos relevantes;
* contador de intercambios.

También se incorporó un mecanismo de **Summarization** que consolida la memoria después de determinados intercambios, evitando almacenar conversaciones completas.

---

## M4 - Integraciones de Negocio

En M4 conecté la arquitectura con un entorno comercial simulado.

Se incorporaron:

* entrada mediante correo electrónico;
* memoria persistente reutilizada desde M3;
* CRM de contactos;
* creación de borradores de respuesta;
* notificaciones internas;
* filtros preventivos para correos automáticos;
* limpieza y minimización de datos.

Las respuestas hacia clientes permanecen bajo supervisión humana mediante generación de borradores en lugar de envío automático.

---

## M5 - Base Documental y RAG

En M5 incorporé una base de conocimiento documental para responder consultas sobre políticas internas.

Se agregó la ruta:

`DOCUMENTATION`

y un Worker especializado en recuperación documental.

El módulo incluye:

* documento institucional ficticio;
* ingesta documental independiente;
* fragmentación del contenido;
* generación de representaciones vectoriales;
* búsqueda semántica;
* respuestas fundamentadas en documentación;
* regla `"No sé"` cuando la información no está respaldada;
* gobernanza y versionado documental.

---

## M6 - Canal Conversacional de Voz

En M6 incorporé un canal de entrada y salida por voz reutilizando la arquitectura existente.

Flujo conceptual:

`Audio → STT → Validación → Normalización → Manager → Workers → TTS → Audio`

La entrada de voz se transforma al mismo contrato utilizado por los demás canales, permitiendo reutilizar memoria, Manager, Workers y RAG.

También se incorporaron:

* validación de transcripciones;
* límite de longitud para respuestas habladas;
* contingencia ante entradas inválidas;
* tratamiento temporal de archivos de audio;
* simulación del canal mediante Webhook.

---

## M7 - Sistema Agéntico Vertical de Sales Ops & Customer Success

En M7 evolucioné la arquitectura hacia una solución vertical orientada a **Sales Operations & Customer Success**.

Se incorporaron tres nuevas capas especializadas:

### Sales Scoring

Evalúa oportunidades comerciales mediante un score estructurado basado en:

* intención;
* urgencia;
* presupuesto;
* autoridad;
* ajuste documental;
* engagement.

El resultado clasifica cada oportunidad como `ALTA`, `MEDIA`, `BAJA` o `DATOS_INSUFICIENTES`.

### Riesgo y Churn

Analiza señales relacionadas con:

* insatisfacción;
* intención de cancelación;
* churn;
* reembolsos;
* compensaciones;
* fraude;
* reclamos legales.

Los casos críticos activan supervisión humana obligatoria.

### Auditor Revenue

Funciona como una capa de control independiente.

Verifica:

* coherencia entre score y clasificación;
* consistencia del nivel de riesgo;
* respeto de las políticas de autonomía;
* preservación de decisiones HITL;
* posibles inconsistencias de gobernanza.

El Auditor no modifica registros ni ejecuta acciones comerciales.

### Semáforo Operativo

La salida consolidada se evalúa mediante reglas determinísticas:

* `GREEN`: automatización permitida para acciones de bajo riesgo;
* `YELLOW`: autonomía limitada y revisión recomendada;
* `RED`: intervención humana obligatoria.

### Human-in-the-Loop

Los casos clasificados como `RED` quedan detenidos hasta obtener una decisión humana.

El flujo registra:

* nivel de riesgo;
* resultado de auditoría;
* necesidad de HITL;
* decisión humana;
* estado final del caso.

De esta manera, acciones sensibles relacionadas con aspectos financieros, legales o de churn crítico quedan fuera de la autonomía del sistema.

---

# Estructura de Workflows

```text
/workflows/

M1/
└── checkpoint1_noelia_rausch.json

M2/
├── manager_noelia_rausch.json
├── worker_inventario_noelia_rausch.json
└── worker_calificacion_lead_noelia_rausch.json

M3/
├── manager_memoria_noelia_rausch.json
├── worker_inventario_noelia_rausch.json
└── worker_calificacion_lead_noelia_rausch.json

M4/
├── checkpoint4_noelia_rausch.json
├── worker_inventario_noelia_rausch.json
└── worker_calificacion_lead_noelia_rausch.json

M5/
├── checkpoint5_noelia_rausch.json
├── ingesta_documental_noelia_rausch.json
└── worker_conocimiento_documental_noelia_rausch.json

M6/
└── checkpoint6_noelia_rausch.json

M7/
├── checkpoint7_noelia_rausch.json
├── worker_scoring_comercial_noelia_rausch.json
├── worker_riesgo_churn_noelia_rausch.json
└── worker_auditor_revenue_noelia_rausch.json
```

La documentación correspondiente a cada checkpoint se conserva separada para poder revisar la evolución del proyecto.

---

# Cómo importar los workflows

1. Descargar los archivos `.json` correspondientes al módulo.
2. Importar los workflows en la instancia de automatización.
3. Configurar las credenciales propias.
4. Importar primero los Workers cuando el Manager dependa de sub-workflows.
5. Verificar que cada nodo de ejecución de sub-workflow esté asociado al Worker correspondiente.
6. Configurar las conexiones externas y bases utilizadas.
7. Ejecutar pruebas desde el workflow principal.

Las credenciales utilizadas durante el desarrollo **no forman parte del repositorio**.

Cada usuario debe configurar sus propias conexiones antes de ejecutar los workflows.

---

# Evolución del Proyecto

| Módulo | Implementación                                                     | Estado       |
| ------ | ------------------------------------------------------------------ | ------------ |
| M1     | Agente base + razonamiento + observabilidad                        | ✅ Completado |
| M2     | Manager + Workers + enrutamiento multi-agente                      | ✅ Completado |
| M3     | Memoria persistente + Session ID + Summarization                   | ✅ Completado |
| M4     | Correo + CRM + borradores + notificaciones + controles preventivos | ✅ Completado |
| M5     | Base documental + búsqueda semántica + RAG + gobernanza            | ✅ Completado |
| M6     | Entrada STT + canal de voz + TTS + contingencia                    | ✅ Completado |
| M7     | Sales Scoring + Riesgo/Churn + Auditor Revenue + Semáforo + HITL   | ✅ Completado |
| M8-M11 | Evolución progresiva hasta el Proyecto Final Integrador            | ⏳ Pendiente  |

---

## Seguridad y privacidad

El repositorio no debe contener:

* credenciales;
* claves de acceso;
* tokens;
* contraseñas;
* identificadores privados innecesarios;
* información real de clientes.

Las integraciones externas deben configurarse nuevamente después de importar los workflows.

---

## Autor

**Noelia Rausch**

Curso: **Automatización Avanzada con IA - Coderhouse**

Este README se actualizará en cada checkpoint para documentar la evolución progresiva del proyecto.
