---
description: Auditoría profesional de PR con puntuación)
---

# 🔍 Workflow: Auditoría Profesional de PR

> **🚨 CRÍTICO - Encoding UTF-8**: Al publicar reviews en GitHub, SIEMPRE usar UTF-8 sin BOM en PowerShell para evitar que caracteres especiales (á, é, í, ó, ú, ñ, ¿, ¡) se muestren como `Ã©`, `Ã±`, `Â¿`. Ver [Paso 8](#paso-8-publicar-review) para detalles.

## Uso
```
/review-pr [NUMERO_PR]
/review-pr [NUMERO_PR] del repo [NOMBRE_REPO]
/review-pr https://github.com/[NOMBRE_USUARIO]/[NOMBRE_REPO]/pull/[NUMERO_PR]
https://github.com/[NOMBRE_USUARIO]/[NOMBRE_REPO]/pull/[NUMERO_PR]
```

---

## 🚨 INSTRUCCIONES OBLIGATORIAS PARA LA IA

> **ANTES de ejecutar cualquier paso, la IA DEBE:**
> 1. **LEER ESTE DOCUMENTO COMPLETO** de principio a fin antes de iniciar el review
> 2. **USAR EL FORMATO EXACTO** definido en el Paso 9 para el mensaje del review — NO inventar otro formato, NO omitir secciones, NO modificar el template
> 3. **APLICAR PRIMERO las reglas generales** (Paso 3) que son para **TODOS** los lenguajes
> 4. **EJECUTAR ÚNICAMENTE la sub-sección del Paso 4** que corresponda al lenguaje detectado. **OMITIR COMPLETAMENTE** las demás sub-secciones de ese paso.
> 5. **NO SALTAR la auto-verificación** (Paso 10) antes de publicar
> 6. **CONSULTAR las referencias del Apéndice D** para C#, kotlin, php, javascript y citarlas en el review
> 7. **Si encuentras keys como GOOGLE_API_KEY, AWS_ACCESS_KEY_ID, etc, o servidores como servidor, user, password, etc, o token como USER_TOKEN, PWD_TOKEN,NEW_RELIC_TOKEN,SOMOS_BELCORP_HOST,API_SB_HOST,HOST_UNETE_BELCORP,MATERIALES_REDES_SOCIALES,OAUTH_PASSWORD,user,password,pass,pwd,id,database,YOUTUBE_API_KEY,username, etc, o cualquier información sensible, no debes guardarlas en el review para que no queden expuestas en GitHub. Debes reemplazarlas por *** y en el mensaje del review debes indicar que se reemplazaron por *** (IMPORTANTE)
> 8. **TRACKING DE PASOS** — La IA debe confirmar internamente que completó CADA paso antes de avanzar:
>    - [ ] Paso 0: Validar formato ✓
>    - [ ] Paso 1: Obtener info + verificar reviews anteriores ✓
>    - [ ] Paso 1.1: Filtrar archivos ignorados ✓
>    - [ ] Paso 2: Detectar lenguaje ✓
>    - [ ] Paso 3: Checklist P0-P3 completo ✓
>    - [ ] Paso 3.1: Consultar referencias Apéndice D ✓
>    - [ ] Paso 4: Análisis por lenguaje específico ✓
>    - [ ] 🚫 GATE A — Paso 5: Encoding de caracteres ✓
>    - [ ] 🚫 GATE A — Paso 6-7: Clasificar PR + dependencias ✓
>    - [ ] Paso 8: Calcular puntuación ✓ _(solo si GATE A pasó)_
>    - [ ] Paso 9: Generar comentario (TODAS las 11 secciones) ✓
>    - [ ] 🚫 GATE B — Paso 10: Auto-verificación ✓
>    - [ ] Paso 11: Publicar ✓ _(solo si GATE B pasó)_
>    - [ ] 🚫 GATE C — Paso 12-13: Historial + archivado + limpieza ✓
> 9. **Si no se cumple alguno de estos puntos, el review es INVÁLIDO**


---

> ### 🚫 SISTEMA DE GATES — PUNTOS DE CONTROL OBLIGATORIOS
>
> La IA **NO PUEDE** avanzar sin pasar estos 3 gates. Si la IA salta un gate, **el review completo es INVÁLIDO**.
>
> | Gate | Antes de... | Pasos requeridos | ¿Qué verifica? |
> |------|-------------|------------------|-----------------|
> | 🚫 **GATE A** | Calcular puntuación (Paso 8) | Pasos 5, 6, 7 | Encoding, clasificación PR, dependencias |
> | 🚫 **GATE B** | Publicar review (Paso 11) | Paso 10 | Auto-verificación: reglas P0, consistencia score-issues, formato |
> | 🚫 **GATE C** | Terminar workflow | Pasos 12, 13 | Historial guardado, limpieza final |
>
> **⚠️ ADVERTENCIA PARA TODAS LAS IAs**: El workflow **NO TERMINA** al publicar el comentario en GitHub.
> Después de publicar, TODAVÍA quedan los Pasos 12 y 13 (GATE C). Si los omites, el review es INVÁLIDO.
>
> > **🚨 MODELO DE PERMISOS**: La IA solo necesita pedir permiso para **publicar en GitHub/GitLab** (Paso 11).
> > Todos los demás pasos (obtener diff, análisis, historial, archivado, **limpieza de archivos temporales**) se ejecutan **automáticamente sin pedir permiso**.
> > La IA **NUNCA** debe pedir permiso para eliminar archivos temporales (`$env:TEMP\pr_review_comment.md`, `full_diff.txt`, `files.json`, etc.).
>
> > **🚨 REGLA DE TERMINACIÓN**: Este workflow tiene **15 PASOS**.
> > Si tu respuesta termina antes de imprimir la "CONFIRMACIÓN DE WORKFLOW COMPLETO" (después del Paso 13),
> > tu trabajo está **INCOMPLETO** y será rechazado.

---

## 📋 Pasos del Workflow

---

# 🔰 SECCIÓN 1: ANTES DE ANALIZAR CÓDIGO

> **Objetivo**: Validar input, obtener diff, detectar lenguaje y filtrar archivos irrelevantes.

### Paso 0: Validar formato del PR

> **IMPORTANTE**: Antes de proceder, validar que el input sea un PR válido.

**Formatos aceptados:**
```
✅ https://github.com/{NOMBRE_USUARIO}/{REPO}/pull/{NUMBER}
✅ /review-pr {NUMBER} del repo {REPO}
✅ /review-pr {NUMBER} (usa repo por defecto si está configurado)
✅ PR #{NUMBER} del repo {REPO}
```

**Formatos NO válidos (rechazar):**
```
❌ https://github.com/{NOMBRE_USUARIO}/{REPO}/compare/master...feature/branch  (es comparación, no PR)
❌ https://github.com/{NOMBRE_USUARIO}/{REPO}/tree/feature/branch  (es rama, no PR)
❌ https://github.com/{NOMBRE_USUARIO}/{REPO}/commit/{SHA}  (es commit, no PR)
❌ Solo nombre de rama sin número de PR
```

**Si el formato es inválido, responder:**
```
⚠️ El link proporcionado no es un PR válido.

Formato esperado: https://github.com/{NOMBRE_USUARIO}/{REPO}/pull/{NUMBER}

Por favor proporciona:
1. El link directo al nombre del repositorio
```

**Regex de validación:**
```regex
^https:\/\/github\.com\/[NOMBRE_USUARIO]\/[REPO]\/pull\/\d+$
```

---

### Paso 1: Obtener información del PR (NO ES NECESARIO VALIDACION DEL USUARIO)
// turbo
```powershell
gh pr view {PR_NUMBER} --repo {NOMBRE_USUARIO}/{REPO_NAME} --json title,body,author,additions,deletions,changedFiles,baseRefName,headRefName
gh pr diff {PR_NUMBER} --repo {NOMBRE_USUARIO}/{REPO_NAME}
```

#### 🔄 Paso 1.0: Verificar Reviews Anteriores del mismo PR

> **OBLIGATORIO en re-reviews**: Antes de analizar, verificar si existe un review previo para comparar.

**Verificar si hay reviews previos:**
// turbo
```powershell
$repoDirectory = "{REPO_NAME}" -replace '^.+/', '' # Asegurar que solo sea el nombre del repo, sin el usuario/organización
$reviewDir = Join-Path (Get-Location) "reviews\$repoDirectory\PR-{PR_NUMBER}"
if (Test-Path $reviewDir) {
    Write-Host "✅ Se encontró review previo en: $reviewDir"
    Get-ChildItem $reviewDir -Filter "review_*.md" | Sort-Object Name -Descending | Select-Object -First 1 | ForEach-Object {
        Write-Host "📄 Último review: $($_.Name)"
    }
    if (Test-Path "$reviewDir\issues_found.json") {
        Write-Host "📋 Issues anteriores encontrados:"
        Get-Content "$reviewDir\issues_found.json" -Encoding UTF8
    }
} else {
    Write-Host "ℹ️ No hay reviews previos para este PR. Es la primera revisión."
}
```

**Si hay review previo, la IA DEBE:**
1. Leer el review anterior (`review_*.md`) para conocer las observaciones previas
2. Leer los issues anteriores (`issues_found.json`) para verificar si fueron corregidos
3. En el nuevo review, incluir una sección indicando:
   - 🔍 **Seguimiento de Issues Anteriores**: Seguir el formato detallado en el Paso 9 (Solo para re-reviews).
   - ✅ Issues corregidos desde el último review (Recuperar puntos restados).
   - ❌ Issues que persisten sin corrección (Mantener resta de puntos).
   - 🆕 Issues nuevos encontrados en esta revisión.




### Paso 1.1: 🚫 Archivos a IGNORAR durante el Review

> **IMPORTANTE:** Los siguientes archivos/directorios NO deben ser revisados ni contabilizados en el análisis:

**Archivos de CI/CD y configuración:**
- `.github/*`
- `.github/workflows/*.yml` - Workflows de GitHub Actions
- `.github/CODEOWNERS`
- `.github/dependabot.yml`
- `.gitlab/*`
- `azure-pipelines.yml`

**Archivos auto-generados:**
- `**/Reference.cs` - Service References de WCF (auto-generados por Visual Studio)
- `*.designer.cs` - Archivos designer auto-generados
- `*.g.cs` - Generated code
- `**/Migrations/*.cs` - Entity Framework migrations
- `**.dll` - Binarios en el repositorio

**Dependencias y terceros:**
- `node_modules/`
- `vendor/`
- `packages/`
- `**/bin/`
- `**/obj/`
- `**/target/`
- `**/build/`

> **IMPORTANTE:** Cuando se encuentren archivos de terceros, informar al usuario que no se revisarán porque son de terceros (vendor, node_modules, plugins externos y tambien decirle cual es exactamente si es posible los nombre de archivos).

**Si un PR solo contiene archivos de estas categorías, indicar:**
```
ℹ️ Este PR solo contiene archivos auto-generados o de configuración CI/CD.
No requiere revisión de código.
Score: N/A
Verdict: ✅ APPROVED (Auto-generated/CI files only)
```

### Paso 1.2: 🚨 PRs MUY GRANDES (>20,000 líneas)

> **Si el diff falla con error HTTP 406 "diff exceeded maximum":**

**1. Notificar al usuario:**
```
⚠️ Este PR es muy grande (X líneas, Y archivos).
El diff excede el límite de GitHub API (20,000 líneas).

Para realizar un análisis completo, necesito que clones el repo:

git clone https://github.com/{NAME_GROUP}/{REPO_NAME}.git
git clone https://gitlab.com/{NAME_GROUP}/{REPO_NAME}.git
```

**2. Una vez clonado, el usuario debe indicar la ruta. Luego ejecutar:**
```powershell
Set-Location {RUTA_REPO_CLONADO}
git fetch origin
git checkout {BRANCH_NAME}
git diff origin/{BASE_BRANCH}..HEAD --name-only | Measure-Object -Line  # Contar archivos
git diff origin/{BASE_BRANCH}..HEAD -- "app/wp-content/themes/{THEME}/" | Select-String "// "  # Buscar comentarios
```

**3. Analizar solo los archivos relevantes:**
- Ignorar plugins de terceros y darle a conocer al usuario que no se revisaran porque son de terceros (vendor, node_modules, plugins externos)
- Enfocarse en código propio del proyecto
- Verificar comentarios, encoding, y estándares

**4. Publicar review indicando que fue análisis local:**
```
### :microscope: Análisis Local Realizado
> Se clonó el repo y se analizó la rama `{BRANCH}` comparando con `{BASE_BRANCH}`
```

### Paso 1.3: 🕵️ Investigación de Dependencias (META-REGLA)

> **OBLIGATORIO**: Si encuentras llamadas a librerías externas críticas (Redis, SQL, Pagos, WCF) y NO estás 100% seguro del comportamiento ante errores (ej. `WRONGTYPE` en Redis, comportamientos de `First()` en EF Core), DEBES:
> 1.  **Investigar** o **Asumir el Peor Caso**.
> 2.  Nunca asumir "Happy Path". Si un comando Redis escribe un Hash, ¿qué pasa si la key ya existe como String?
> 3.  Si no puedes verificarlo, marca una **Advertencia de Investigación** en el review.

---


### Paso 2: Detectar lenguaje del proyecto

| Extensiones | Lenguaje | Stack |
|-------------|----------|-------|
| `.cs`, `.cshtml`, `.razor` | C# | .NET/ASP.NET |
| `.js`, `.jsx`, `.ts`, `.tsx`, `.vue` | JavaScript | React/Next.js/Vue/Vanilla |
| `.kt`, `.kts` | Kotlin | Android/Compose |
| `.php` | PHP | WordPress/Laravel |
| `.sql` | SQL | Database |

---

# 🔍 SECCIÓN 2: REGLAS PARA ANÁLISIS DE CÓDIGO

> **Objetivo**: Aplicar checklist P0-P3, reglas por lenguaje, consultar referencias oficiales, y encoding. Estas reglas son la base del review.

---

### Paso 3.1: Consultar Referencias del Apéndice D (OBLIGATORIO)

> **🚨 CRÍTICO**: La IA DEBE consultar las referencias oficiales del **Apéndice D** correspondientes al lenguaje detectado en el Paso 2.
> Esto garantiza que el review se base en documentación oficial y no solo en criterios genéricos.

**Instrucciones:**

1. **Identificar el lenguaje** detectado en el Paso 2
2. **Ir al Apéndice D** (final del documento) y localizar la sección del lenguaje
3. **Consultar las guías oficiales** listadas como referencia para validar:
   - Convenciones de nombres y estilo de código
   - Patrones de arquitectura recomendados
   - Best practices específicas del lenguaje/framework
4. **Registrar qué referencias usaste** — Serán incluidas en la línea final del review

**Mapeo rápido de referencias por lenguaje:**

| Lenguaje/Tecnología | Formato de Escape | Ejemplo |
|---------------------|-------------------|---------|
| **C# / .NET** | Unicode escapes `\uXXXX` | `"No se encontr\u00f3"` |
| **Java/Kotlin** | Unicode escapes `\uXXXX` | `"No se encontr\u00f3"` |
| **JavaScript/TypeScript** | Unicode escapes `\uXXXX` | `"No se encontr\u00f3"` |
| **Python** | Unicode escapes `\uXXXX` | `"No se encontr\u00f3"` |
| **PHP** | HTML entities `&code;` (Vistas) | `No se encontr&oacute;` |
| **HTML/Razor/Handlebars** | HTML entities `&code;` | `No se encontr&oacute;` |
| **XML** | HTML entities `&code;` | `No se encontr&oacute;` |
| **SQL** | Sin tildes | `'No se encontro'` |
| **JSON** | Unicode escapes `\uXXXX` | `"No se encontr\u00f3"` |

> **Ejemplo**: Si el PR es C# con ASP.NET, la IA debe validar que el código siga las convenciones de
> [C# Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)
> y [ASP.NET Core Best Practices](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/best-practices)
> antes de asignar el score.

> ⚠️ **Si la IA NO consultó las referencias, el review está INCOMPLETO.**

---

### Paso 3: Análisis del Código - Checklist Obligatorio

> **🚨 CRÍTICO**: Este checklist es OBLIGATORIO. La IA DEBE verificar CADA punto antes de asignar score.
> **NO SE PUEDE SALTAR NINGUNA REGLA P0**, incluso si el código parece "correcto a primera vista".

> ⚠️ **IMPORTANTE**: Estas reglas aplican **SOLO a los cambios nuevos del PR** (el diff), no al código existente del proyecto.

**Instrucciones para la IA:**
1. Lee el diff línea por línea
2. Verifica CADA regla P0 del checklist contra el código
3. Si encuentras una violación → **DEBES** reportarla como P0
4. Si no verificaste todas las reglas → El review está INCOMPLETO
5. **Cualquier violación P0 en código NUEVO = RECHAZO sin importar puntaje**

---

#### 📋 3.1 Reglas P0 - Todos los Lenguajes (BLOQUEANTES)

**Para CADA línea de código nuevo (`+` en el diff), verificar:**

##### Seguridad
- [ ] **Seguridad de datos sensibles**: ¿Hay passwords, API keys, tokens expuestos? (PROHIBIDO)
  - ❌ Datos sensibles expuestos (passwords, API keys, tokens en código) → Rechazo inmediato
- [ ] **SQL Injection**: ¿Hay concatenación de strings en queries SQL? (PROHIBIDO)
  - ❌ SQL Injection potencial → Rechazo inmediato
- [ ] **Passwords/tokens en URLs**: ¿Hay credenciales en query params en lugar de headers? (PROHIBIDO)
  - ❌ Deben ir en headers, NUNCA en URLs → Rechazo inmediato

##### Errores de código
##### Errores de código y Crash Risk (P0)
- [ ] **Naming de Parámetros/Locales**: ¿Hay parámetros o variables locales en `PascalCase`? (PROHIBIDO)
  - ❌ Deben ser `camelCase` (ej: `myVar`, `userId`). `PascalCase` solo para Clases, Propiedades y Métodos. → Rechazo inmediato
- [ ] **LINQ Inseguro en Datos Dinámicos**: ¿Se usa `.First()`, `.Last()`, `.Single()` en consultas a BD, Redis o APIs? (PROHIBIDO)
  - ❌ Estos métodos lanzan excepción si no hay datos.
  - ✅ **SOLUCIÓN SEGURA**: Sugerir usar `FirstOrDefault()`, `Find()`, `TryGet...` o validar previamente. Dejar que el desarrollador elija la mejor opción segura según el caso.
- [ ] **Typos en nombres**: ¿Hay errores de escritura en nombres de métodos/variables? (PROHIBIDO)
  - ❌ Ejemplo: `requrida()` en vez de `requerida()` → Rechazo inmediato
- [ ] **Inconsistencia de case**: ¿Se llama a un método con diferente capitalización que su definición? (PROHIBIDO)
  - ❌ Ejemplo: `Requerida()` y luego `requerida()` → Rechazo inmediato
- [ ] **Merge conflict markers**: ¿Hay `<<<<<<<`, `=======`, `>>>>>>>`? (PROHIBIDO)
  - ❌ Marcadores de merge conflict → Rechazo inmediato
- [ ] **Código comentado extenso**: ¿Hay >5 líneas de código comentado? (PROHIBIDO)
  - ❌ Código comentado extenso → Rechazo inmediato
- [ ] **Inconsistencia de nombre de archivo/vista** (Case Sensitive): Si el archivo es `NuevaVista`, la referencia debe ser `NuevaVista` (no `nuevaVista`) → Rechazo inmediato

##### Código de debug
- [ ] **Console.log/print/Log.d**: ¿Hay logs de debug en código de producción? (PROHIBIDO)
  - ❌ `console.log`, `print`, `Log.d` en producción → Rechazo inmediato
  - ❌ **`// console.error(...)` comentado** → Rechazo inmediato (es código muerto, ver regla de Comentarios)
  - ⚠️ **EXCEPCIÓN para `console.error` en catch blocks**: Si el proyecto **NO tiene un logger centralizado**, aplicar el siguiente criterio:
    - **Paso 1 — Buscar si existe una mejor alternativa en el proyecto**: Revisar otros archivos del mismo PR o del proyecto para detectar si ya existe un mecanismo de logging (ej: `ctx.log`, `_logger`, `logger.error`, `Timber.e`, `Sentry.captureException`, servicio de Kibana, etc.).
      - ✅ Si **existe una mejor alternativa**: Reportarla como **P1** y recomendar usarla en lugar de `console.error`. Ejemplo: _"El proyecto usa `logger.error` en otros módulos — se recomienda usarlo aquí para consistencia."_
      - ✅ Si **NO existe ninguna alternativa mejor**: No reportar como violación. Incluir una **nota al desarrollador** sugiriéndole que decida cómo manejar la trazabilidad del error. Ejemplo: _"No se detectó un logger centralizado. Se deja a criterio del desarrollador si mantener `console.error`, silenciar el error o implementar otro mecanismo de trazabilidad."_
    - ❌ Rechazar siempre: `catch (e) { // console.error('Error', e); }` (comentado = código muerto, sin trazabilidad)
    - ❌ Rechazar siempre: `console.log('Success:', data)` (log de flujo feliz, no de error)
- [ ] **Debugger statements**: ¿Hay `debugger;`, `binding.pry`, etc? (PROHIBIDO)
  - ❌ Debugger statements → Rechazo inmediato

##### Comentarios y Documentación
- [ ] **Comentarios en código nuevo**: ¿Hay comentarios `//`, `/* */`, `#`, `{{!-- --}}`, `@* *@`, `<!-- -->`, etc? (PROHIBIDO)
  - ❌ No se permiten comentarios en ningún lenguaje. El código debe ser autoexplicativo → Rechazo inmediato
  - ❌ **Caso especial — debug comentado**: `// console.error(...)`, `// console.log(...)`, `// print(...)`, etc. son código muerto disfrazado de comentario. Se aplica esta misma regla P0 → Rechazo inmediato
  - ✅ **Distinción clave**: Un `console.error(...)` activo en un `catch` puede ser aceptable (ver regla "Excepción catch sin logger"). Un `// console.error(...)` comentado NUNCA es aceptable.

##### Consistencia (P0)
- [ ] **Inconsistencia de Logging**: Si el proyecto utiliza un logger específico (ej: `ctx.log`, `_logger`, `Timber`), **PROHIBIDO** usar `console.error`, `System.out` o `print`. Usar el logger existente para mantener consistencia y features (contexto, formato, niveles) → Rechazo inmediato
- [ ] **Inconsistencia con patrones existentes**: Si el proyecto ya usa una librería/patrón para algo (ej: HTTP request, Date handling, Validation), **NO** introducir una nueva forma de hacerlo sin justificación. Alinearse con el código existente → Rechazo inmediato
- [ ] **Inconsistencia de Manejo de Errores**: Si el proyecto tiene una estrategia de manejo de errores definida (ej: `ctx.log` para Kibana, middleware de errores), **NO** sugerir manejadores que no existan en el proyecto. Usar siempre la infraestructura existente (Kibana, Sentry, etc) → Rechazo inmediato

---

#### 📋 3.2 Programación Defensiva - NULL SAFETY (P0)

**Para CADA acceso a variable/parámetro/propiedad, verificar:**

- [ ] **¿Se accede a propiedades sin `?.`?** → Ejemplo: `user.address.city` sin validar
  - ❌ Antes de usar `.Property` o `.Method()`, validar que el objeto no sea null
- [ ] **¿Se usa un índice sin validar colección?** → Ejemplo: `array[0]` sin validar length
  - ❌ Antes de `collection[index]`, validar que no sea null/empty y que el índice exista
- [ ] **¿Hay casting directo sin validar tipo?** → Ejemplo: `(Type)obj` sin `as` + null check
  - ❌ Usar `as` con null check o `is` pattern matching en lugar de casting directo
- [ ] **¿Se deserializa sin validar resultado?** → Ejemplo: `JSON.parse()` sin try-catch
  - ❌ El resultado de JSON/XML parse puede ser null
- [x] **¿Se usan parámetros de función sin validar?** → Ejemplo: parámetro usado directamente en filtro BD
  - ❌ **Parámetros críticos deben validarse al inicio del método**
  - No solo validar que no sean nulos, sino también validar su **formato, longitud o estructura** (ej. RegEx para códigos de país, `trim()` para espacios, valores mínimos/máximos) antes de pasarlos a dependencias críticas externas (Caché, DB, HTTP APIs).
  - **🚨 ESPECIAL ATENCIÓN**: Parámetros en filtros MongoDB (`$match`, `$eq`, etc.)
  - **🚨 ESPECIAL ATENCIÓN**: Parámetros en queries SQL (`WHERE`, `JOIN`, etc.)
  - **🚨 ESPECIAL ATENCIÓN**: Parámetros en APIs externas (headers, body, query params)


**Ejemplos de violaciones:**
```csharp
// ❌ MAL - Acceso directo sin validar
var nombre = usuario.Direccion.Calle;  // Si Direccion es null, explota

// ✅ BIEN - Con validación
var nombre = usuario?.Direccion?.Calle ?? "Sin dirección";

// ❌ MAL - Índice sin validar
var primero = lista[0];  // Si lista es null o vacía, explota

// ✅ BIEN - Con validación
var primero = lista?.FirstOrDefault();
```

```javascript
// ❌ MAL - Acceso directo
const city = response.data.address.city;

// ✅ BIEN - Con optional chaining
const city = response?.data?.address?.city ?? 'Unknown';
```

```php
// ❌ MAL - Acceso directo
$city = $response->data->address->city;

// ✅ BIEN - Con optional chaining
$city = $response?->data?->address?->city ?? 'Unknown';
```

---

#### 📋 3.3 REGLA ABSOLUTA - PROHIBIDO SUGERIR COMENTARIOS, DOCUMENTACIÓN O TESTS

> Esta es una regla **OBLIGATORIA** sin excepciones. **NUNCA** sugerir en recomendaciones P1/P2/P3:
> - ❌ Agregar comentarios en el código
> - ❌ Agregar documentación en README.md
> - ❌ Agregar documentación en wiki
> - ❌ Agregar documentación externa de cualquier tipo
> - ❌ Agregar XML docs / JSDoc / docstrings
> - ❌ Agregar comentarios explicativos de cualquier forma
> - ❌ Agregar tests (unit tests, integration tests, etc.)

> **Excepción**: Solo sugerir tests si el usuario **explícitamente lo solicita** en el PR o en la conversación.

**✅ En su lugar, sugerir ÚNICAMENTE:**
- **Nombres descriptivos** de variables/funciones/clases que hagan el código autoexplicativo
- **Refactoring** para simplificar lógica compleja
- **Constantes con nombres claros** en lugar de magic numbers
- **Tipos explícitos** que documenten contratos de funciones
- **Separación de funciones** para reducir complejidad
- **Extracción de lógica** a funciones con nombres que expliquen el "qué" y "por qué"

---

#### 📋 3.4 Issues P1 - Todos los Lenguajes

##### Deuda técnica
- ❌ **TODO/FIXME sin ticket** - Deben tener referencia a issue (ej: `// TODO: [TIPO_TICKET]-[NUMERO_TICKET]`)
- ❌ **URLs/endpoints hardcodeados** - Deben estar en config, environment o ser obtenidos desde aws secrets manager
- ❌ **Funciones vacías** o solo con `pass`/`return` sin implementar
- ❌ **Imports/variables no usados** - Código muerto

##### Duplicación vs Restricciones Arquitectónicas
- ⚠️ **Antes de penalizar por "Código Duplicado"**, la IA debe analizar el **contexto arquitectónico** de ambos componentes (Aplica para Frontend y Backend):
  - ✅ **Justificado (NO penalizar)**: Si un componente no puede usar el código compartido porque existe una barrera de contexto técnico o de negocio.
    - *Ejemplos Frontend*: El componente está fuera del `Provider` necesario (estado o caché), renderiza en SSR sin hidratación, o la abstracción usa paquetes incompatibles con ese render-tree.
    - *Ejemplos Backend*: Evitar referencias circulares entre proyectos (ej. Proyecto `Core` vs `Web`), scopes de Inyección de Dependencias incompatibles, o separación intencionada de dominios/microservicios (DDD). En estos casos, la duplicación controlada local es permitida.
  - ❌ **Duplicación perezosa (Penalizar)**: Si ambos componentes están en el mismo contexto/capa, tienen las mismas dependencias disponibles y pueden consumir la abstracción compartida sin problemas, pero el desarrollador eligió copiar-pegar. Recomendar usar la abstracción.

##### Mantenibilidad
- ❌ **Código duplicado** >10 líneas en 2+ archivos
- ❌ **Archivos >500 líneas** - Considerar dividir
- ❌ **Funciones >100 líneas** - Demasiado largas
- ❌ **Mezcla de convenciones naming** (camelCase + snake_case en mismo metodo o archivo modificado)

##### Calidad
##### Calidad y Robustez
- ❌ **Redis Type Safety** - Verificar si se escribe una estructura compleja (Hash/Set) en una key que podría ser String. Riesgo de `WRONGTYPE`. Sugerir validar tipo o usar keys distintas.
- ❌ **Caché como Dependencia Dura (Falta de Tolerancia a Fallos)** - ¿Se lanzan (`throw`) errores cuando falla una escritura/lectura en Redis o Caché? (ej. `catch (e) { throw e; }` al hacer un `set`).
  - El caché debe ser "Best-effort" (dependencia blanda). Un fallo en caché (por timeout o caída temporal) NO DEBE romper el flujo principal de la aplicación.
  - **Extension de falla por Configuración (Opt-in Cache)**: Si falta configuración (ej. `env.REDIS` no está seteado o falta prefijo de caché), métodos clave como `generateKey` NO DEBEN lanzar error (`throw`). Deben retornar `null` silenciosamente de modo que las llamadas superiores eviten usar la caché e invoquen directamente a la Base de Datos como fallback (degradación elegante).
  - ✅ **SOLUCIÓN SEGURA**: Sugerir loguear el error (usando `log.error` o el logger centralizado del proyecto) y retornar `null` (o continuar el flujo), permitiendo que la operación principal (ej. procesar un pedido) finalice con éxito asumiendo un "cache miss" o "bypass" de caché.
- ❌ **Falso Positivo de Inicialización (Fake State)** - ¿Se actualizan banderas de estado (ej. `this.isInitialized = true`, `isReady = true`) incondicionalmente sin verificar si el proceso de configuración o conexión subyacente realmente tuvo éxito?
  - Declarar un objeto o handler como "inicializado" obliga a otros métodos a confiar ciegamente en él. Si la dependencia falla (ej. por falta de variables de entorno o error en conexión) la bandera nunca debería ser `true`.
  - ✅ **SOLUCIÓN SEGURA**: Retornar el estado real de la configuración/conexión, o usar propiedades separadas (ej. `isConfigured` vs `isAvailable`) para convertir las subsecuentes operaciones dependientes en *no-op* (operaciones nulas que no explotan y hacen fallback safely).
- ❌ **Colisión de Llaves en Caché (Cache Key Collision)** - ¿Se está construyendo una llave de caché (`Key`) genérica usando solo una parte del identificador que podría compartirse entre múltiples registros diferentes?
  - Guardar múltiples datos bajo un mismo identificador no compuesto (ej. guardar dos configuraciones distintas bajo el mismo `TablaLogicaID` ignorando su `TablaLogicaDatosID`) causa que se sobreescriban entre sí, originando bugs lógicos silenciosos y respuestas corruptas.
  - ✅ **SOLUCIÓN SEGURA**: Exigir que las llaves de caché sean granulares y combinadas (ej. `tlId:tldId` o `EntityName:ID:SubID`) para garantizar entradas únicas por cada bandera o data cacheados.
- ❌ **Lógica Muerta** - ¿Se leen keys de cache/configuración que nunca se escriben/setean en el código? (Verificar si existen fuera del PR).
- ❌ **Catch genéricos vacíos** sin mensaje útil ni logging
- ❌ **Números mágicos** sin constante explicativa (ej: `if (status == 42)`)
- ❌ **Fechas/horas hardcodeadas** (ej: `"2025-01-01"` en código)

##### Performance
- ❌ **Complejidad algorítmica O(n²)** - Loops anidados con `foreach` sobre colecciones grandes
  - Usar diccionarios/HashSet para búsquedas O(1) en lugar de loops anidados
  - Ejemplo: `foreach(a) { foreach(b) { if(a.id == b.id) } }` → Usar `ToDictionary()` primero
- ❌ **Llamadas Asíncronas en Serie (Waterfall Anti-pattern)** - ¿Hay múltiples llamadas a bases de datos, APIs o servicios externos (`await` / `suspend` / `async`) que se ejecutan secuencialmente a pesar de NO depender de los resultados anteriores?
  - Sumar latencias aumenta el tiempo total de respuesta (Time to First Byte).
  - ✅ **SOLUCIÓN SEGURA**: Sugerir paralelizar las operaciones independientes usando `Promise.all()` (JS), `Task.WhenAll()` (C#), o `async/await` estructurado de Kotlin.

##### Consistencia de Código
- ❌ **Inconsistencia de nombres de parámetros** - Al pasar variables entre métodos, mantener el mismo nombre
  - Ejemplo: `MetodoA(userId, orderId)` debe recibirse como `MetodoB(userId, orderId)`, NO como `MetodoB(id, order)`
  - Excepción: Cuando el contexto cambia significativamente (ej: `parentId` → `childParentId`)

---

#### 📋 3.5 Issues P3 - Formato de archivo

- ℹ️ **Falta newline al final del archivo** - Todos los archivos deben terminar con una línea vacía solo dejarle como sugerencia y explicarle el motivo para que sirviria agregarlo

---

#### ⚠️ Si NO verificaste TODAS las reglas del checklist:

```
El review está INCOMPLETO y debe rehacerse.

Razón: Las reglas P0 son OBLIGATORIAS y no negociables.
Si una IA salta una regla P0, puede aprobar código con bugs críticos.
```

---

### Paso 4: Análisis por Lenguaje Específico

> 🛑 **ROUTER OBLIGATORIO**:
> Basado en el lenguaje detectado en el Paso 2, **VE DIRECTAMENTE** a la sub-sección correspondiente.
> **OMITIR COMPLETAMENTE** las demás sub-secciones.
>
> - **Kotlin / Android** → Ir a [4.1 Kotlin](#41-kotlin--android--jetpack-compose)
> - **C# / .NET** → Ir a [4.2 C#](#42-c--net--aspnet-mvc)
> - **PHP / WordPress** → Ir a [4.3 PHP](#43-php--wordpress--laravel)
> - **Handlebars / Razor** → Ir a [4.4 Templates](#44-handlebarsrazor-templates)
> - **JS / React / Next.js** → Ir a [4.5 JS/React](#45-javascript--react--nextjs)
> - **SQL** → Ir a [4.6 SQL](#46-sql--base-de-datos)

---

#### 🤖 4.1 Kotlin / Android + Jetpack Compose

> **Objetivo**: Garantizar aplicaciones Android robustas, reactivas y libres de crashes (Null Safety).

##### 🚨 Bloqueantes (P0) - Crash & ANR

**1. Null Safety**
- ❌ **Uso de `!!` (Double Bang)**.
    - Causa `NullPointerException` si el valor es null.
    - ✅ Usar `?.` (Safe Call), `?:` (Elvis), o `let`.
    - ❌ `val name = user!!.name`
    - ✅ `val name = user?.name ?: "Unknown"`
- ❌ **`lateinit var` sin inicializar**.
    - Verificar con `.isInitialized` si hay riesgo.

**2. Hilos y Coroutines (ANR)**
- ❌ **Operaciones I/O en Main Thread**.
    - Database, Network, o cálculos pesados en `Dispatchers.Main`.
    - ✅ Usar `Dispatchers.IO` o `Dispatchers.Default`.
- ❌ **GlobalScope.launch**.
    - No se cancela automáticamente, causa memory leaks.
    - ✅ Usar `viewModelScope`, `lifecycleScope` o `suspend functions`.

**3. Comparaciones de Tipos**
- ❌ **Comparar `Double` o `Float` con `==`**.
    - Problemas de precisión.
    - ✅ Usar `BigDecimal` o `compareTo()`.
    - ❌ `if (price == 0.0)`
    - ✅ `if (price.compareTo(0.0) == 0)`

**4. Jetpack Compose Stability**
- ❌ **MutableState creado fuera de `remember`**.
    - Se reinicia en cada recomposición.
    - ✅ `val state = remember { mutableStateOf(...) }`

---

##### ⚠️ Importantes (P1) - Arquitectura y Performance

**1. Clean Architecture (Android)**
- ⚠️ **Lógica de Negocio en Activity/Fragment/Composable**.
    - UI solo debe mostrar datos y capturar eventos.
    - ✅ Mover lógica a `ViewModel` o `UseCase`.
- ⚠️ **Referencias a Context/View en ViewModel**.
    - Causa Memory Leaks.
    - ✅ Usar `AndroidViewModel` si context es necesario (Application Context), NUNCA Activity Context.

**2. State Management (Compose)**
- ⚠️ **Pasar `ViewModel` a componentes hijos profundos**.
    - Hace los componentes difíciles de testear y reusar.
    - ✅ Pasar solo los parámetros necesarios (State Hoisting) y lambdas para eventos.
- ⚠️ **Recomposiciones innecesarias**.
    - Usar `key` en `LazyColumn`.
    - Usar `remember` para cálculos costosos.
    - Usar Clases inmutables (`data class` con `val`).

**3. Coroutine Error Handling**
- ⚠️ **`launch` sin `try/catch`**.
    - Una excepción no manejada crashea la app.
    - ✅ Usar `CoroutineExceptionHandler` o `runCatching`.

**4. Recerencias Circulares**
- ⚠️ **Listeners o Callbacks que retienen Fragment/Activity**.
    - Usar `WeakReference` o limpiar en `onDestroy`.

---

##### ℹ️ Mejoras (P2/P3) - Idiomático

**1. Scope Functions**
- ℹ️ **Uso excesivo de `if (obj != null)`**.
    - ✅ Usar `.let {}` o `.apply {}`.
- ℹ️ **Configuración de objetos repetitiva**.
    - ✅ Usar `apply` para inicializar:
        ```kotlin
        val intent = Intent().apply {
            action = ACTION_VIEW
            data = uri
        }
        ```

**2. Colecciones**
- ℹ️ **Añadir elementos en loop**.
    - ✅ Usar `map`, `filter`, `forEach`.
    - ❌ `for (item in list) { newList.add(transform(item)) }`
    - ✅ `val newList = list.map { transform(it) }`

**3. Hardcoded Strings/Dimens**
- ℹ️ Textos en código.
    - ✅ Usar `strings.xml`.
- ℹ️ Tamaños en código.
    - ✅ Usar `dimens.xml`.

---

##### 📋 Checklist Específico Kotlin

> Verificar cada punto antes de aprobar:

**🛡️ Robustez**
- [ ] **Cero uso de `!!` (Double Bang)**
    - Verificar que no exista ningún operador `!!`.
    - Solución: Usar `?.`, `?:` o `let`.
- [ ] **Coroutines en Dispatchers correctos**
    - `Dispatchers.Main` solo para UI.
    - `Dispatchers.IO` para DB/Network.
    - `Dispatchers.Default` para CPU heavy.
- [ ] **ViewModel no tiene referencias a Views/Activity**
    - Verificar que no reciba `Activity`, `Fragment` o `View` en constructor.
    - Verificar que no retenga `Context`.
- [ ] **Estado de UI sobrevive a rotación/process death**
    - Verificar uso de `SavedStateHandle` en ViewModel.
    - Verificar `rememberSaveable` en Compose.

**🏗️ Arquitectura**
- [ ] **Capas separadas (Data -> Domain -> UI)**
    - Data: Repositories, DataSources, Network.
    - Domain: UseCases, Models puros.
    - UI: ViewModels, Composables/Fragments.
- [ ] **Inyección de Dependencias (Hilt/Koin) usada**
    - Verificar anotaciones `@Inject`, `@HiltViewModel`.
    - Verificar módulos de DI.
- [ ] **UseCases para lógica de negocio compleja**
    - Verificar que ViewModels no tengan lógica de negocio extensa.

**📐 Jetpack Compose**
- [ ] **State Hoisting aplicado (params in, events out)**
    - Componentes no deben modificar estado interno si pueden evitarlo.
    - Deben recibir `value` y `onValueChange`.
- [ ] **`remember` usado para estado local**
    - Verificar que objetos costosos no se re-creen en recomposición.
- [ ] **Keys únicas en LazyLists**
    - `items(list, key = { it.id })`. NUNCA usar index como key si la lista cambia.
- [ ] **Previews disponibles y funcionales**
    - Verificar que existan `@Preview` para componentes UI.

---
#### 💻 4.2 C# / .NET / ASP.NET MVC

> **Objetivo**: Aplicaciones .NET performantes, seguras y mantenibles.

##### 🚨 Bloqueantes (P0) - Crash & Seguridad

**1. Async/Await Incorrecto (Deadlocks)**
- ❌ **`.Result` o `.GetAwaiter().GetResult()`**.
    - Bloquea el hilo actual. Puede causar deadlocks en ASP.NET clásico o UI apps.
    - ✅ **SIEMPRE** usar `await`.
    - Si es imposible (ej: constructor), usar `Task.Run(...).Result` (con precaución) o refactorizar.
- ❌ **`async void`** (excepto Event Handlers).
    - Excepciones no pueden ser capturadas. Crashea el proceso.
    - ✅ Usar `async Task`.

**2. Null Reference Exceptions**
- ❌ **Uso de objetos sin validar null**.
    - `var user = _repo.GetUser(id); return user.Name;` (Si user es null -> Crash).
    - ✅ `return user?.Name ?? "Guest";`
- ❌ **`FirstOrDefault()` sin manejo de default**.
    - ✅ Validar resultado != null.

**3. Entity Framework Performance**
- ❌ **Consultas N+1 (Lazy Loading involuntario)**.
    - Iterar una colección que dispara queries por cada elemento.
    - ✅ Usar `.Include(x => x.Relacion)` (Eager Loading).
- ❌ **Tracking innecesario en lectura**.
    - ✅ Usar `.AsNoTracking()` para listas de solo lectura (Ahorra memoria y CPU).

**4. ASP.NET MVC / Core**
- ❌ **Lógica de Negocio en Controlador**.
    - Controllers solo deben orquestar (Recibir HTTP -> Llamar Service -> Retornar DTO/View).
    - ✅ Mover lógica a `Services` o `Handlers` (Mediator).
- ❌ **ViewBag/ViewData excesivo**.
    - No es tipado, propenso a errores en runtime.
    - ✅ Usar `ViewModel` fuertemente tipados.

---

##### ⚠️ Importantes (P1) - Performance y Estándares

**1. LINQ Performance**
- ⚠️ **`Count()` vs `Any()`**.
    - ❌ `if (lista.Count() > 0)` (Itera toda la lista).
    - ✅ `if (lista.Any())` (Retorna con el primero).
- ⚠️ **Materialización prematura**.
    - ❌ `db.Users.ToList().Where(u => u.Active)` (Trae todo a memoria, luego filtra).
    - ✅ `db.Users.Where(u => u.Active).ToList()` (Filtra en BD).

**2. Dependency Injection**
- ⚠️ **Captura de dependencias (Captive Dependency)**.
    - Inyectar un servicio `Scoped` dentro de un `Singleton`.
    - ✅ Validar lifetimes correctamente.
- ⚠️ **Instanciación manual (`new Service()`)**.
    - Rompe la inyección de dependencias y testabilidad.
    - ✅ Inyectar interfaz por constructor.

**3. Manejo de Recursos**
- ⚠️ **`IDisposable` sin `using`**.
    - `HttpClient`, `SqlConnection`, `FileStream`.
    - ✅ Usar bloque `using` o declaración `using var`.
    - ℹ️ `HttpClient` debería ser inyectado via `IHttpClientFactory` (Singleton gestionado), no creado en cada request.

**4. Validación de Input**
- ⚠️ **Falta de `ModelState.IsValid`** (en MVC clásico).
- ✅ Validar antes de procesar. (En API Controllers modernos es automático con `[ApiController]`).

---

##### ℹ️ Mejoras (P2/P3) - Mantenibilidad

**1. Naming Conventions**
- ℹ️ Interfaces empiezan con `I` (`IService`).
- ℹ️ Async métodos terminan en `Async` (`GetDataAsync`).
- ℹ️ Campos privados con guión bajo `_campo` (Standard .NET).

**2. Propiedades vs Campos**
- ℹ️ Campos públicos (`public int Id;`).
- ✅ Usar propiedades (`public int Id { get; set; }`).

**3. String Manipulation**
- ℹ️ Concatenación en loops.
- ✅ Usar `StringBuilder`.

---

##### 📋 Checklist Específico C#

> Verificar cada punto antes de aprobar:

**🛡️ Robustez**
- [ ] **No hay `.Result` ni `.Wait()` (Deadlock risk)**
    - Verificar que todas las llamadas async usen `await`.
    - Buscar `Task.Run(...).Result` y validar necesidad.
- [ ] **Validaciones de Null (`?.`, `??`) presentes**
    - Verificar accesos a propiedades de objetos retornados por repositorios.
    - Verificar argumentos de métodos públicos.
- [ ] **`dispose` llamado correctamente (`using`)**
    - Verificar `HttpClient`, `SqlConnection`, `Stream`, `MemoryStream`.
    - Verificar objetos que implementan `IDisposable`.

**db Entity Framework**
- [ ] **`AsNoTracking()` en lecturas**
    - Verificar queries de solo lectura (GETs).
    - Asegurar que no se modifiquen entidades traídas sin tracking.
- [ ] **`Include()` para evitar N+1**
    - Verificar bucles `foreach` que accedan a propiedades de navegación.
    - Sugerir `Include` o `Select` proyectado.
- [ ] **Queries filtran en BD, no en memoria**
    - Verificar `Where()` antes de `ToList()`.
    - Verificar ausencia de evaluaciones locales en `IQueryable`.

**🏗️ Arquitectura**
- [ ] **Controller delgado, Service grueso**
    - Controller no debe tener lógica de negocio (> 5 líneas de lógica es sospechoso).
    - Controller solo mapea DTOs y llama servicios.
- [ ] **Inyección de Dependencias correcta (Constructor)**
    - No usar `new Service()`.
    - No usar `ServiceLocator`.
- [ ] **ViewModels usados en lugar de ViewBag**
    - Verificar que las vistas sean fuertemente tipadas (`@model`).

**⚡ Performance**
- [ ] **`Any()` en lugar de `Count() > 0`**
    - Verificar condiciones de existencia.
- [ ] **`StringBuilder` para strings complejos**
    - Verificar concatenaciones en bucles.
- [ ] **`HttpClientFactory` usado**
    - Verificar que no se cree `new HttpClient()` en cada request.

---

#### 🐘 4.3 PHP / WordPress / Laravel

> **Objetivo**: Asegurar código seguro, performante y compatible con estándares de WordPress y Modern PHP.

##### 🚨 Bloqueantes (P0) - Seguridad Crítica

**1. Output Escaping (XSS)**
- ❌ **Imprimir variables sin escapar**.
    - ❌ `echo $variable;`
    - ✅ `echo esc_html($variable);` (Texto simple)
    - ✅ `echo esc_attr($variable);` (Atributos HTML)
    - ✅ `echo esc_url($variable);` (URLs)
    - ✅ `echo wp_kses_post($variable);` (HTML permitido)

**2. Input Sanitization**
- ❌ **Guardar datos de usuario sin sanitizar**.
    - ❌ `update_option('mi_opt', $_POST['val']);`
    - ✅ `update_option('mi_opt', sanitize_text_field($_POST['val']));`
    - ✅ `sanitize_email()`, `absint()`, `sanitize_key()`.

**3. Database Security (SQL Injection)**
- ❌ **SQL directo con variables concatenadas**.
    - ❌ `$wpdb->query("SELECT * FROM $table WHERE id = $id");`
    - ✅ **SIEMPRE usar `prepare()`**:
        ```php
        $wpdb->query($wpdb->prepare(
            "SELECT * FROM %i WHERE id = %d",
            $table, $id
        ));
        ```

**4. Verificación de Intención (CSRF)**
- ❌ **Procesar formularios sin verificar Nonce**.
    - ✅ Input: `<?php wp_nonce_field('accion_guardar', 'mi_nonce'); ?>`
    - ✅ Check:
        ```php
        if (!isset($_POST['mi_nonce']) || !wp_verify_nonce($_POST['mi_nonce'], 'accion_guardar')) {
            wp_die('Seguridad fallida');
        }
        ```

**5. Permisos y Capabilities**
- ❌ **Acciones administrativas sin verificar usuario**.
    - ✅ **SIEMPRE**:
        ```php
        if (!current_user_can('manage_options')) {
            return;
        }
        ```

**6. Ejecución Arbitraria**
- ❌ **`eval()`, `exec()`, `system()`**. (Prohibido terminantemente).
- ❌ **`include($user_input)`**. (LFI vulnerability).

---

##### ⚠️ Importantes (P1) - Arquitectura y Performance

**1. Performance en Queries**
- ❌ **Queries dentro de loops (N+1)**.
    - Evitar `get_post_meta()` en bucles largos si no está cacheado.
    - ✅ Usar `WP_Query` eficiente con `no_found_rows => true` si no se necesita paginación.
    - ✅ Usar `fields => 'ids'` si solo se necesitan IDs.

**2. Manejo de Assets (JS/CSS)**
- ❌ **Hardcodear `<script>` o `<link>` en header/footer**.
- ✅ **Usar `wp_enqueue_script` y `wp_enqueue_style`**.
    - ✅ Definir dependencias correctamente (ej: `array('jquery')`).
    - ✅ Usar versión dinámica (ej: `filemtime()`) para cache busting.

**3. Transients y Caching**
- ⚠️ **Operaciones pesadas (API calls) en cada carga**.
- ✅ **Usar Transients**:
    ```php
    $data = get_transient('mi_api_data');
    if (false === $data) {
        $data = remote_call();
        set_transient('mi_api_data', $data, 12 * HOUR_IN_SECONDS);
    }
    ```

**4. Validación de Retorno**
- ⚠️ **`wp_remote_get` sin validar error**.
    - ✅ Verificar `is_wp_error($response)` antes de usar `wp_remote_retrieve_body($response)`.

---

##### ℹ️ Mejoras (P2/P3) - Estándares WP

**1. Prefijos (Namespacing)**
- ⚠️ **Funciones genéricas** (`function guardar()`).
- ✅ **Usar prefijo único**: `function cliente_proyecto_guardar()`.

**2. Hooks vs Modificación Directa**
- ℹ️ **No editar archivos del Core o Plugins de terceros**.
- ✅ Usar `add_action` y `add_filter`.

**3. Internacionalización (i18n)**
- ℹ️ **Strings hardcodeados**.
- ✅ Usar `__('Texto', 'text-domain')` o `esc_html_e('Texto', 'text-domain')`.

**4. Estilo de Código (PSR-12 / WP Standards)**
- ℹ️ Espacios alrededor de paréntesis.
- ℹ️ `Yoda conditions` (WordPress standard): `if (true === $val)` (Opcional pero recomendado en WP).

---

##### 📋 Checklist Específico PHP/WordPress

> Verificar cada punto antes de aprobar:

**🔒 Seguridad (Obligatorio)**
- [ ] Output Escaping (`esc_html`, `esc_attr`) usado consistente?
- [ ] Input Sanitization (`sanitize_*`) al guardar?
- [ ] Nonces verificados en POST/Ajax?
- [ ] Capabilities checaneadas (`current_user_can`)?
- [ ] SQL Preprared Statements (`$wpdb->prepare`)?

**🏗️ Arquitectura**
- [ ] Assets encolados con `wp_enqueue_*`?
- [ ] Prefijos en funciones/clases?
- [ ] No lógica compleja en templates (vistas)?
- [ ] Uso de Child Theme (si aplica)?

**⚡ Performance**
- [ ] No queries en loops?
- [ ] Transients para datos externos?
- [ ] Imágenes optimizadas/lazy load?

---
#### �📝 4.4 Handlebars/Razor Templates

- ❌ **Tildes directas en texto visible** - Usar entidades HTML: `opci&oacute;n` en lugar de `opción`, `a&ntilde;o` en lugar de `año`

---

#### ⚛️ 4.5 JavaScript / React / Next.js

##### Reglas Específicas JavaScript/React (P0/P1)

> 🚨 Las reglas generales P0-P3 (Paso 3) YA aplican a JS. Aquí solo reglas EXCLUSIVAS de JS/React.

- ❌ **`eval()` o `new Function()`** con inputs externos (P0)
- ❌ **`dangerouslySetInnerHTML`** sin sanitización (P0)
- ❌ **Destructuring sin defaults** - Usar `const { prop = defaultValue } = obj || {}` (P0)
- ❌ **State mutation directa** - Usar `setState` o spread operator, no mutar directamente (P0)

**Ejemplo de validación de parámetros en queries:**
```typescript
// ❌ MAL - Parámetro usado sin validar
const filter = {
  $match: {
    $expr: { $eq: ['$campaign', campaign] }  // Si campaign es null/undefined, falla
  }
}

// ✅ BIEN - Con validación
if (!campaign) {
  throw new Error('Campaign parameter is required');
}
const filter = {
  $match: {
    $expr: { $eq: ['$campaign', campaign] }
  }
}
```

##### 🤖 IA-Compatible Checks (JavaScript/React) (OBLIGATORIO)
- ❌ **useEffect sin cleanup** - Retornar función de limpieza para subscriptions/timers (P1)
- ❌ **Array.find() sin validar null** - El resultado puede ser `undefined` (P1)
- ⚠️ **Comparación con `==` en lugar de `===`** - Usar comparación estricta (P2)
- ⚠️ **Falta de key en listas** - Elementos de lista sin prop `key` único (P1)
- ⚠️ **Promise sin catch** - Promises sin manejo de errores (P1)

##### 📋 Checklist Específico JavaScript/React

> Verificar cada punto antes de aprobar:

**🛡️ Robustez (React)**
- [ ] **useEffect con cleanup**
    - Verificar suscripciones, timers (`setInterval`), o listeners en `window`.
    - Asegurar que retornen función de limpieza.
- [ ] **Promise.all con manejo de errores**
    - Verificar que se use `.catch` o `try/catch`.
    - Si una promesa falla, `Promise.all` falla completamente.
- [ ] **Verificar componente montado en async**
    - No setear estado si el componente se desmontó (anti-pattern).
    - Usar flag `isMounted` o cancelar suscripción.
- [ ] **Sin datos sensibles en LocalStorage**
    - Tokens, passwords, PII no deben ir en `localStorage` o `sessionStorage`.

**📐 Calidad (JS)**
- [ ] **PropTypes o TypeScript types**
    - Props deben estar tipadas. No usar `any`.
    - `isRequired` para props obligatorias.
- [ ] **Imports ordenados**
    - 1. Librerías externas (React, lodash).
    - 2. Componentes internos.
    - 3. Estilos/Assets.
- [ ] **Desestructuración con defaults**
    - `const { id } = user || {}`. Evitar crash por null.

**🏗️ Arquitectura (React)**
- [ ] **Componentes con responsabilidad única**
    - Separar Container (Lógica) de Presentational (UI).
    - Componentes de más de 200 líneas deben dividirse.
- [ ] **Custom hooks para lógica reutilizable**
    - Extraer lógica compleja de `useEffect` a `useHook`.
- [ ] **Feature flags bien implementados**
    - Verificar que el código nuevo esté detrás de flag si se requiere.
- [ ] **ErrorBoundary para componentes críticos**
    - Envolver widgets complejos en ErrorBoundary.

**⚡ Performance (React)**
- [ ] **React.memo, useMemo, useCallback donde corresponda**
    - Usar solo si hay problemas de renderizado o para estabilidad de referencias.
    - No abusar (optimización prematura).
- [ ] **Lazy loading de componentes/imágenes**
    - `React.lazy` para rutas o modales pesados.
    - `next/image` con tamaños definidos.
- [ ] **Sin re-renders innecesarios**
    - Verificar dependencias de `useEffect`.
    - Verificar objetos creados en cada render pasados como props.

---

---

---

#### 🗄️ 4.6 SQL / Base de Datos

> **Compatibilidad**: SQL Server 2016+, Azure SQL Database
> **Objetivo**: Garantizar integridad, performance y mantenibilidad de la base de datos.

##### 🚨 Bloqueantes (P0) - Seguridad e Integridad

**1. Scripts NO Idempotentes (Re-runnable)**
- ❌ **Script que falla si se corre 2 veces**
- ✅ **Solución**: Usar siempre `IF NOT EXISTS` o `IF EXISTS` antes de crear/borrar objetos.
- **Ejemplo Correcto**:
    ```sql
    IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'MiTabla' AND schema_id = SCHEMA_ID('dbo'))
    BEGIN
        CREATE TABLE dbo.MiTabla (...)
    END
    ```

**2. Manipulación de Datos sin WHERE**
- ❌ **`UPDATE` o `DELETE` sin `WHERE`** explícito.
- ✅ **Solución**: Siempre filtrar. Si es intencional borrar todo, usar `TRUNCATE` (con justificación).

**3. Índices y Performance Crítica**
- ❌ **Creación de índice sin verificación existencia** (Rompe despliegue).
- ❌ **Índice en columna `VARCHAR(MAX)` / `NVARCHAR(MAX)`** (No permitido como key).
- ❌ **Indices duplicados** (Mismas columnas, mismo orden).

**4. Modificación de Esquema (DDL)**
- ❌ **`ALTER TABLE` agregando columna `NOT NULL` sin `DEFAULT`** (Falla si hay datos).
    - ✅ Usar `DEFAULT` o agregar como `NULL`, llenar datos, y luego alterar a `NOT NULL`.
- ❌ **`DROP COLUMN` sin validar dependencias** (SPs, Vistas, FKs que la usan).
- ❌ **Inconsistencia en `INSERT` tras cambios de esquema**.
    - Si se agrega una columna a una tabla (ALTER) o tabla temporal (`#Tabla`), **TODOS** los `INSERT` que impactan esa tabla deben actualizarse.
    - ⚠️ Especial cuidado con `INSERT INTO #Tabla EXEC sp_...` (Falla si las columnas no coinciden exactamente).

**5. Seguridad**
- ❌ **`GRANT ALL` o `GRANT CONTROL`**. Dar solo permisos necesarios (`SELECT`, `INSERT`, `UPDATE`, `EXECUTE`).
- ❌ **SQL Dinámico (`EXEC(@sql)`)** sin usar `sp_executesql` con parámetros (Riesgo Injection).
- ❌ **Uso de `xp_cmdshell` o acceso a disco**.

---

##### ⚠️ Importantes (P1) - Performance y Estándares

**1. Patrones de Consulta (Anti-patterns)**
- ⚠️ **`SELECT *`** - Trae columnas innecesarias, rompe si cambia esquema, desperdicia I/O.
    - ✅ Listar columnas explícitamente: `SELECT Id, Nombre FROM ...`
- ⚠️ **Funciones en el WHERE (Non-SARGable)**
    - ❌ `WHERE YEAR(Fecha) = 2024` (Escanea toda la tabla).
    - ✅ `WHERE Fecha >= '2024-01-01' AND Fecha < '2025-01-01'` (Usa índice).
- ⚠️ **`LIKE '%texto%'`** (Wildcard al inicio) - No usa índice. Evitar si es posible.
- ⚠️ **Conversión Implícita de Tipos**
    - ❌ Comparar `VARCHAR` con `NVARCHAR` (causa Scan).
    - ✅ Asegurar que tipos de datos de variables coincidan con columnas.

**2. Manejo de Transacciones**
- ⚠️ **Lógica compleja sin Transacción**.
- ✅ Usar bloque `TRY...CATCH` con `TRANSACTION`:
    ```sql
    BEGIN TRY
        BEGIN TRAN
            -- Operaciones
        COMMIT TRAN
    END TRY
    BEGIN CATCH
        IF @@TRANCOUNT > 0 ROLLBACK TRAN
        THROW;
    END CATCH
    ```

**3. Índices Faltantes (Foreign Keys)**
- ⚠️ **FK sin Índice de soporte**.
    - Causa Deadlocks en operaciones de borrado del padre.
    - Causa Table Scans en Joins.
    - ✅ Crear índice no clustered en columnas FK.

**4. Naming Conventions**
- ⚠️ **Nombres no estándar**.
    - Tablas: `PascalCase`, Plural (ej: `Usuarios`, `OrdenesDetalle`).
    - Columnas: `PascalCase` (ej: `FechaCreacion`, `EsActivo`).
    - PK: `PK_NombreTabla`.
    - FK: `FK_TablaOrigen_TablaDestino_Columna`.
    - IX: `IX_Tabla_Columnas`.
    - SP: `usp_AccionEntidad` (ej: `usp_GetUsuarioById`).

---

##### ℹ️ Mejoras (P2/P3) - Mantenibilidad

- ℹ️ **Comentarios**: Encabezado del script con Autor, Fecha, Descripción.
- ℹ️ **Indentación**: SQL legible, palabras clave alineadas.
- ℹ️ **Alias**: Usar alias significativos (`u` para `Usuarios`, no `a`, `b`).
- ℹ️ **`SET NOCOUNT ON`**: Al inicio de SPs para evitar ruido de red.

---

##### 📋 Checklist Específico SQL

> Verificar cada punto antes de aprobar:

**DDL (Estructura)**
- [ ] **Scripts Idempotentes (`IF NOT EXISTS`)**
    - Verificar que el script pueda correrse N veces sin error.
    - Usar guardas para `CREATE`, `DROP`, `ALTER`.
- [ ] **Nombres según convención**
    - `PascalCase` para tablas y columnas.
    - Prefijos claros (`PK_`, `FK_`, `IX_`, `usp_`).
- [ ] **Tipos de datos adecuados**
    - Evitar `NVARCHAR(MAX)` si el dato es corto (ej: Código de 10 chars).
    - Usar `DATE` en lugar de `DATETIME` si no se requiere hora.
- [ ] **PKs y FKs definidas**
    - Todas las tablas deben tener Primary Key.
    - Relaciones deben tener Foreign Keys explícitas.
- [ ] **Consistencia en INSERTs tras DDL**
    - Si se agregó/quitó una columna, verificar que TODOS los `INSERT` (incluyendo a tablas temporales) se hayan actualizado.

**DML (Datos)**
- [ ] **`INSERT` especifica columnas**
    - NUNCA `INSERT INTO Table VALUES (...)`.
    - SIEMPRE `INSERT INTO Table (Col1, Col2) VALUES (...)`.
- [ ] **`MERGE` usado correctamente**
    - Validar cláusulas `WHEN MATCHED` y `WHEN NOT MATCHED`.
    - Asegurar `;` al final (obligatorio en MERGE).
- [ ] **Filtros `WHERE` sargables**
    - No usar funciones en columnas indexadas dentro del `WHERE`.

**Performance**
- [ ] **No hay `SELECT *`**
    - Verificar proyección de columnas necesarias.
- [ ] **No hay n+1 queries (en lógica de SP)**
    - Evitar cursores o loops `WHILE` para operaciones de set.
- [ ] **Índices creados para queries frecuentes**
    - Verificar si las columnas de `WHERE` y `JOIN` tienen índices.
- [ ] **Plan de ejecución revisado (si aplica)**
    - Para scripts críticos, verificar Costo Estimado.

**Seguridad**
- [ ] **Sin inyección SQL**
    - No concatenar strings en `EXEC`. Usar `sp_executesql`.
- [ ] **Permisos mínimos**
    - No usar `GRANT ALL`. Dar solo `EXECUTE` o `SELECT/INSERT/UPDATE`.
- [ ] **Datos sensibles encriptados/hasheados si aplica**
    - Passwords, PII, Tarjetas deben estar protegidos.

# 📊 SECCIÓN 3: CALCULAR PUNTUACIÓN Y VEREDICTO

### Paso 5: 🚫 GATE A - Encoding

> **🚨 CRÍTICO**: Si el código nuevo contiene caracteres especiales (á, ñ, etc.) y el archivo NO está en UTF-8, reportar como P1.

> **⚠️ ADVERTENCIA — FALSO POSITIVO POR TERMINAL RENDERING**: El diff obtenido vía CLI (`gh pr diff`) puede mostrar caracteres UTF-8 como secuencias corruptas (ej: `campa├▒a` en lugar de `campaña`, `┬┐` en lugar de `¿`) debido al encoding del terminal o del buffer de PowerShell. **Esto NO significa que el archivo fuente esté mal codificado.**
>
> **ANTES de reportar un problema de encoding, la IA DEBE verificar:**
> 1. ¿El carácter problemático está en una línea `+` del diff (código nuevo) o solo en el output de la terminal?
> 2. Consultar el contexto del PR: si el archivo está guardado en UTF-8 y el resto del proyecto usa UTF-8, asumir que los caracteres son correctos.
> 3. Solo reportar como P1 si hay evidencia concreta (ej: el diff muestra secuencias de escape literales como `\u00f1` donde debería haber `ñ`, o bytes BOM como `´╗┐` al inicio del archivo).
>
> **Ejemplo de falso positivo real**: La terminal muestra `'campa├▒a': campania` pero el código fuente real es `'campaña': campania` (UTF-8 correcto). No reportar.

---

### Paso 6: Clasificar PR (Tamaño y Riesgo)

**TAMAÑO:**
- **XS**: 0-10 líneas / Docs / Typo
- **S**: 10-50 líneas / Bugfix simple
- **M**: 50-200 líneas / Feature pequeña
- **L**: 200-500 líneas / Refactor
- **XL**: 500+ líneas / Feature mayor (⚠️ REQUIERE MAYOR ATENCIÓN Y HAY QUE REVISARLO BIEN TAMBIEN MENCIONARLE AL USUARIO QUE LE DE UNA REVISION MAS DETALLADA)

**RIESGO:**
- **Bajo**: Docs, CSS, Tests, UI Text
- **Medio**: Lógica de negocio, Validaciones, UI Logic
- **Alto**: Auth, Pagos, DB Schema, Core Architecture, Seguridad
- **Crítico**: Credentials, PII, Encryption, Money handling

---

### Paso 7: Detectar nuevas dependencias

> Al analizar el PR, buscar archivos que indican nuevas dependencias:

| Archivo | Lenguaje | Acción |
|---------|----------|--------|
| `*.csproj` | C# | Verificar nuevos `<PackageReference>` |
| `build.gradle` / `build.gradle.kts` | Kotlin/Android | Verificar `implementation`/`api` |
| `package.json` | JS/Node | Verificar `dependencies` |
| `composer.json` | PHP | Verificar `require` |

**Template de comentario si se detectan dependencias:**
```markdown
### 📦 Nuevas Dependencias Detectadas
> Se agregaron las siguientes dependencias. Verificar licencias y necesidad.
- `NombrePaquete` (v1.0.0)
```

---

### Paso 8: Cálculo del Score

> **Fórmula:** Promedio Ponderado de 6 Categorías

El puntaje final (0-100) es la suma de: `(Puntaje Categoría * Peso) / 5 * 100`

| Categoría | Peso | Descripción |
|-----------|------|-------------|
| **Funcionalidad** | 20% | ¿El código hace lo que debe? ¿Hay bugs lógicos? |
| **Robustez** | 20% | Manejo de errores, null safety, edge cases, seguridad. |
| **Mantenibilidad** | 20% | Legibilidad, nombres claros, tamaño de funciones, DRY. |
| **Testabilidad** | 15% | ¿Es fácil de testear? ¿Separación de concerns? |
| **Arquitectura** | 15% | ¿Respeta capas/patrones? ¿Uso correcto de framework? |
| **Escalabilidad** | 10% | Performance, eficiencia de recursos. |

**Guía de Puntuación por Categoría (0-5):**
- **5 (Excelente)**: Sin issues relevantes. Código ejemplar.
- **4 (Bueno)**: Algunos issues menores (P3) o sugerencias de mejora (P2).
- **3 (Aceptable)**: Varios issues P2 o algún P1 menor. Requiere cambios no bloquantes.
- **2 (Deficiente)**: Issues importantes (P1) o un P0 aislado fácil de corregir.
- **1 (Pobre)**: Múltiples P1 o P0s. Código frágil o inseguro.
- **0 (Crítico)**: Bloqueantes severos, código inoperable o riesgo de seguridad alto.

> **Regla de Consistency**: Si se resta puntaje en una categoría, DEBE haber un issue reportado que lo justifique.

---

### Paso 9: Generar Comentario del Review

> **Template OBLIGATORIO**: Copiar y pegar este formato exacto.

```markdown
# 🤖 Code Review - PR #{PR_NUMBER}

📊 **Puntaje General**: {SCORE}/100 ⭐⭐⭐
Veredicto: **{VERDICT ICON} {VERDICT}**

## 📈 Desglose de Puntaje
| Categoría | Puntaje | Peso | Contribución |
|-----------|---------|------|--------------|
| Funcionalidad | X/5 | 20% | X.X |
| Robustez | X/5 | 20% | X.X |
| Mantenibilidad | X/5 | 20% | X.X |
| Testabilidad | X/5 | 15% | X.X |
| Escalabilidad | X/5 | 10% | X.X |
| Arquitectura | X/5 | 15% | X.X |
| **TOTAL** | | | **{SCORE}/100** |

> **Classification**: {SIZE} / {RISK} Risk
> **Reviewer**: AI Auditor (FPININ)
> **Stack Detectado**: {LENGUAJE} ({EXTENSIONES})
> **Desarrollador**: {DEVELOPER}
> **Fecha**: {DATE}
> **¿Listo para fusionar?**: {SI/NO/PODRIA MEJORAR}

*(Incluir solo si es RE-REVIEW)*
## 🔍 Seguimiento de Issues Anteriores
- ✅ **CORREGIDO**: [Título Issue] ({CATEGORIA} +{X} pts). {Explicación breve de la solución}.
- ❌ **PENDIENTE**: [Título Issue] (-{X} pts {CATEGORIA}).

---

## 🔴 Problemas Críticos (P0 - Blocking)

> *Issues de seguridad, crashes, o violaciones de reglas estrictas. Deben corregirse obligatoriamente.*

1. **[Título Breve del Error]**
   - **Archivo**: `{ARCHIVO}` línea {LINEA}
   - **Problema**: {DESCRIPCIÓN_DETALLADA}
   - **Riesgo**: {IMPACTO_NEGATIVO}
   - **Impacto en score**: {X} puntos
   - **Tiempo est.**: {X} minutos
   - **Categoria afectada**: {CATEGORIA}
   - **Código actual**:

     ```
     {CODIGO_CON_ERROR}
     ```

   - **Solución sugerida**: {DESCRIPCION_DE_LA_SOLUCION}

     ```
     {CODIGO_CORREGIDO}
     ```

*(Si no hay issues P0, escribir: "Ninguno ✅")*

---

## 🟡 Problemas Importantes (P1 - High Priority)
> *Problemas de performance, bugs probables o deuda técnica alta.*

1. **[Título Breve del Error]**
   - **Archivo**: `{ARCHIVO}` línea {LINEA}
   - **Problema**: {DESCRIPCIÓN}
   - **Impacto**: {CONSECUENCIA E IMPACTO}
   - **Impacto en score**: {X} puntos
   - **Tiempo est.**: {X} minutos
   - **Categoria afectada**: {CATEGORIA}
   - **Código actual**:

     ```
     {CODIGO_CON_ERROR}
     ```

   - **Solución sugerida**: {DESCRIPCION_DE_LA_SOLUCION}

     ```
     {CODIGO_CORREGIDO}
     ```

*(Si no hay issues P1, escribir: "Ninguno ✅")*

---

## 🟢 Recomendaciones (P2/P3 - Medium/Low)
> *Mejoras de mantenibilidad y buenas prácticas. (Ver Regla 3.3: NO sugerir comentarios/docs/tests extra)*

**P2 - Medium Priority:**

1. 💡 **[Sugerencia]** ...
   - **Archivo**: `{ARCHIVO}` línea {LINEA}
   - **Impacto en score**: {X} puntos
   - **Tiempo est.**: {X} minutos
   - **Categoria afectada**: {CATEGORIA}
   - **Código actual**:

     ```
     {CODIGO_CON_ERROR}
     ```

   - **Solución sugerida**: {DESCRIPCION_DE_LA_SOLUCION}

     ```
     {CODIGO_CORREGIDO}
     ```

2. 💡 **[Sugerencia]** ...
   - **Archivo**: `{ARCHIVO}` línea {LINEA}
   - **Impacto en score**: {X} puntos
   - **Tiempo est.**: {X} minutos
   - **Categoria afectada**: {CATEGORIA}
   - **Código actual**:

     ```
     {CODIGO_CON_ERROR}
     ```

   - **Solución sugerida**: {DESCRIPCION_DE_LA_SOLUCION}

     ```
     {CODIGO_CORREGIDO}
     ```

**P3 - Low Priority:**

1. ℹ️ **[Nitpick/Formato]** ... **¿Bueno o malo?**
   - **Archivo**: `{ARCHIVO}` línea {LINEA}
   - **Impacto en score**: {X} puntos
   - **Tiempo est.**: {X} minutos
   - **Categoria afectada**: {CATEGORIA}
   - **Código actual**:

     ```
     {CODIGO_CON_ERROR}
     ```

   - **Solución sugerida**: {DESCRIPCION_DE_LA_SOLUCION}

     ```
     {CODIGO_CORREGIDO}
     ```

---

## ✅ Aspectos Positivos
- ✅ [Aspecto 1] (ej. "Excelente manejo de errores en módulo X")
- ✅ [Aspecto 2] (ej. "Refactor del método Y mejoró legibilidad")
- ✅ [Aspecto 3] (ej. "Eliminación de código muerto")
- ✅ [Aspecto 4] (ej. "Variables con nombres descriptivos")

---

## 📋 Plan de Acción
**Antes de mergear (Requerido):**
- [ ] 🔴 Corregir [Issue P0] ({TIEMPO})
- [ ] 🟡 Corregir [Issue P1] ({TIEMPO})

**Este sprint (High priority):**
- [ ] Implementar [Mejora P2 clave]

**Backlog (Low priority):**
- [ ] Revisar [Mejora P3 opcional]

---

## 🎓 Aprendizajes Clave
- ❌ **Evitar**: [Anti-patrón encontrado]
- ✅ **Preferir**: [Buena práctica sugerida]

---

## 📝 Sugerencia de Próximo Commit
{El numero o id del ticket tiene que salir de la rama del PR Y SI NO LO TIENE SUGERIR QUE COLOQUE EN EL COMMIT EL NUMERO DEL TICKET QUE SE ESTA SOLUCIONANDO} ejemplo:
 `[TICKET-123] -FIX {Resumen corto de correcciones P0/P1}`

---

## 📋 Resumen del PR
**¿Qué hace este PR?**
{BREVE_EXPLICACION_FUNCIONAL}

**Archivos principales modificados:**
- [NEW/MODIFY/DELETE] `{ARCHIVO}` - {CAMBIO}

**¿Qué Podria impactar?**
{BREVE_EXPLICACION_DE_QUE_PODRIA_IMPACTAR}

**Impacto:**
- {SEGURIDAD/PERFORMANCE/ARQUITECTURA}

> _Ref: Consultar Apéndice D para referencias oficiales específicas de cada lenguaje_
```

---

# 🚀 SECCIÓN 4: GENERAR MENSAJE PARA GITHUB

### Paso 9: Generar cuerpo del comentario

- Usar formato Markdown
- Usar emojis definidos
- Incluir tabla de score

---

# 💾 SECCIÓN 5: HISTORIAL Y ESTADÍSTICAS

### Paso 10: 🚫 GATE B - Auto-verificación (OBLIGATORIO)

> **Instrucciones**: Antes de "enviar" mentalmente el review, la IA debe hacerse estas preguntas.
> Si la respuesta es "NO" a alguna, detenerse y corregir.

1. **¿He verificado TODAS las reglas P0 del Paso 3?** [SÍ/NO]
2. **¿He calculado bien el promedio ponderado (∑ peso*puntaje)?** [SÍ/NO]
3. **¿El veredicto coincide con el score?** [SÍ/NO]
   - < 70 o cualquier P0 = REQUEST CHANGES
   - 70-89 = COMMENT
   - >= 90 = APPROVE
4. **¿He incluido la tabla de puntuación al inicio?** [SÍ/NO]
5. **¿He consultado las referencias del Apéndice D?** [SÍ/NO]

---

### Paso 11: Publicar Review en GitHub

> **🚨 REQUIERE APROBACIÓN DEL USUARIO**: Este es el ÚNICO paso que requiere permiso explícito.
> La IA DEBE mostrar el review al usuario y esperar confirmación antes de ejecutar este comando.
> Todos los demás pasos (historial, archivado, limpieza) se ejecutan automáticamente.

> **🚨 CRÍTICO — HEREDOC EN POWERSHELL**:
> **SIEMPRE** usar `@'...'@` (comillas simples) para escribir el contenido markdown.
> **NUNCA** usar `@"..."@` (comillas dobles).
> En PowerShell, el heredoc de comillas dobles interpreta el backtick `` ` `` como carácter de escape,
> convirtiendo ` ``` ` en un solo `` ` `` y rompiendo todos los bloques de código en GitHub.

```powershell
# 1. Definir encoding para evitar errores de caracteres
$OutputEncoding = [System.Text.Encoding]::UTF8
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

# 2. Guardar comentario en archivo temporal seguro
# ⚠️ USAR @'...'@ (SIMPLE) — NUNCA @"..."@ (DOBLE): los backticks se dañan con comillas dobles
$commentFile = "$env:TEMP\pr_review_comment.md"
@'
{CONTENIDO_MARKDOWN_DEL_PASO_9}
'@ | Set-Content -Path $commentFile -Encoding UTF8

# 3. Determinar evento (approve, request-changes, comment)
$event = "{EVENTO_SEGÚN_SCORE}"

# 3.1 Evitar error "Cannot approve your own pull request"
$prAuthor = (gh pr view {PR_NUMBER} --repo {NOMBRE_USUARIO}/{REPO_NAME} --json author --jq .author.login)
$currentUser = (gh api user -q .login)
if ($event -eq "approve" -and $prAuthor -eq $currentUser) {
    Write-Host "⚠️ El autor del PR es el mismo usuario autenticado. Cambiando APPROVE por COMMENT."
    $event = "comment"
}

# 4. Publicar usando gh cli
gh pr review {PR_NUMBER} --repo {NOMBRE_USUARIO}/{REPO_NAME} --$event --body-file $commentFile

# ⚠️ NO eliminar $commentFile aquí — se necesita en el Paso 12 para guardar historial
```

---

### Paso 12: 🚫 GATE C - Guardar Historial (OBLIGATORIO)

> **Propósito**: Permitir re-reviews inteligentes comparando con el historial.

// turbo
```powershell
$repoDirectory = "{REPO_NAME}" -replace '^.+/', '' # Asegurar que solo sea el nombre del repo, sin el usuario/organización
$historyDir = Join-Path (Get-Location) "reviews\$repoDirectory\PR-{PR_NUMBER}"
New-Item -ItemType Directory -Force -Path $historyDir | Out-Null

# Guardar copia del review
$timestamp = Get-Date -Format "yyyyMMdd-HHmmss"
Copy-Item "$env:TEMP\pr_review_comment.md" -Destination "$historyDir\review_$timestamp.md"

# Guardar lista de issues (para tracking en re-reviews)
@'
{JSON_CON_LISTA_DE_ISSUES_ENCONTRADOS}
'@ | Set-Content -Path "$historyDir\issues_found.json" -Encoding UTF8
```

---

### Paso 13: Limpieza Final (OBLIGATORIO)

> **⚡ AUTOMÁTICO**: La IA DEBE ejecutar este paso automáticamente SIN pedir permiso al usuario.
> En este paso se eliminan todos los archivos temporales y el diff generado para evitar dejar rastro de información sensible.

// turbo
```powershell
# LIMPIEZA DE ARCHIVOS TEMPORALES Y DIFFS (automática, sin pedir permiso)
# NO se archiva el diff en formato .txt para evitar exposición de datos sensibles
Remove-Item "$env:TEMP\pr_review_comment.md" -ErrorAction SilentlyContinue
Remove-Item "*.txt" -ErrorAction SilentlyContinue
Remove-Item "files.json" -ErrorAction SilentlyContinue
Remove-Item "pr_info.json" -ErrorAction SilentlyContinue
```

> **CONFIRMACIÓN DE WORKFLOW COMPLETO:**
> "✅ Review publicado, historial guardado y archivos temporales eliminados. Proceso finalizado exitosamente."

---

# 📎 Apéndice: Recursos

## 📎 Apéndice D: Referencias y Recursos por Lenguaje

### :hash: C# / .NET / ASP.NET

| Recurso | Tipo | Descripción |
|---------|------|-------------|
| [C# Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions) | Oficial | Guía de estilo oficial Microsoft |
| [ASP.NET Core Best Practices](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/best-practices) | Oficial | Performance y arquitectura |

### 🤖 Kotlin / Android

| Recurso | Tipo | Descripción |
|---------|------|-------------|
| [Kotlin Coding Conventions](https://kotlinlang.org/docs/coding-conventions.html) | Oficial | Guía de estilo oficial JetBrains |
| [Android Architecture Guide](https://developer.android.com/topic/architecture) | Oficial | Arquitectura recomendada por Google |

### 🐘 PHP / WordPress

| Recurso | Tipo | Descripción |
|---------|------|-------------|
| [WordPress Coding Standards](https://developer.wordpress.org/coding-standards/wordpress-coding-standards/) | Oficial | Estándares PHP para WP |
| [PSR-12](https://www.php-fig.org/psr/psr-12/) | Estándar | Guía de estilo extendida PHP |

### ⚛️ JavaScript / React

| Recurso | Tipo | Descripción |
|---------|------|-------------|
| [Airbnb JS Style Guide](https://github.com/airbnb/javascript) | Comunitario | Estándar de facto para JS |
| [React Documentation](https://react.dev/learn) | Oficial | Best practices modernas (Hooks) |

### 🗄️ SQL

| Recurso | Tipo | Descripción |
|---------|------|-------------|
| [SQL Style Guide](https://www.sqlstyle.guide/) | Comunitario | Convenciones de formato SQL |
| [Use The Index Luke](https://use-the-index-luke.com/) | Performance | Guía de indexación y performance |

### :atom: JavaScript / React

| Recurso | Tipo | Descripción |
|---------|------|-------------|
| [Airbnb JS Style Guide](https://github.com/airbnb/javascript) | Comunitario | Estándar de facto para JS |
| [React Documentation](https://react.dev/) | Oficial | Documentación moderna de React |
| [Next.js Best Practices](https://nextjs.org/docs/pages/building-your-application/routing/middleware) | Oficial | Guías de Next.js |
| [Clean Code JavaScript](https://github.com/ryanmcdermott/clean-code-javascript) | Repo | Adaptación de Clean Code a JS |

### :closed_book: Libros Recomendados (Todos los lenguajes)

| Libro | Autor | Tema |
|-------|-------|------|
| **Clean Code** | Robert C. Martin | Estilo y legibilidad |
| **Refactoring** | Martin Fowler | Mejorar código existente |
| **Pragmatic Programmer** | Hunt & Thomas | Mindset profesional |

---

# 🛠️ Apéndice E: Common Fixes (Guía de Corrección)

### 🤖 Kotlin / Android Fixes

#### Kotlin: Null Safety (`!!` vs `?.`)
```diff
// ❌ MAL - Riesgo de Crash
- val name = user!!.name
- val len = name.length

// ✅ BIEN - Safe Call + Elvis
+ val name = user?.name ?: "Guest"
+ val len = name.length
```

#### Kotlin: Coroutines (Main vs IO)
```diff
// ❌ MAL - Network en Main Thread (ANR)
- fun loadData() {
-     val data = api.getData() // Bloquea UI
-     show(data)
- }

// ✅ BIEN - Suspend + Dispatchers.IO
+ fun loadData() = viewModelScope.launch {
+     val data = withContext(Dispatchers.IO) {
+         api.getData()
+     }
+     show(data)
+ }
```

#### Compose: State Hoisting
```diff
// ❌ MAL - ViewModel dentro de componente UI (Difícil de testear/reusar)
- @Composable fun UserScreen(viewModel: UserViewModel = hiltViewModel()) {
-     val user by viewModel.user.collectAsState()
-     Text(user.name)
- }

// ✅ BIEN - Pasar estado y eventos
+ @Composable fun UserScreen(
+     user: User,
+     onEvent: (UserEvent) -> Unit
+ ) {
+     Text(user.name)
+ }
```

---

### 🐘 PHP / WordPress Fixes

#### PHP: SQL Injection Prevention
```diff
// ❌ MAL - Interpolación directa
- $wpdb->query("SELECT * FROM table WHERE id = $id");

// ✅ BIEN - Prepared Statement
+ $wpdb->query($wpdb->prepare(
+     "SELECT * FROM table WHERE id = %d",
+     $id
+ ));
```

#### PHP: XSS Prevention (Output Escaping)
```diff
// ❌ MAL - Confiar en el dato
- echo '<a href="' . $url . '">' . $text . '</a>';

// ✅ BIEN - Escapar todo output
+ echo '<a href="' . esc_url($url) . '">' . esc_html($text) . '</a>';
```

#### PHP: Nonce Verification
```diff
// ❌ MAL - Procesar form sin verificar origen
- if ($_POST['action'] == 'save') {
-     save_data();
- }

// ✅ BIEN - Verificar Nonce
+ if ($_POST['action'] == 'save' && check_admin_referer('save_action', 'nonce_field')) {
+     save_data();
+ }
```

---

### 🗄️ SQL Fixes

#### SQL: Idempotency (Re-runnable Scripts)
```diff
-- ❌ MAL - Falla si ya existe
- CREATE TABLE dbo.Usuarios (...)

-- ✅ BIEN - Verifica antes de crear
+ IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'Usuarios')
+ BEGIN
+     CREATE TABLE dbo.Usuarios (...)
+ END
```

#### SQL: Performance (SARGable)
```diff
-- ❌ MAL - Función en columna (Index Scan)
- SELECT * FROM Orders WHERE YEAR(OrderDate) = 2024

-- ✅ BIEN - Rango en valor (Index Seek)
+ SELECT * FROM Orders WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01'
```

---

### 💻 C# / .NET Fixes

#### C#: ViewBag seguro
```diff
- bool flag = ViewBag.SomeFlag;
+ bool flag = ViewBag.SomeFlag as bool? ?? false;
```

#### C#: Async correcto
```diff
- var result = asyncMethod().Result;
+ var result = await asyncMethod();
```

#### C#: Consistencia de nombres de parámetros (P1)
```diff
// ❌ MAL - Renombrar parámetros confunde el flujo
- public void ProcessOrder(int orderId, string country) {
-     _repository.Save(orderId, country);
- }
- public void Save(int id, string code) {  // ❌ orderId ≠ id, country ≠ code
-     // ...
- }

// ✅ BIEN - Mantener mismos nombres
+ public void ProcessOrder(int orderId, string countryCode) {
+     _repository.Save(orderId, countryCode);
+ }
+ public void Save(int orderId, string countryCode) {  // ✅ Mismo nombre
+     // ...
+ }
```

#### C#: async void → async Task (P1)
```diff
// ❌ MAL - Excepciones no manejadas, no se puede await
- public async void ProcessData() {
-     await _service.DoWorkAsync();
- }

// ✅ BIEN - Se puede await y manejar excepciones
+ public async Task ProcessDataAsync() {
+     await _service.DoWorkAsync();
+ }
```

#### C#: Dispose sin using (P1)
```diff
// ❌ MAL - Puede dejar recursos abiertos si hay excepción
- var stream = new FileStream(path, FileMode.Open);
- var content = stream.Read(...);
- stream.Close();

// ✅ BIEN - using garantiza dispose
+ using var stream = new FileStream(path, FileMode.Open);
+ var content = stream.Read(...);
// stream se cierra automáticamente
```

---

### ⚛️ JavaScript / React Fixes

#### React: useEffect Cleanup
```diff
// ❌ MAL - Memory leak en suscripción
- useEffect(() => {
-     const sub = dataSource.subscribe();
- }, []);

// ✅ BIEN - Retornar cleanup
+ useEffect(() => {
+     const sub = dataSource.subscribe();
+     return () => sub.unsubscribe();
+ }, []);
```

#### JS: Destructuring Default
```diff
// ❌ MAL - Crash si options es null
- const { title } = options;

// ✅ BIEN - Default object
+ const { title } = options || {};
```

---
**v4.5 - Multi-Language**
