# 🔎 AI Workflow Auditor

Framework avanzado de auditoría técnica y revisión de código impulsado por IA, diseñado para garantizar estándares de alta calidad, seguridad y escalabilidad en proyectos de software.

## 🚀 Descripción General
Este proyecto centraliza los flujos de trabajo (workflows) para realizar revisiones de código profesionales y automatizadas. Utiliza una metodología de **Auditoría Profesional** basada en puntos de control (GATES) y una clasificación de hallazgos por severidad (P0 a P3).

## ✨ Características Principales

*   **🛡️ Auditoría de PRs Inteligente**: Análisis profundo de Pull Requests con detección de bugs lógicos, riesgos de crash y vulnerabilidades.
*   **🚦 Sistema de GATES**: Puntos de control obligatorios (Encoding, Auto-verificación, Archivación) que garantizan la validez del review.
*   **📋 Clasificación P0-P3**:
    *   **P0 (Crítico)**: Bloqueantes de seguridad, crashes, o violaciones de normas estrictas.
    *   **P1 (Alto)**: Performance, mantenibilidad y lógica de negocio.
    *   **P2/P3 (Bajo)**: Recomendaciones de estilo, formato y buenas prácticas.
*   **🌍 Multi-Lenguaje**: Soporte optimizado para:
    *   **C# / .NET** (ASP.NET MVC, Core, Entity Framework).
    *   **JavaScript / React** (Next.js, Hooks, State Management).
    *   **PHP / WordPress** (Seguridad XSS, SQLi, WP Standards).
    *   **Kotlin / Android** (Coroutines, Jetpack Compose, Null Safety).
    *   **SQL** (Idempotencia, Performance SARGable, Seguridad).
*   **📊 Métricas e Historial**: Registro automático de cada revisión en formato JSON y Markdown para análisis de tendencia y re-reviews inteligentes.

## 🛠️ Requisitos del Sistema
*   **GitHub CLI (`gh`)**: Necesario para interactuar con los Pull Requests.
*   **PowerShell**: Entorno de ejecución para los scripts de automatización.
*   **Visual Studio Code / Cursor**: Integrado mediante `.cursorrules` para una experiencia optimizada.

## 📁 Estructura del Proyecto
*   [`review-pr.md`](file:///d:/GitHubProyects/workflows/review-pr.md): El "Manual de Operaciones" y motor del flujo de auditoría.
*   `reviews/`: Directorio que almacena el historial persistente de auditorías por repositorio y PR.
*   `dev-metrics.json`: Base de datos de métricas de desarrollo.
*   `review-history.json`: Índice global de revisiones ejecutadas.

## 📖 Modo de Uso
Para iniciar una auditoría, utiliza el comando configurado en tu entorno de IA:

```bash
/review-pr [LINK_DEL_PR]
```

El sistema guiará el proceso a través de 15 pasos definidos, culminando en la publicación del reporte en GitHub y la formalización para Confluence si es necesario.

## 🎓 Filosofía
Este framework no busca solo encontrar errores, sino promover la **Mejora Continua** del equipo, proporcionando aprendizajes clave y soluciones técnicas (Fixes) claras y accionables.

---
**Desarrollado para Equipos de Ingeniería de Alto Rendimiento.**
