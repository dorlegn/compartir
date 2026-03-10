# Sección 5 — Reconstrucción Design Science del Evidence Snippet

## Protocolo de instrumentación XAI como artefacto de diseño

---

## 0. Encuadre epistemológico: Design Science Research (Hevner et al., 2004)

El Evidence Snippet no es un ejemplo ilustrativo. Es un **artefacto de diseño** cuya construcción debe satisfacer tres ciclos (Hevner et al., 2004):

**Ciclo de Relevancia.** El problema procede del entorno de aplicación: sistemas de IA desplegados en flujos de trabajo clínicos donde la relación output→acción no dispone de instrumentación estructurada. El Epic Sepsis Model (Wong et al., 2021; Michigan Medicine, 2021) documenta cuatro modos de fallo en esa relación. El artefacto debe generar evidencia evaluable en el punto de uso, no post-hoc.

**Ciclo de Diseño.** El artefacto es una arquitectura de registro compuesta por: (a) campos operacionales, (b) campos derivados de XAI, y (c) lógica de decisión por RC. Las reglas de selección de métodos XAI y de construcción del snippet deben ser explícitas y reproducibles.

**Ciclo de Rigor.** La evaluación se apoya en la base de conocimiento: UVC derivadas del corpus (N = 43), criterios de admisibilidad (Jacovi y Goldberg, 2020), y anclajes empíricos independientes (Wong et al., 2021; Michigan Medicine). El artefacto se evalúa por utilidad (genera evidencia que antes no existía), capacidad discriminativa (cada RC produce campos distintos y no redundantes), y reproducibilidad (reglas explícitas permiten replicar la asignación).

---

## 1. Especificación del problema de diseño

### 1.1 Objeto evaluable

La relación **output → acción** en un paso de workflow concreto.

Para el caso demostrativo:

```
EHR data stream → feature assembly → score computation → 
thresholding (≥ 6) → alert emission → workflow action trigger
```

El punto de uso (decision boundary) es el momento entre la emisión de la alerta y la acción clínica. Es ahí donde la evidencia debe existir.

### 1.2 Restricción de diseño: asunción black-box pública

El artefacto asume:
- Sin acceso al código fuente del modelo.
- Sin acceso a datos de entrenamiento.
- Sin acceso a documentación interna del proveedor.
- Acceso disponible: output numérico, inputs al modelo (features EHR), y API de inferencia.

Consecuencia: solo métodos XAI que operen sobre la interfaz input-output son admisibles. Métodos que requieran acceso a gradientes internos (Grad-CAM, LRP, Integrated Gradients) quedan excluidos salvo que exista una capa wrapper proporcionada por el proveedor.

### 1.3 Requisitos de evidencia por RC

Cada RC define un tipo de evidencia que el snippet debe producir:

| RC | Pregunta que responde | Tipo de evidencia requerida | Punto de fallo (UVC primarias) |
|---|---|---|---|
| RC1 — Claim Clarity | ¿A qué responde realmente el output en este caso? | Caracterización funcional: qué features/conceptos dominan la predicción | UVC-01, UVC-05 |
| RC2 — Contextual Validity | ¿Opera el output dentro de su envolvente validada? | Cobertura: distancia al espacio evidenciado, novedad del caso | UVC-01, UVC-02, UVC-03, UVC-06 |
| RC3 — Decision Adequacy | ¿Es la acción robusta ante perturbación plausible? | Robustez: margen al umbral, fragilidad bajo variación operacional | UVC-02 |
| RC4 — Accountability | ¿Puede reconstruirse y contestarse la decisión? | Registro: artefactos loggables, integridad, versionado | UVC-04, UVC-06, UVC-07 |

---

## 2. Protocolo de selección de métodos XAI

### 2.1 Principio de diseño

Los métodos XAI no se seleccionan por su capacidad explicativa general. Se seleccionan por **tres criterios funcionales** derivados de la arquitectura de aseguramiento:

**Criterio 1 — Relevancia para RC.** El método debe producir una señal que sea directamente evaluable contra el requisito de evidencia de al menos una RC. Si no produce una señal RC-relevante, queda excluido independientemente de su calidad técnica.

**Criterio 2 — Utilidad operacional.** La señal debe ser loggable como campo estructurado (no narrativa), computable en el punto de uso (latencia aceptable), y expresable en unidades que permitan umbrales institucionales.

**Criterio 3 — Capacidad discriminativa.** El método debe distinguir entre estados de RC (OK, CONTESTABLE, BLOCKED). Un método que siempre produce el mismo tipo de señal sin variación bajo condiciones diferentes no tiene capacidad discriminativa y no aporta evidencia.

### 2.2 Puerta de admisibilidad (Jacovi y Goldberg, 2020)

Antes de que cualquier artefacto XAI cuente como evidencia, debe pasar una puerta de admisibilidad:

**Fidelidad (faithfulness).** El artefacto debe reflejar el comportamiento real del modelo, no una aproximación plausible pero no verificada. Se operacionaliza como: consistencia entre la atribución y el efecto observado al perturbar las features atribuidas.

**Estabilidad.** Perturbaciones acotadas en el input (ruido de medición, missingness rutinaria) no deben producir cambios cualitativos en el artefacto. Se operacionaliza como: delta de atribución bajo perturbación bounded < umbral institucional.

Artefactos que no superan estas puertas se degradan de "evidencia" a "señal que requiere corroboración". Esta degradación es un campo del snippet.

### 2.3 Reglas explícitas de asignación XAI → RC

Las siguientes reglas determinan qué método XAI se asigna a qué RC. Cada regla es reproducible: dado el mismo caso y las mismas restricciones, la asignación es idéntica.

---

#### RC1 — Claim Clarity: ¿A qué responde el output?

**Necesidad de evidencia:** La organización debe poder declarar a qué referente clínico responde la puntuación de riesgo en este caso concreto. El fallo documentado (Epic): la etiqueta "sepsis risk" persiste pero el output tiene peso evidencial débil (AUC 0.63, PPV ≈12%).

**Regla de selección R1.1 — Atribución agrupada por conceptos (SHAP).**
- Señal producida: vector de atribución por feature, agrupable en dominios clínicos vs. proxy/administrativos.
- Campo del snippet: `concept_group_attribution` = {Lactate: X%, HR_var: Y%, WBC: Z%, ...}; `clinical_domain_fraction` = suma de atribución en features clínicas / atribución total.
- Umbral: si `clinical_domain_fraction` < 0.60, RC1 = CONTESTABLE. Si < 0.40, RC1 = BLOCKED.
- Justificación: RC1 requiere que la organización pueda declarar qué mide el output. Si >40% de la masa de atribución recae en features administrativas (código de facturación, tipo de cama, duración de estancia), la etiqueta "riesgo de sepsis" no es sostenible como claim.
- Compatibilidad black-box: SHAP (KernelSHAP) opera sobre la interfaz input-output. No requiere acceso a gradientes.
- Alternativa admisible: LIME (modelos sustitutos locales). Produce el mismo tipo de señal con menor coste computacional pero menor estabilidad.

**Regla de selección R1.2 — Activación conceptual (TCAV / ConceptSHAP).**
- Señal producida: grado de activación de vectores conceptuales definidos a priori (concepto "sepsis clínica" vs. concepto "complejidad administrativa").
- Campo del snippet: `tcav_sepsis_activation` = valor escalar; umbral de activación definido institucionalmente (ej. 0.60).
- Justificación: SHAP muestra qué features dominan; TCAV verifica si esas features se alinean con el concepto clínico declarado. Son complementarios, no redundantes.
- Restricción: TCAV requiere definir vectores conceptuales con datos de referencia. En el caso Epic, se construyen a partir de cohortes de sepsis confirmada y cohortes de complejidad administrativa sin sepsis.
- Compatibilidad black-box: requiere acceso a representaciones intermedias. Si no están disponibles, TCAV se marca como CANNOT_COMPUTE y RC1 depende solo de R1.1.

**Regla de admisibilidad R1.A — Estabilidad de atribución.**
- Test: perturbar el input con ruido bounded (±1 SD en features continuas; flip de 5% de features binarias). Recalcular SHAP.
- Campo: `attribution_stability_delta` = máxima diferencia en `clinical_domain_fraction` bajo perturbación.
- Umbral: si delta > 0.10, el artefacto SHAP se degrada a "señal no admisible como evidencia primaria".

---

#### RC2 — Contextual Validity: ¿Opera dentro de su envolvente?

**Necesidad de evidencia:** La organización debe poder establecer que el output se aplica dentro del espacio de datos para el que existe evidencia de validez. El fallo documentado (Epic): tasa de alertas inflada de 9% a 21%, volumen +43% contra un descenso de censo del 35%, bajo régimen COVID.

**Regla de selección R2.1 — Cobertura prototípica (ProtoDash / MMD-critic).**
- Señal producida: distancia del caso actual al prototipo más cercano en el espacio de entrenamiento evidenciado.
- Campo del snippet: `prototype_distance` = distancia al prototipo más cercano; `nearest_prototype_id` = identificador del prototipo.
- Umbral: si `prototype_distance` > umbral de envolvente institucional (ej. 0.30), RC2 = CONTESTABLE.
- Justificación: RC2 requiere evidencia de que el caso cae dentro del espacio cubierto por la validación. Un caso sin prototipo cercano opera fuera de la envolvente evidenciada.
- Compatibilidad black-box: ProtoDash opera sobre representaciones de features. No requiere acceso al modelo.

**Regla de selección R2.2 — Funciones de influencia.**
- Señal producida: identificación de las regiones de entrenamiento más influyentes para la predicción actual.
- Campo del snippet: `influence_concentration` = top-5 regiones de entrenamiento y su grado de influencia; `validated_cohort_match` = booleano (las regiones influyentes pertenecen a la cohorte validada o no).
- Justificación: detecta cuándo la base evidencial activa ha migrado desde la población validada hacia regiones no cubiertas.
- Restricción: funciones de influencia requieren acceso a gradientes del loss respecto a parámetros del modelo. Bajo asunción black-box estricta, se marca CANNOT_COMPUTE y RC2 depende solo de R2.1 + telemetría operacional.

**Regla de admisibilidad R2.A — Consistencia temporal.**
- Test: comparar `prototype_distance` del caso actual con la distribución de distancias en las últimas N alertas del mismo sitio.
- Campo: `coverage_trend` = pendiente de la serie de distancias prototípicas.
- Umbral: si la tendencia es creciente por encima de 2 SD de la distribución histórica, se activa señal de boundary degradation.

---

#### RC3 — Decision Adequacy: ¿Es robusta la acción?

**Necesidad de evidencia:** La organización debe poder establecer que la acción (activar alerta) es robusta ante perturbaciones operacionalmente plausibles. El fallo documentado (Epic): umbral fijo (=6) y cap de una alerta por persona y día sin evidencia de robustez.

**Regla de selección R3.1 — Distancia contrafactual (DiCE / Wachter).**
- Señal producida: conjunto mínimo de cambios en features que invertirían la decisión (alert → no alert).
- Campo del snippet: `counterfactual_distance` = número de features y magnitud del cambio; `counterfactual_features` = lista de features y deltas.
- Umbral: si `counterfactual_distance` < umbral de fragilidad (ej. cambio en ≤1 feature dentro de rango de variación clínica normal), RC3 = CONTESTABLE.
- Justificación: RC3 requiere que la acción no dependa de un margen frágil. Un contrafactual de 1 feature a magnitud clínicamente plausible indica que la alerta se dispara en una zona de alta incertidumbre decisional.
- Compatibilidad black-box: DiCE opera sobre la interfaz input-output. No requiere acceso a internals.

**Regla de selección R3.2 — Condiciones suficientes locales (Anchors).**
- Señal producida: regla "IF ... THEN alert" con precisión estimada (fracción de vecindario local donde la regla se cumple).
- Campo del snippet: `anchor_rule` = regla textual; `anchor_precision` = valor escalar.
- Umbral: si `anchor_precision` < 0.80, la regla no es fiable como condición suficiente; la alerta se emite en una zona donde el patrón de features no es estable.
- Justificación: Anchors complementa DiCE. DiCE mide la distancia al flip; Anchors identifica qué corredores de features sostienen la decisión y cuáles son frágiles.
- Compatibilidad black-box: Anchors opera sobre la interfaz input-output.

**Regla de selección R3.3 — Tasa de flip bajo perturbación (SHAP perturbation).**
- Señal producida: fracción de perturbaciones bounded (ruido de medición, missingness rutinaria) que invierten la decisión.
- Campo del snippet: `flip_rate` = fracción; `perturbation_type` = descripción del régimen de perturbación.
- Umbral: si `flip_rate` > 0.15, RC3 = CONTESTABLE (la decisión es inestable bajo variación operacional normal). Si > 0.30, RC3 = BLOCKED.
- Justificación: es la prueba más directa de RC3 — simula lo que ocurriría si las mediciones del caso variaran dentro de márgenes clínicos normales.

---

#### RC4 — Accountability and Reviewability: ¿Es reconstruible?

**Necesidad de evidencia:** La organización debe poder reconstruir por qué se consideró defendible confiar en el output bajo las condiciones operativas del momento. El fallo documentado (Epic): un sitio pausa alertas reactivamente, sin registro estructurado vinculando la pausa a señales monitorizadas.

**Regla de selección R4.1 — Logging de artefactos XAI.**
- Todos los artefactos producidos por R1.1, R1.2, R2.1, R2.2, R3.1, R3.2, R3.3 se registran como campos estructurados del snippet.
- Campo del snippet: cada campo XAI mencionado arriba, con timestamp, versión del modelo, y hash del input.

**Regla de selección R4.2 — Puertas de integridad.**
- Campo: `faithfulness_score` = métrica de fidelidad del artefacto SHAP (correlación entre atribución y efecto perturbacional).
- Campo: `monotonicity_check` = PASS/FAIL (si el artefacto cumple monotonía respecto a la relación feature-output).
- Justificación: RC4 no solo exige que el registro exista. Exige que los artefactos de evidencia sean íntegros y reproducibles. Un artefacto XAI sin puerta de integridad no es evidencia defendible.

**Regla de admisibilidad R4.A — Reproducibilidad.**
- Test: dado el mismo input y la misma versión del modelo, el snippet debe ser idéntico.
- Campo: `reproducibility_check` = PASS/FAIL.

---

## 3. Arquitectura del Evidence Snippet

### 3.1 Especificación de campos

El snippet es un registro estructurado con cuatro bloques:

**Bloque A — Identificación del caso:**
```
case_id:          STRING    (identificador único)
timestamp:        ISO-8601  (momento de la decisión)
model_version:    STRING    (versión del modelo + hash)
site_id:          STRING    (identificador del sitio)
```

**Bloque B — Output y contexto operacional:**
```
output_value:     FLOAT     (puntuación de riesgo)
threshold:        FLOAT     (umbral de acción del sitio)
margin:           FLOAT     (output_value - threshold)
census_norm_rate: FLOAT     (tasa de alertas normalizada por censo)
```

**Bloque C — Campos XAI (instrumentación de evidencia):**

| Campo | Método XAI | RC servida | Tipo | Admisibilidad requerida |
|---|---|---|---|---|
| `concept_group_attribution` | SHAP (KernelSHAP) | RC1 | DICT {feature_group: fraction} | R1.A (estabilidad) |
| `clinical_domain_fraction` | SHAP (derivado) | RC1 | FLOAT | R1.A |
| `tcav_sepsis_activation` | TCAV | RC1 | FLOAT (o CANNOT_COMPUTE) | N/A si CANNOT_COMPUTE |
| `attribution_stability_delta` | SHAP perturbation | RC1 (admisibilidad) | FLOAT | — (es la puerta misma) |
| `prototype_distance` | ProtoDash | RC2 | FLOAT | R2.A (consistencia temporal) |
| `nearest_prototype_id` | ProtoDash | RC2 | STRING | — |
| `influence_concentration` | Influence Functions | RC2 | DICT (o CANNOT_COMPUTE) | — |
| `validated_cohort_match` | Influence Functions | RC2 | BOOL (o CANNOT_COMPUTE) | — |
| `counterfactual_distance` | DiCE | RC3 | INT (n features) + magnitudes | — |
| `counterfactual_features` | DiCE | RC3 | LIST [{feature, delta}] | — |
| `anchor_rule` | Anchors | RC3 | STRING (regla) | — |
| `anchor_precision` | Anchors | RC3 | FLOAT | — |
| `flip_rate` | SHAP perturbation | RC3 | FLOAT | — |
| `perturbation_type` | — | RC3 | STRING | — |
| `faithfulness_score` | Correlación perturbacional | RC4 | FLOAT | — |
| `monotonicity_check` | Test de monotonía | RC4 | PASS/FAIL | — |

**Bloque D — Veredictos RC y decisión:**
```
rc1_status:       ENUM {OK, CONTESTABLE, BLOCKED}
rc2_status:       ENUM {OK, CONTESTABLE, BLOCKED}
rc3_status:       ENUM {OK, CONTESTABLE, BLOCKED}
rc4_status:       ENUM {COMPLETE, INCOMPLETE}
reliance_decision: ENUM {PROCEED, PROCEED_WITH_FLAG, CONSTRAIN}
operator_rationale: STRING (requerido si CONTESTABLE)
```

### 3.2 Lógica de decisión

La lógica es **no compensatoria** (un RC fuerte no compensa uno débil):

```
SI algún RC = BLOCKED:
    reliance_decision = CONSTRAIN
    
SI algún RC = CONTESTABLE Y ninguno = BLOCKED:
    reliance_decision = PROCEED_WITH_FLAG
    operator_rationale = REQUERIDO
    
SI todos RC = OK/COMPLETE:
    reliance_decision = PROCEED
```

Cuando `reliance_decision = PROCEED_WITH_FLAG`, el evento se registra. Si los eventos CONTESTABLE se acumulan (clustering temporal o por sitio), el patrón mismo se convierte en señal de degradación de la envolvente.

---

## 4. Instanciación contra el Epic Sepsis Model

### 4.1 Anclajes cuantitativos (fuentes independientes)

De Wong et al. (2021): desplazamiento de tasa de alertas 9% → 21%; volumen +43% (953 → 1.363 alertas/día) con descenso de censo del 35%; umbral fijo = 6; un sitio pausa alertas.

De Michigan Medicine (JAMA Internal Medicine, 2021): AUC = 0.63 (IC 95%: 0.62–0.64); PPV ≈ 12% en umbral habitual.

### 4.2 Mapeo diagnóstico RC con instrumentación XAI

#### Fallo 1 → RC1: La etiqueta persiste pero el output tiene peso evidencial débil

**Brecha de evidencia en el punto de uso:** No existe caracterización funcional vinculando la respuesta del score a features al referente clínico declarado ("sepsis risk") en el momento de la alerta.

**Instrumentación operacional:** Monitorización de distribución de features (fracción de features de entrada procedentes de fuentes clínicas vs. administrativas).

**Instrumentación XAI:**
- SHAP (KernelSHAP): perfiles de atribución agrupados por concepto en cada alerta. `clinical_domain_fraction` cuantifica qué proporción de la masa de atribución recae en features clínicamente relevantes (lactato, variabilidad de frecuencia cardíaca, tendencia de leucocitos) vs. proxies administrativos (código diagnóstico, duración de estancia, tipo de cama).
- TCAV: vectores de activación conceptual verificando que los casos con puntuación alta activan conceptos de sepsis clínica, no complejidad administrativa.
- Puerta de admisibilidad: estabilidad de atribución bajo perturbación bounded (delta < 0.10).

**Capacidad discriminativa demostrada:** Si en una muestra de alertas el `clinical_domain_fraction` promedio es < 0.60, RC1 detecta que la etiqueta "riesgo de sepsis" no es sostenible como claim — independientemente de que AUC sea 0.63 o 0.93. El fallo es de claim clarity, no de performance.

#### Fallo 2 → RC2: Inflación de alertas bajo régimen COVID

**Brecha de evidencia en el punto de uso:** No existen señales de cobertura de envolvente que condicionen la emisión de alertas. No se detecta el cambio de régimen en la generación de datos.

**Instrumentación operacional:** Tasa de alertas normalizada por censo (por sitio); sentinelas de missingness; monitores de distribución de puntuaciones en la cola del umbral.

**Instrumentación XAI:**
- ProtoDash / MMD-critic: sondas de cobertura prototípica comparando cada caso que dispara alerta con la envolvente de entrenamiento evidenciada. `prototype_distance` > 0.30 → caso sin prototipo cercano → fuera de envolvente.
- Funciones de influencia (si computables): identifican qué regiones de entrenamiento son más influyentes para las alertas actuales. Cuando la base activa migra desde la cohorte UCI validada hacia regiones no cubiertas, se activa señal de shift.

**Capacidad discriminativa demostrada:** RC2 detecta que las alertas operan fuera de la envolvente validada durante el régimen COVID — no porque el AUC baje, sino porque la población que genera alertas ha cambiado. Es un fallo de contextual validity, distinto del fallo de claim clarity (RC1).

#### Fallo 3 → RC3: Umbral fijo sin evidencia de robustez

**Brecha de evidencia en el punto de uso:** No existen datos de margen al umbral ni evidencia de robustez bajo perturbación plausible (ruido, missingness, variación temporal de documentación).

**Instrumentación operacional:** Logging de margen al umbral; densidad de concentración cerca del umbral; volatilidad de puntuación bajo variación de timing de documentación.

**Instrumentación XAI:**
- DiCE / Wachter: distancia contrafactual mínima para invertir la alerta. `counterfactual_distance` = 2 features (HR +4bpm, Lactato -0.3) → la alerta depende de un margen clínicamente plausible de variación.
- Anchors: condiciones suficientes locales. `anchor_rule`: IF Lactate > 2.8 AND HR_var > 0.6 THEN alert (precision 0.89). Muestra qué corredores de features sostienen la decisión.
- Tasa de flip: `flip_rate` = 0.18 bajo perturbación bounded > 0.15 (banda de advertencia). Significa que el 18% de las perturbaciones operacionalmente plausibles invertirían la decisión.

**Capacidad discriminativa demostrada:** RC3 detecta que el umbral fijo opera sobre un margen frágil. El fallo no es de claim clarity (RC1) ni de validity (RC2): la puntuación puede ser clínicamente referenciada y dentro de envolvente, pero la acción (alerta) se dispara en una zona donde perturbaciones normales la invertirían. Es un fallo de decision adequacy.

#### Fallo 4 → RC4: Pausa reactiva sin registro estructurado

**Brecha de evidencia en el punto de uso:** No existe capa de persistencia vinculando output, contexto y estatus RC en el momento de la decisión.

**Instrumentación operacional:** Registro estructurado: {output, threshold, model_version, timestamp, RC-status}.

**Instrumentación XAI:**
- Logging de artefactos: resumen de atribución, distancia contrafactual y match prototípico registrados como campos estructurados — cada acto de reliance es inspeccionable.
- Puertas de integridad: `faithfulness_score` y `monotonicity_check` como verificación de que los artefactos XAI son íntegros antes de contar como evidencia.

**Capacidad discriminativa demostrada:** RC4 detecta que la pausa de alertas fue reactiva y no auditable. Con instrumentación RC4, la pausa habría estado vinculada a señales monitorizadas (clustering de RC2-CONTESTABLE, tendencia ascendente de flip_rate), y el acto de pausa sería reconstruible y contestable.

### 4.3 Snippet instanciado (ejemplo)

```
─────────────────────────────────────────────────────────
EVIDENCE SNIPPET
─────────────────────────────────────────────────────────
BLOQUE A — Identificación
  Case ID:        2025-09-14-ICU-001
  Timestamp:      2025-09-14T14:32:09Z
  Model version:  ESM-v3.2.1-site-MCH
  Site ID:        MCH-ICU-3

BLOQUE B — Output y contexto
  Output:         Sepsis risk = 0.82
  Threshold:      0.60 (site policy)
  Margin:         0.22
  Census-norm alert rate: 14.2% (< 18% ceiling)

BLOQUE C — Instrumentación XAI

  [RC1 — Claim Clarity]
  SHAP concept groups:
    Lactate:           32%
    HR variability:    28%
    WBC trend:         18%
    Admin/proxy:       12%
    Other clinical:    10%
  Clinical domain fraction: 0.78 (> 0.60 threshold → OK)
  TCAV sepsis activation:   0.81 (> 0.60 threshold → OK)
  Attribution stability δ:  0.04 (< 0.10 ceiling → ADMISSIBLE)

  [RC2 — Contextual Validity]
  ProtoDash nearest prototype: distance = 0.12 (< 0.30 → OK)
  Influence concentration: top-5 regions = validated ICU cohort
  Coverage trend (last 50 alerts): stable (slope = +0.002/alert)

  [RC3 — Decision Adequacy]
  Margin to threshold: 0.22 (> 0.10 minimum → OK)
  DiCE counterfactual: 2 features (HR +4bpm, Lactate -0.3)
  Anchors rule: IF Lactate > 2.8 AND HR_var > 0.6
                THEN alert (precision = 0.89)
  Flip rate: 0.18 under bounded perturbation
             (> 0.15 warning → CONTESTABLE)

  [RC4 — Record integrity]
  Faithfulness score: 0.91
  Monotonicity check: PASS
  Reproducibility:    PASS

BLOQUE D — Veredictos y decisión
  RC1: OK
  RC2: OK
  RC3: CONTESTABLE (flip_rate 0.18 > 0.15 warning)
  RC4: COMPLETE

  RELIANCE DECISION: PROCEED_WITH_FLAG
  Action: Alerta emitida. Flip-rate registrado.
          Rationale clínica requerida y documentada.
─────────────────────────────────────────────────────────
```

---

## 5. Evaluación del artefacto (Ciclo de Rigor)

### 5.1 Utilidad (utility)

El snippet genera información que no existía antes del artefacto:

- **Sin snippet:** La alerta es un número (0.82) con una etiqueta ("sepsis risk") y un umbral fijo (0.60). No hay evidencia de qué mide, si el caso cae dentro de la envolvente validada, si la decisión es robusta, ni registro reconstruible.
- **Con snippet:** Cada campo produce evidencia evaluable. La organización puede responder a "¿por qué confiaron en esta alerta?" con datos estructurados, no con narrativas post-hoc.

### 5.2 Capacidad discriminativa

Los cuatro RC producen campos distintos y no redundantes:

| Propiedad evaluada | RC1 | RC2 | RC3 | RC4 |
|---|---|---|---|---|
| ¿Qué mide el output? | clinical_domain_fraction, tcav_activation | — | — | — |
| ¿Dónde opera? | — | prototype_distance, influence_concentration | — | — |
| ¿Es robusta la acción? | — | — | counterfactual_distance, flip_rate, anchor_precision | — |
| ¿Es reconstruible? | — | — | — | faithfulness, monotonicity, reproducibility |

Cada RC puede ser OK mientras otras son CONTESTABLE o BLOCKED. En el ejemplo instanciado: RC1=OK, RC2=OK, RC3=CONTESTABLE, RC4=COMPLETE. No hay redundancia: el estado de RC3 no puede inferirse de RC1 ni de RC2.

### 5.3 Reproducibilidad

Cada decisión de asignación sigue una regla explícita (R1.1, R1.2, R2.1, etc.). Dado:
- El mismo caso (input features).
- La misma versión del modelo.
- Los mismos umbrales institucionales.
- Los mismos métodos XAI con los mismos parámetros.

El snippet producido es idéntico. No hay juicio implícito en la construcción. Las decisiones analíticas (qué features son "clínicas" vs. "proxy") son declaradas como configuración institucional, no como hallazgos emergentes.

### 5.4 Contraste externo (fallos documentados)

Los cuatro fallos documentados por Wong et al. (2021) y Michigan Medicine son identificados por las definiciones de RC, no por conocimiento retrospectivo del resultado:

| Fallo documentado | RC asignada | Derivabilidad |
|---|---|---|
| Etiqueta persiste con peso evidencial débil | RC1 | RC1 requiere claim-evidence alignment; SHAP/TCAV lo cuantifican |
| Inflación 9%→21% bajo COVID shift | RC2 | RC2 requiere evidencia de envolvente; ProtoDash/influence lo detectan |
| Umbral fijo sin evidencia de robustez | RC3 | RC3 requiere robustez al action boundary; DiCE/Anchors/flip-rate lo miden |
| Pausa reactiva sin registro | RC4 | RC4 requiere registro reconstruible; el snippet mismo es la solución |

Nota: los fallos documentados se usan para contrastar la capacidad del artefacto, no para construirlo. Las reglas de selección XAI proceden de los requisitos de evidencia de RC (derivados del corpus N=43), no del conocimiento de qué falló en Epic.

---

## 6. Resumen de métodos XAI seleccionados con justificación funcional

| Método XAI | RC servida | Señal producida | Compatibilidad black-box | Alternativa si no computable |
|---|---|---|---|---|
| **SHAP (KernelSHAP)** | RC1, RC3 | Atribución por feature, agrupable en conceptos; flip-rate bajo perturbación | Sí (interfaz input-output) | LIME (menor estabilidad) |
| **TCAV / ConceptSHAP** | RC1 | Activación de vectores conceptuales predefinidos | Parcial (requiere capas intermedias) | CANNOT_COMPUTE → RC1 depende solo de SHAP |
| **ProtoDash / MMD-critic** | RC2 | Distancia al prototipo más cercano en espacio evidenciado | Sí (opera sobre features) | k-NN distance en espacio de features |
| **Influence Functions** | RC2 | Regiones de entrenamiento más influyentes | No (requiere gradientes de loss) | CANNOT_COMPUTE → RC2 depende de ProtoDash + telemetría |
| **DiCE / Wachter** | RC3 | Cambios mínimos para invertir la decisión | Sí (interfaz input-output) | — |
| **Anchors** | RC3 | Reglas suficientes locales con precisión estimada | Sí (interfaz input-output) | — |
| **Faithfulness / Monotonicity** | RC4 (admisibilidad) | Correlación perturbacional; test de monotonía | Sí | — |

---

## 7. Limitaciones del artefacto

Tres propiedades permanecen abiertas:

1. **Sensibilidad bajo condiciones prospectivas.** La instanciación demuestra capacidad discriminativa retrospectiva. Si las sondas SHAP/TCAV, contrafactuales DiCE y monitores ProtoDash detectarían degradación de envolvente antes del daño a una tasa de falsas alarmas aceptable requiere un estudio de despliegue con instrumentación XAI pre-especificada, recogida prospectiva de datos y linkage con resultados.

2. **Tratabilidad computacional.** Para modelos tabulares EHR, las computaciones SHAP y DiCE son típicamente sub-segundo en infraestructura clínica estándar. La latencia bajo carga de producción requiere validación empírica. TCAV y funciones de influencia tienen mayor coste computacional y pueden no ser factibles en tiempo de alerta.

3. **Admisibilidad institucional.** Que los artefactos XAI cuenten como evidencia de gobernanza — no como explicaciones para la persona usuaria — no tiene precedente establecido en informática clínica. Si las puertas de fidelidad son suficientes para hacer los artefactos XAI institucionalmente defendibles es una cuestión de gobernanza abierta.

Estas son limitaciones de la etapa actual de validación. Un framework que no puede demostrar capacidad discriminativa contra fallos conocidos no tiene base para evaluación prospectiva. Este artefacto establece esa base y especifica la arquitectura de instrumentación XAI que un estudio prospectivo necesitaría desplegar.
