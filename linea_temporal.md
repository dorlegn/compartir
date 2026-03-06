```mermaid
flowchart TD
  %% --- CONFIGURACIÓN DE ESTILOS ---
  classDef nodo fill:#ffffff,stroke:#24292e,stroke-width:1px,color:#24292e
  classDef gap fill:#fef2f2,stroke:#cf222e,stroke-width:2px,color:#24292e
  classDef ref fill:#f6f8fa,stroke:#d0d7de,stroke-width:1px,color:#586069
  classDef desp fill:#e8f4fd,stroke:#0969da,stroke-width:2px,color:#24292e
  classDef pivote fill:#fff3cd,stroke:#d4a017,stroke-width:2px,color:#24292e
  classDef resultado fill:#f0fdf4,stroke:#1a7f37,stroke-width:2px,color:#24292e
  classDef arco fill:#f5f0ff,stroke:#8250df,stroke-width:2px,color:#24292e

  %% --- 1. PUNTO DE PARTIDA ---
  subgraph S1 [1. PUNTO DE PARTIDA - 2020]
    direction TB
    plan["<b>Título del plan doctoral:</b><br/>'Development of methodology and supporting technology<br/>to produce concise, understandable symbolic explanations<br/>of actions taken by a system built out of many,<br/>possibly autonomous, parts (including the human operator)'"]:::nodo
    problema["<b>El problema tal como se formuló:</b><br/>los modelos black-box son opacos;<br/>se necesitan explicaciones comprensibles.<br/><b>Marco:</b> Responsible AI (Díaz-Rodríguez et al., 2019)."]:::nodo
    lit1["<b>Literatura de referencia:</b><br/>- Gilpin et al. (2018, 2019): inside vs outside explanations, reasonableness monitors.<br/>- Lipton (2018): 'the mythos of model interpretability', ambigüedad del concepto.<br/>- Ribeiro et al. (2016): LIME, 'why should I trust you?'<br/>- Lepri et al. (2018): fair, transparent, accountable algorithmic decision-making."]:::ref
    gap2020["<b>GAP IDENTIFICADO EN 2020:</b><br/>Falta metodología para producir explicaciones simbólicas concisas<br/>de acciones en sistemas complejos. El problema es técnico-comunicativo.<br/>Lipton (2018) ya advierte: 'it is not clear what common properties<br/>unite these [interpretability] techniques'"]:::gap
    
    plan --> problema --> lit1 --> gap2020
  end

  %% --- 2. EXPLORACIÓN Y GIRO ---
  subgraph S2 [2. EXPLORACIÓN Y GIRO ~2021-2023]
    giro["<b>El campo se desplaza hacia Trustworthy AI.</b><br/>Marco dominante: Ethics Guidelines for Trustworthy AI (EC, 2019):<br/>sistemas 'lawful, ethical, and robust throughout the lifecycle.'"]:::nodo
    inst["<b>Transparencia + ética + regulación:</b><br/>- GDPR: right to explanation, contestabilidad (Lazcoz, 2023).<br/>- AI Act: risk-based framework, conformity assessments (Palmiotto, 2024; Malgieri & Pasquale, 2024).<br/>- CSRD: mandatory sustainability disclosure (Cicchiello et al., 2023; Krasodomska et al., 2024)."]:::ref
    preg_pivote["<b>INVESTIGACIÓN SE DESPLAZA HACIA:</b><br/>¿Realmente la transparencia y la explicabilidad resuelven<br/>el problema de accountability en contextos de alto riesgo?"]:::pivote
    
    gap2020 --> giro --> inst --> preg_pivote
  end

  %% --- 3. BEYOND TRANSPARENCY ---
  subgraph S3 [3. BEYOND TRANSPARENCY - EL VERDADERO PIVOTE]
    metodo3["<b>Revisión narrativa estructurada</b><br/>(Jaakkola, 2020; screening: Bowen, 2009).<br/>64 documentos. 4 ejes analíticos."]:::nodo
    T1["<b>T1:</b> Transparencia insuficiente para accountability.<br/>Disclosure no produce accountability. 'Comply or explain' permite selectividad (Cuomo, 2024).<br/>AIA como regulación de producto, no captura daños sistémicos (Almada, 2024)."]:::nodo
    T2["<b>T2:</b> Principios de Trustworthy AI pobremente operacionalizados.<br/>'From politics to ethics' despolitiza accountability (Carlsson, 2022).<br/>Riesgo de ethics-washing (De Pagter, 2024). Gobernanza fragmentada (Bisconti, 2023)."]:::nodo
    T3["<b>T3:</b> XAI es respuesta técnica parcial. AI carece de agencia moral (Ryan, 2020).<br/>Explicaciones moldean RELIANCE, no trust. Riesgo de overreliance (Herrera, 2025).<br/>Trade-off performance/interpretabilidad y fidelidad cuestionable (Miró-Nicolau, 2025)."]:::nodo
    T4["<b>T4:</b> Fragmentación de dominios. Explicaciones sin conexión con estructuras de decisión.<br/>'Explainability mismatch': regulación ≠ oversight interno (Langer, 2021; Herrera, 2025).<br/>Auditabilidad como puente (Mökander, 2023)."]:::nodo
    diag3["<b>DIAGNÓSTICO FINAL:</b><br/>'Knowing how a model produced an output does not necessarily clarify who is accountable when that output is acted upon.'<br/>XAI es 'indispensable for scrutiny, but insufficient for addressing the social, organisational and regulatory conditions.'<br/><b>Queda sin resolver:</b> how accountability is enacted in practice (Beyond Transparency, conclusión)."]:::gap
    
    preg_pivote --> metodo3 --> T1 & T2 & T3 & T4 --> diag3
  end

  %% --- 4. POSICIÓN PROVISIONAL Y CORPUS ---
  diag3 --> desplaza1
  desplaza1["<b>ESTE DIAGNÓSTICO DESPLAZA LA TESIS:</b><br/>DE: 'necesitamos mejores explicaciones' (Gilpin et al., 2019)<br/>A: 'necesitamos saber cuándo es defendible actuar sobre un output'<br/>El problema ya no es explicabilidad. Es RELIANCE."]:::desp

  subgraph S4 [4. POSICIÓN PROVISIONAL Y CORPUS]
    pos4["<b>Objeto de estudio:</b> la relación entre un output algorítmico y una decisión concreta,<br/>entendida como relación evaluable.<br/>'Reliance is treated as an action-guiding commitment. A person relies on an output<br/>when they use it as a reason for action.' (Paper v2, §1)"]:::nodo
    excl4["<b>Exclusiones metodológicas:</b><br/>- Trust como actitud psicológica (Ryan, 2020).<br/>- Principios éticos sin condiciones de decisión (Herrera-Poyatos, 2025).<br/>- Frameworks a nivel de sistema (Stettinger, 2024).<br/><b>Distinción estabilizadora:</b> Transparencia ≠ accountability; Explicaciones ≠ justificaciones."]:::ref
    corpus4["<b>Corpus específico:</b><br/>311 registros → 221 únicos → 137 Phase 1 → 43 Phase 2. (Paper v2, §3)<br/>Solo literatura que describa condiciones de reliance aceptable, contestable o defectivo."]:::nodo
    marco4["<b>Marco analítico:</b><br/>WHAT (Defendible) | WHEN (Defendible) | HOW (Detección Defendible).<br/>Deriva de tensiones T1 a T4 de Beyond Transparency."]:::nodo
    
    desplaza1 --> pos4 --> excl4 --> corpus4 --> marco4
  end

  %% --- 5. ANÁLISIS INDUCTIVO Y 6. BREAKDOWNS ---
  subgraph S56 [5. ANÁLISIS INDUCTIVO Y 6. REINTERPRETACIÓN]
    induct5["<b>Emergencia del Corpus Phase 2 (N=43):</b><br/>- Regularidades: Outputs meaningful solo relativo a propósito, alcance y límites (RC1).<br/>- Fallos: Reliance problemático cuando el rol es incierto o la validez no sostiene el contexto (Paper v2, §1).<br/>- Convergencia cross-type: Normativos y técnicos describen el mismo breakdown (Paper v2, §5.4)."]:::nodo
    giro6["<b>GIRO HACIA BREAKDOWNS (UVC):</b><br/>Validity Conditions se reformulan como USE VALIDITY CHECKS (UVC): 'Reliance fails when... because...'<br/>Justificación: el corpus habla en breakdowns; produce checks ejecutables;"]:::resultado
    
    marco4 --> induct5 --> giro6
  end

  %% --- 7. POINT OF USE Y 8. ASSURANCE ---
  subgraph S78 [7. POINT OF USE Y 8. ANCLAJE EN ASSURANCE]
    pou7["<b>POINT OF USE:</b> el paso del procedimiento donde un output algorítmico se convierte<br/>en razón para una acción concreta. 'The proper object of evaluation and accountability<br/>is the institutional act of delegating authority... within an established workflow.' (Paper v7, §2.2)"]:::resultado
    assur8["<b>Assurance Engineering como anclaje:</b><br/>UVC son formalmente equivalentes a modos de fallo.<br/>- AMLAS (Hawkins, 2021): Ciclo de vida, no output-decisión concreta.<br/>- STPA (Leveson, 2011): Nivel sistema, no transición individual.<br/>- Safety Cases (Ward & Habli, 2020): No sobre cada paso del workflow."]:::ref
    
    giro6 --> pou7 --> assur8
  end

  %% --- 9. GAP UNIFICADO Y REPOSICIONAMIENTO ---
  subgraph S9 [9. GAP UNIFICADO Y REPOSICIONAMIENTO XAI]
    gap9["<b>Gap unificado:</b> 1. Assurance (Falta estructura checkable en uso) | 2. Instrumentación (XAI post-hoc<br/>no genera evidencia conductual continua en el boundary)."]:::gap
    posicion9["<b>Quinta Posición: DECISION-BOUNDARY INSTRUMENTATION</b><br/>'A source of structured, integrity-checkable evidence artefacts that can be bound<br/>to the reliance act and used to assess RC-status at the moment of action.' (Paper v7, §2.4)"]:::resultado
    mapeo9["<b>Mapeo XAI → RC:</b><br/>RC1 (Claim Clarity) → SHAP/LIME profiles, TCAV activation.<br/>RC2 (Contextual Validity) → ProtoDash probes, Influence functions.<br/>RC3 (Decision Adequacy) → DiCE counterfactuals, Anchors, SHAP flip-rate.<br/>RC4 (Accountability) → XAI artefact logging, Integrity gates (Jacovi, 2020)."]:::nodo
    
    assur8 --> gap9 --> posicion9 --> mapeo9
  end

  %% --- 11. DEMOSTRADOR EPIC ---
  subgraph S11 [11. DEMOSTRADOR: EPIC SEPSIS MODEL]
    epic11["<b>Caso: Wong et al. (2021) y Michigan Medicine (JAMA, 2021).</b><br/>Validación contra fallos reales: AUC 0.63, PPV 12%, Alert rate inflation (COVID shift)."]:::nodo
    table3["<b>RESULTADO DIAGNÓSTICO (Table 3):</b><br/>RC1: Fallo de peso evidencial detectable por SHAP concept groups.<br/>RC2: Inflación detectable por Influence functions (shift detection).<br/>RC3: Threshold frágil detectable por counterfactual distance per alert.<br/>RC4: Falta de registro resuelto por structured XAI artefact logging."]:::resultado
    compl11["<b>Complementariedad:</b> Telemetría detecta QUE algo falla. Instrumentación XAI diagnostica QUÉ falla y DÓNDE.<br/>'Las dos familias de evidencia son complementarias, no sustituibles.' (Section 5 v2, §5.2)"]:::resultado
    
    mapeo9 --> epic11 --> table3 --> compl11
  end

  %% --- 12. CONTRIBUCIÓN Y 13. ARCO ---
  subgraph S1213 [12. CONTRIBUCIÓN Y 13. ARCO COMPLETO]
    alcance12["<b>ALCANCE:</b> Arquitectura de assurance que hace evaluable la relación output→acción,<br/>con XAI reposicionada como instrumentación que genera campos estructurados de evidencia.<br/>Límites: Sensibilidad prospectiva, Tractabilidad, Admisibilidad institucional."]:::nodo
    tesis13["<b>TESIS (Arco Completo):</b> El punto de evaluación no es el modelo.<br/>Es la relación output→acción en el punto de uso.<br/>Grounded en design science (Hevner), demostrador (Wong) y assurance (Hawkins, Leveson)."]:::arco
    
    compl11 --> alcance12 --> tesis13
  end

 
