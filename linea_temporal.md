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
    plan["<b>Título del plan doctoral:</b><br/>'Development of methodology and supporting technology to produce concise, understandable symbolic explanations of actions taken by a system built out of many, possibly autonomous, parts (including the human operator)'"]:::nodo
    problema["<b>El problema tal como se formuló:</b><br/>los modelos black-box son opacos; se necesitan explicaciones comprensibles.<br/><b>Marco:</b> Responsible AI (Díaz-Rodríguez et al., 2019)."]:::nodo
    lit1["<b>Literatura de referencia:</b><br/>- Gilpin et al. (2018, 2019): inside vs outside explanations, reasonableness monitors.<br/>- Lipton (2018): 'the mythos of model interpretability', ambigüedad del concepto.<br/>- Ribeiro et al. (2016): LIME, 'why should I trust you?'<br/>- Lepri et al. (2018): fair, transparent, accountable algorithmic decision-making."]:::ref
    gap2020["<b>GAP IDENTIFICADO EN 2020:</b><br/>Falta metodología para producir explicaciones simbólicas concisas de acciones en sistemas complejos. El problema es técnico-comunicativo. Lipton (2018) ya advierte: 'it is not clear what common properties unite these [interpretability] techniques'"]:::gap
    plan --> problema --> lit1 --> gap2020
  end

  %% --- 2. EXPLORACIÓN Y GIRO ---
  subgraph S2 [2. EXPLORACIÓN Y GIRO ~2021-2023]
    giro["<b>El campo se desplaza hacia Trustworthy AI.</b><br/>Marco dominante: Ethics Guidelines for Trustworthy AI (EC, 2019): sistemas 'lawful, ethical, and robust throughout the lifecycle.'"]:::nodo
    inst["<b>Transparencia + ética + regulación:</b><br/>- GDPR: right to explanation, contestabilidad (Lazcoz, 2023).<br/>- AI Act: risk-based framework, conformity assessments (Palmiotto, 2024; Malgieri & Pasquale, 2024).<br/>- CSRD: mandatory sustainability disclosure (Cicchiello et al., 2023; Krasodomska et al., 2024)."]:::ref
    preg_pivote["<b>INVESTIGACIÓN SE DESPLAZA HACIA:</b><br/>¿Realmente la transparencia y la explicabilidad resuelven el problema de accountability en contextos de alto riesgo?"]:::pivote
    gap2020 --> giro --> inst --> preg_pivote
  end

  %% --- 3. BEYOND TRANSPARENCY ---
  subgraph S3 [3. BEYOND TRANSPARENCY - EL VERDADERO PIVOTE]
    metodo3["<b>Revisión narrativa estructurada</b> (Jaakkola, 2020; screening: Bowen, 2009). 64 documentos. 4 ejes analíticos."]:::nodo
    T1["<b>T1:</b> Transparencia insuficiente para accountability. Disclosure no produce accountability. 'Comply or explain' permite selectividad (Cuomo, 2024). AIA como regulación de producto, no captura daños sistémicos (Almada, 2024)."]:::nodo
    T2["<b>T2:</b> Principios de Trustworthy AI pobremente operacionalizados. 'From politics to ethics' despolitiza accountability (Carlsson, 2022). Riesgo de ethics-washing (De Pagter, 2024). Gobernanza fragmentada (Bisconti, 2023)."]:::nodo
    T3["<b>T3:</b> XAI es respuesta técnica parcial. AI carece de agencia moral (Ryan, 2020). Explicaciones moldean RELIANCE, no trust. Riesgo de overreliance (Herrera, 2025). Trade-off performance/interpretabilidad y fidelidad cuestionable (Miró-Nicolau, 2025)."]:::nodo
    T4["<b>T4:</b> Fragmentación de dominios. Explicaciones sin conexión con estructuras de decisión. 'Explainability mismatch': regulación != oversight interno (Langer, 2021; Herrera, 2025). Auditabilidad como puente (Mökander, 2023)."]:::nodo
    diag3["<b>DIAGNÓSTICO FINAL:</b><br/>'Knowing how a model produced an output does not necessarily clarify who is accountable when that output is acted upon.' XAI es 'indispensable for scrutiny, but insufficient for addressing the social, organisational and regulatory conditions.' Queda sin resolver: how accountability is enacted in practice (Beyond Transparency, conclusión)."]:::gap
    preg_pivote --> metodo3 --> T1 & T2 & T3 & T4 --> diag3
  end

  %% --- 4. POSICIÓN PROVISIONAL Y CORPUS ---
  diag3 --> desplaza1
  desplaza1["<b>ESTE DIAGNÓSTICO DESPLAZA LA TESIS:</b><br/>DE: 'necesitamos mejores explicaciones' (Gilpin et al., 2019)<br/>A: 'necesitamos saber cuándo es defendible actuar sobre un output'. El problema ya no es explicabilidad. Es RELIANCE."]:::desp

  subgraph S4 [4. POSICIÓN PROVISIONAL Y CORPUS]
    pos4["<b>Objeto de estudio:</b> la relación entre un output algorítmico y una decisión concreta, entendida como relación evaluable. 'Reliance is treated as an action-guiding commitment. A person relies on an output when they use it as a reason for action.' (Paper v2, §1)"]:::nodo
    excl4["<b>Exclusiones metodológicas:</b> - Trust como actitud psicológica (Ryan, 2020). - Principios éticos sin condiciones de decisión (Herrera-Poyatos, 2025). - Frameworks a nivel de sistema (Stettinger, 2024). <b>Distinción estabilizadora:</b> Transparencia != accountability; Explicaciones != justificaciones."]:::ref
    corpus4["<b>Corpus específico:</b> 311 registros ➔ 221 únicos ➔ 137 Phase 1 ➔ 43 Phase 2. (Paper v2, §3). Solo literatura que describa condiciones de reliance aceptable, contestable o defectivo."]:::nodo
    marco4["<b>Marco analítico:</b> WHAT (Defendible) | WHEN (Defendible) | HOW (Detección Defendible). Deriva de tensiones T1 a T4 de Beyond Transparency."]:::nodo
    desplaza1 --> pos4 --> excl4 --> corpus4 --> marco4
  end

  %% --- 5. ANÁLISIS INDUCTIVO Y 6. BREAKDOWNS ---
  subgraph S56 [5. ANÁLISIS INDUCTIVO Y 6. REINTERPRETACIÓN]
    induct5["<b>Emergencia del Corpus Phase 2 (N=43):</b> - Regularidades: Outputs meaningful solo relativo a propósito, alcance y límites (RC1). - Fallos: Reliance problemático cuando el rol es incierto o la validez no sostiene el contexto (Paper v2, §1). - Convergencia cross-type: Normativos y técnicos describen el mismo breakdown (Paper v2, §5.4)."]:::nodo
    giro6["<b>GIRO HACIA BREAKDOWNS (UVC):</b> Validity Conditions se reformulan como USE VALIDITY CHECKS (UVC): 'Reliance fails when... because...'. Justificación: el corpus habla en breakdowns; produce checks ejecutables."]:::resultado
    marco4 --> induct5 --> giro6
  end

  %% --- 7. LOCALIZACIÓN DEL PUNTO DE RUPTURA ---
  subgraph S7 [7. LOCALIZACIÓN DEL PUNTO DE RUPTURA]
    direction TB
    rupt7["<b>LOCALIZACIÓN DEL PUNTO DE RUPTURA:</b> En la codificación del corpus, prácticamente todos los breakdowns se sitúan en el mismo punto: la transición output ➔ acción. 'The fragile moment is not the computation itself, but what follows: someone has to decide whether that output can serve as a defensible reason for action. When the procedure moves on the basis of a score, authority has been delegated.' (Paper v7, §1)"]:::nodo
    vuln7["'The critical vulnerability is not only in training or testing, but in the moment of epistemic translation: when a score is used to justify a material action' (McLean & Hristidis, 2021 — citado en Paper v7, §2.2)."]:::ref
    pou7["<b>POINT OF USE =</b> el paso del procedimiento donde un output algorítmico se convierte en razón para una acción concreta. 'The proper object of evaluation and accountability is the institutional act of delegating authority to an algorithmic output within an established workflow.' (Paper v7, §2.2)"]:::resultado
    system7["<b>Confirmación:</b> la gobernanza system-centric no resuelve este problema. 'System-level properties do not tell operators what must be true for a specific output to justify a specific action at a specific workflow step.' (Paper v7, §2.3)"]:::gap
    defend7["'A more robust objective is structural defensibility: explicit, inspectable arguments that link output and action in a way that can be audited and contested if needed' (Ward & Habli, 2020; Picardi & Habli, 2022, Paper v7, §2.3)."]:::resultado
    giro6 --> rupt7 --> vuln7 --> pou7 --> system7 --> defend7
  end

  %% --- 8. ASSURANCE ENGINEERING ---
  subgraph S8 [8. ASSURANCE ENGINEERING COMO CAMPO DE ANCLAJE]
    direction TB
    assur8["<b>ANCLAJE EN ASSURANCE:</b> Los UVC formulados como breakdowns son formalmente equivalentes a modos de fallo en safety cases. Pero el campo se detiene antes del punto de uso:"]:::nodo
    amlas8["<b>AMLAS (Hawkins et al., 2021):</b> Cubre ciclo de vida del modelo. No especifica qué evidencia debe estar disponible cuando un output concreto se usa para una decisión concreta. 'AMLAS framework was initially validated against retrospective failure analysis rather than prospective data collection' (Paper v7, §1)"]:::gap
    stpa8["<b>STPA (Leveson, 2011):</b> Restricciones de seguridad a nivel de sistema. No modela la transición output ➔ acción individual."]:::gap
    safety8["<b>Safety Cases (Ward & Habli, 2020):</b> Argumentan sobre el sistema. No sobre cada transición output ➔ acción en el workflow."]:::gap
    rc_grounded["'RC are grounded in sociotechnical governance and assurance-case logic (Ward & Habli, 2020)"]:::ref
    defend7 --> assur8 --> amlas8 --> stpa8 --> safety8 --> rc_grounded
  end

  %% --- 9. GAP UNIFICADO + REPOSICIONAMIENTO XAI ---
  subgraph S9 [9. GAP UNIFICADO + REPOSICIONAMIENTO XAI]
    direction TB
    gap9["<b>DIMENSIÓN 1 — ASSURANCE:</b> 'What is missing is an assurance structure that makes reliance checkable in use.' (Paper v7, §2.3).<br/><b>DIMENSIÓN 2 — INSTRUMENTACIÓN:</b> 'Many uses of XAI remain focused on post-hoc, case-level explanation outputs... they are not designed to generate ongoing behavioural evidence about the output at the workflow boundary.' (Paper v7, §1)"]:::gap
    posiciones_desc["<b>REPOSICIONAMIENTO DE XAI (Paper v7, §2.4):</b> Posiciones descartadas:<br/>Pos.1: XAI como derecho a explicación (Wachter 2017). Lógica retrospectiva.<br/>Pos.2: XAI como instrumento de evaluación (Doshi-Velez 2017). Evalúa método, no artefacto.<br/>Pos.3: XAI como justificación institucional (Selbst 2018). Post-fact.<br/>Pos.4: XAI como problema a eliminar (Rudin 2019). No operativa para desplegados."]:::nodo
    pos5["<b>QUINTA POSICIÓN (Contribución): DECISION-BOUNDARY INSTRUMENTATION:</b> 'A source of structured, integrity-checkable evidence artefacts that can be bound to the reliance act and used to assess RC-status at the moment of action.' (Paper v7, §2.4). Criterio de admisibilidad: No comprensibilidad, sino como registro (loggable record)."]:::resultado
    mapeoXAI["<b>MAPEO EXPLÍCITO XAI ➔ RC (Table 3):</b><br/>- <b>RC1 (Clarity):</b> SHAP/LIME profiles + TCAV concept activation (clínico vs proxy). Stability check gate.<br/>- <b>RC2 (Context):</b> ProtoDash/MMD-critic (envelope coverage) + Influence functions (detecta training shift).<br/>- <b>RC3 (Adequacy):</b> DiCE counterfactual distance + Anchors (fragile feature corridors) + SHAP flip-rate under realistic noise.<br/>- <b>RC4 (Accountability):</b> XAI artefact logging + Faithfulness/monotonicity gates (Jacovi 2020). Artefactos inestables se degradan a 'señal'.<br/><b>Argumento de Complementariedad:</b> XAI diagnostica QUÉ falla y DÓNDE. (§5.2)"]:::resultado
    rc_grounded --> gap9 --> posiciones_desc --> pos5 --> mapeoXAI
  end

  %% --- 10. MARCO PROPUESTO ---
  subgraph S10 [10. MARCO PROPUESTO]
    direction TB
    marco10["<b>Epistemología: Design Science (Hevner 2004).</b> Criterios: relevance, utility, discriminative capacity. (Paper v7, §1)"]:::nodo
    niveles10["<b>ARQUITECTURA DE TRES NIVELES:</b><br/><b>NIVEL 1 — ESTRATÉGICO:</b> Reliance Conditions (RC1–RC4). 'RC function as reliance boundaries. If a boundary is not evidenced... reliance becomes contestable.' (Paper v7, §3).<br/><b>NIVEL 2 — OPERATIVO:</b> Use Validity Checks (UVC-01 a UVC-07). Formulados como: 'Reliance fails when... because...'. Soporte inductivo trazable (Consolidation Log).<br/><b>NIVEL 3 — INSTRUMENTAL:</b> Familias de evidencia (SHAP, DiCE, Integrated Gradients, TCAV, etc.). (Paper v7, §4)"]:::resultado
    mapeoXAI --> marco10 --> niveles10
  end

  %% --- 11. DEMOSTRADOR: EPIC SEPSIS MODEL ---
  subgraph S11 [11. DEMOSTRADOR: EPIC SEPSIS MODEL]
    direction TB
    epic11["<b>Caso: Wong et al. (2021) y Michigan Medicine (JAMA, 2021).</b> Validación contra fallos reales: AUC 0.63, PPV ≈ 12%. COVID shift: alert rate inflation (9%➔21%), census -35%. EHR data stream ➔ feature assembly ➔ score ➔ threshold ➔ alert ➔ action trigger. (§5)"]:::nodo
    diag_epic["<b>RESULTADO DIAGNÓSTICO (Table 3 completa):</b><br/>- <b>RC1:</b> Alert label persiste pero peso evidencial débil. SHAP concept groups (clínico vs proxy).<br/>- <b>RC2:</b> Inflación por COVID shift. ProtoDash envelope coverage probes + Influence shift detection.<br/>- <b>RC3:</b> Fixed threshold sin evidencia de robustez. DiCE counterfactual distance per alert + SHAP flip-rate.<br/>- <b>RC4:</b> Alerting paused sin registro estructurado. XAI artefact logging + Faithfulness gates.<br/><b>EVIDENCE SNIPPET:</b> Registro estructurado que vincula output, contexto y RC-status. Flip-rate 0.18 > 0.15 marca CONTESTABLE."]:::resultado
    valid11["xxx"]:::resultado
    niveles10 --> epic11 --> diag_epic --> valid11
  end

  %% --- 12. CONTRIBUCIÓN Y 13. ARCO COMPLETO ---
  subgraph S1213 [12. CONTRIBUCIÓN Y 13. ARCO COMPLETO]
    direction TB
    contrib12["<b>ALCANCE:</b> Arquitectura de assurance que hace evaluable la relación output ➔ acción en el punto de uso, con XAI como instrumentación de evidencia estructurada."]:::nodo
    limites12["<b>LÍMITES DECLARADOS:</b>."]:::gap
    arco13["<b>TESIS (Arco Completo):</b> 2020: 'Los modelos no se explican'. ➔ Beyond Transparency: 'Explicaciones no resuelven accountability'. ➔ UVC: 'El problema es cuándo es defendible actuar sobre un output'. ➔ IEEE: 'El punto de evaluación no es el modelo. Es la relación output ➔ acción en el punto de uso.' (Hevner, Wong, Hawkins, Leveson)."]:::arco
    valid11 --> contrib12 --> limites12 --> arco13
  end
