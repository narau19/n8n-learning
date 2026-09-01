# n8n-learning

# Checkpoint 1 - Agente Base y Motor de Razonamiento (M1)

## 📌 Descripción del Proyecto
Este repositorio contiene el **primer hito (M1)** del proyecto integrador del curso. Se trata de un agente autónomo configurado en n8n que actúa como **Asesor Comercial Senior** para la empresa ficticia "Soluciones Globales Tech". 

Este flujo base es el cimiento sobre el cual se construirán los próximos módulos (Memoria, Multi-agente, RAG, Voz, etc.). **No es un ejercicio aislado**, sino la versión 1.0 de un asistente evolutivo.

## 🧠 Estructura del Flujo (Versión 1.0)
El workflow está diseñado siguiendo la arquitectura básica de un agente con herramientas:

1. **Disparador (Trigger)**: `When Chat Message Received` - Captura los mensajes del usuario a través de la interfaz nativa de chat de n8n.
2. **Cerebro (AI Agent)**: Configurado como **Tools Agent** con el modelo `gpt-4o-mini` (o equivalente). 
   - **System Prompt**: Define el rol corporativo, objetivos comerciales y restricciones explícitas (sin lenguaje inclusivo).
   - **Guardrails**: Límite máximo de **5 iteraciones** para evitar bucles infinitos.
3. **Herramienta Lateral (Tool)**: Nodo `Function` conectado a la entrada "Tools". 
   - **Descripción semántica extensa** que le indica al modelo activarlo ante palabras clave como *stock, precio, disponibilidad, cotización*.
   - Simula una consulta a un sistema ERP de inventario.
4. **Observabilidad (Log)**: Nodo `Function` final que captura el `Execution Log` y lo imprime en consola o lo estructura como reporte para supervisión humana.

## 📂 ¿Cómo importar este flujo en mi n8n?
1. Descarga el archivo `checkpoint1_tu_nombre_apellido.json` desde este repositorio.
2. Abre tu instancia local de n8n.
3. Ve al lienzo principal y haz clic en el menú superior derecho (tres puntos) → **Import from File**.
4. Selecciona el archivo descargado. El workflow se cargará automáticamente.
5. **IMPORTANTE**: Dirígete al nodo `Google Gemini Chat Model` y pega tu propia `API Key` (o cambia el modelo).
6. Haz clic en **"Execute Workflow"** y escribe un mensaje de prueba en el chat flotante.

## ⚙️ Configuración Post-Importación
| **Iteraciones máximas** | 5 |
| **Sugerencias rápidas** | "Consultar stock del Producto A", "Cotización Producto B", "Calificar lead" |


## 🗺️ Hoja de Ruta del Proyecto (Evolución)
| **M1 (Actual)** | Agente base + System Prompt + 1 Tool + Log | ✅ Completado |
| **M2** | Multi-agente (Manager + Workers) | ⏳ Próximo |
| **M3** | Memoria persistente (Airtable) | ⏳ Próximo |
| **M4** | Integraciones reales (CRM, Calendario) | ⏳ Próximo |
| **M5** | RAG y Vector Store (LlamaCloud) | ⏳ Próximo |
| **M6** | Voz (STT/TTS) | ⏳ Próximo |

## 👤 Autor
- **Nombre y Apellido**: Noelia Rausch
- **Curso**: Automatización con IA, de Coderhouse

---

*Este README se actualizará en cada checkpoint (M2, M3...) para reflejar la evolución del agente.*
