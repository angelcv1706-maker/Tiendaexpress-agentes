# TiendaExpress Perú S.A.C. - Sistemas Multiagentes con LangChain[cite: 2]

Repositorio de evidencias para la sesión 09 sobre Automatización Inteligente de Procesos mediante el ecosistema LangChain[cite: 2].

## Procesos Automatizados
* **D1**: Atención de consultas pre-venta[cite: 2].
* **D2**: Gestión de devoluciones y reembolsos[cite: 2].

## Tecnologías y Modelos
* **Modelos LLM**: Anthropic Claude, utilizando `claude-haiku-5-5` para clasificar y extraer información, y `claude-sonnet-5-5` para asesorar y validar fotos[cite: 2].
* **Versiones**: langchain 1.4.7, langchain-core 1.7.3, langgraph 1.2.18, deepagents 0.7.32, langsmith 0.14.16 y fastmcp 4.1.4[cite: 2].

## Arquitectura del Ecosistema (4 Capas)
El proyecto implementa las cuatro capas del ecosistema LangChain[cite: 2]:
1. **LangChain Core (Fundación)**: Contiene 5 cadenas LCEL ubicadas en `src/cadenas/` que conectan prompts, modelos Claude y parsers[cite: 2]. Integra herramientas de 5 servidores MCP (FastMCP) para interactuar con los sistemas de la empresa[cite: 2].
2. **LangGraph (Orquestación)**: Orquestador compuesto por 24 nodos, que incluye 7 pausas de intervención humana (HITL) mediante la función `interrupt()` y 4 ciclos recursivos basados en el BPMN TO-BE[cite: 2]. Maneja el estado persistente (checkpointer) para esperas externas, como la respuesta del cliente o webhooks de almacén[cite: 2].
3. **LangSmith (Observabilidad)**: Sistema de trazabilidad con un cliente que anonimiza datos sensibles (como nombres, DNI, correos y tarjetas) antes de enviarlos a la plataforma para cumplir con las normativas de protección de datos personales[cite: 2].
4. **Deep Agents (Harness)**: Infraestructura para tareas complejas implementada en `src/deep_agents/`[cite: 2]. Incluye a los agentes A6 (Validador) y A7 (Financiero), los cuales escriben su plan con `write_todos`, consultan una memoria interna (`AGENTS.md`) y ejecutan skills con permisos de solo lectura[cite: 2].

## Agentes Implementados
* **A0 Recepción**: Enmascara los datos personales del usuario con reglas fijas[cite: 2].
* **A1 Clasificador**: Evalúa intenciones ejecutando una cadena LCEL 5 veces en paralelo (`.batch()`) para medir la confianza del modelo[cite: 2].
* **A2 Asesor RAG**: Consulta el catálogo técnico y el stock en paralelo usando `RunnableParallel`[cite: 2].
* **A3 Cotizador**: Calcula precios y envíos; incluye un subgrafo con una pausa HITL si el descuento supera el 10 %[cite: 2].
* **A4 Agendador y A5 Calificador**: Actualizan leads y agendan demostraciones[cite: 2].
* **A6 Validador (Deep Agent)**: Decide si una devolución procede basándose en reglas duras y análisis visual de evidencias/fotos[cite: 2].
* **A7 Financiero (Deep Agent)**: Revierte pagos en pasarelas (Niubiz/Culqi) y emite notas de crédito, requiriendo siempre una aprobación humana obligatoria antes de mover dinero[cite: 2].
* **A8 Logístico y A9 Notificador**: Programación de recojos y envío de comprobantes de rechazo o reembolso[cite: 2].

## Cómo Reproducir el Proyecto
Para ejecutar el entorno, generar las trazas y correr las pruebas desde la raíz del repositorio[cite: 2]:

```bash
pip install -r requirements.txt
cp .env.example .env                 # MODO_SIMULADO=1 permite correr sin gastar tokens[cite: 2]
python -m pytest -q                  # Ejecuta las 32 pruebas del proyecto[cite: 2]
python -m scripts.exportar_grafos    # Exporta los diagramas del orquestador en formatos .mmd y .png a docs/grafos/[cite: 2]
python -m scripts.ejecutar_langsmith # Realiza una corrida de D1 y D2, generando los logs locales[cite: 2]
langgraph dev                        # Inicia LangGraph Studio para visualizar el grafo y reanudar pausas[cite: 2]
