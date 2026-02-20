---
description: Dashboard de estadísticas y métricas del equipo de desarrollo
---

# 📊 Workflow: Estadísticas de Code Reviews

## Uso
```
/review-stats
/review-stats [PERIODO]  (semanal|mensual|trimestral)
/review-stats dev [USUARIO]
```

---

## 📋 Pasos del Workflow

### Paso 1: Cargar datos
// turbo
```powershell
# Leer archivos de métricas
$history = Get-Content "review-history.json" | ConvertFrom-Json
$metrics = Get-Content "dev-metrics.json" | ConvertFrom-Json
```

---

### Paso 2: Generar Reporte según tipo

#### 2.1 Reporte General del Equipo
```markdown
## 📊 Dashboard del Equipo - [PERIODO]

### 📈 Resumen General
| Métrica | Valor | Tendencia |
|---------|-------|-----------|
| PRs Revisados | {TOTAL} | {TREND} |
| Score Promedio | {AVG_SCORE}/100 | {TREND} |
| Issues P0 | {P0_COUNT} | {TREND} |
| Issues P1 | {P1_COUNT} | {TREND} |

### 🏆 Top Performers
| # | Developer | PRs | Avg Score | Badges |
|---|-----------|-----|-----------|--------|
| 1 | @{DEV1} | {N} | {SCORE} | {BADGES} |
| 2 | @{DEV2} | {N} | {SCORE} | {BADGES} |
| 3 | @{DEV3} | {N} | {SCORE} | {BADGES} |

### ⚠️ Atención Requerida
| Developer | Issue | Frecuencia | Acción Sugerida |
|-----------|-------|------------|-----------------|
| @{DEV} | {ISSUE} | {N} veces | {ACCION} |

### 🔥 Issues Más Comunes
| # | Issue | Ocurrencias | Recurso |
|---|-------|-------------|---------|
| 1 | {ISSUE1} | {N} | [Link] |
| 2 | {ISSUE2} | {N} | [Link] |
| 3 | {ISSUE3} | {N} | [Link] |

### 📊 Por Repositorio
| Repo | PRs | Avg Score | P0 Rate |
|------|-----|-----------|---------|
| {REPO1} | {N} | {SCORE} | {RATE}% |
| {REPO2} | {N} | {SCORE} | {RATE}% |
```

---

#### 2.2 Reporte Individual de Developer
```markdown
## 👤 Perfil de @{DEVELOPER}

### 📊 Estadísticas
| Métrica | Valor | vs Equipo |
|---------|-------|-----------|
| Total PRs | {N} | - |
| Score Promedio | {SCORE} | {+/-}% |
| PRs sin P0 | {N}/{TOTAL} | {RATE}% |
| Badge Actual | {BADGE} | - |

### 📈 Evolución (últimos 30 días)
[Gráfico de scores por PR]

### ⚠️ Issues Recurrentes
| Issue | Frecuencia | Último | Estado |
|-------|------------|--------|--------|
| {ISSUE} | {N} veces | {FECHA} | {🔴|🟡|🟢} |

### 🎯 Próximo Badge
- Actual: {BADGE_ACTUAL}
- Siguiente: {BADGE_NEXT}
- Progreso: {X}/{Y} ({PERCENT}%)

### 📚 Recursos Recomendados
- [{RECURSO1}]({URL1}) - Para mejorar {ISSUE1}
- [{RECURSO2}]({URL2}) - Para mejorar {ISSUE2}
```

---

### Paso 3: Calcular Badges

**Fórmulas de cálculo:**

| Badge | Fórmula |
|-------|---------|
| 🥉 Bronze | `COUNT(score > 80) >= 5` |
| 🥈 Silver | `CONSECUTIVE(p0_issues == 0) >= 10` |
| 🥇 Gold | `COUNT(score > 90) >= 20` |
| 💎 Diamond | `COUNT(mentees) >= 3` |

---

### Paso 4: Detectar Alertas

**Condiciones de alerta:**

```
🚨 ALERTA CRÍTICA:
- Score < 70 en último PR
- P0 igual repetido 3+ veces

⚠️ ALERTA MODERADA:
- Score promedio < 75 (últimos 5 PRs)
- Sin PRs en 14+ días

📝 OBSERVACIÓN:
- Tendencia negativa (-10% en 2 semanas)
- Issue P1 repetido 2+ veces
```

---

### Paso 5: Actualizar dev-metrics.json

Después de generar el reporte, actualizar:
- `developers[usuario].stats`
- `developers[usuario].badges`
- `developers[usuario].recurring_issues`
- `team_stats`
- `metadata.last_updated`

---

## 📅 Reporte Semanal Automático

**Generar cada lunes a las 9:00 AM:**

```markdown
## 📊 Reporte Semanal - Semana {N} de {AÑO}

### 📈 Resumen
- **PRs revisados:** {N}
- **Score promedio:** {SCORE}/100
- **P0 encontrados:** {N}
- **Tiempo promedio de fix:** {HORAS}h

### 🏆 Developer Destacado
**@{USUARIO}** - {MOTIVO}
- Score promedio: {SCORE}
- PRs entregados: {N}
- Sin issues P0: {N} consecutivos

### ⚠️ Foco de Mejora
**Issue más común:** {ISSUE} ({N} ocurrencias)
> **Tip:** {SUGERENCIA}

### 📊 Tendencia del Equipo
[📈 Mejorando | ➡️ Estable | 📉 Requiere atención]
```

---

## 🔔 Sistema de Notificaciones

| Evento | Acción | Destinatario |
|--------|--------|--------------|
| Score < 70 | Mensaje directo | Lead técnico |
| P0 repetido 3x | Alerta + Capacitación | Dev + Lead |
| Badge obtenido | Celebración en canal | Equipo |
| Dev inactivo 14d | Check-in reminder | Lead |
| Reporte semanal | Post automático | Canal del equipo |

---

**Última actualización**: 2026-01-20
**Versión**: 1.0
