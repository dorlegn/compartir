# Case Study — Ophthalmology: ICL Vault Prediction

This section illustrates the empirical application of the **Systematic Framework for Explainability in Trustworthy AI** to a clinical decision support system in ophthalmology.  
The case study focuses on a regression model predicting post-surgical vault size for ICL (Implantable Collamer Lens) implantation.

---

## 1. Explanation Audit

The initial audit revealed **critical gaps** across all trust dimensions.  
The model was deployed as a black-box, with no explanation mechanisms available for clinicians, regulators, or patients.  

- **Technical gaps**: No fidelity or stability assessment; clinicians could not trace decision logic.  
- **Organizational gaps**: Explanations absent from clinical workflow; no usability or governance processes in place.  
- **Regulatory gaps**: No artifacts aligned with AI Act Article 13; end-users could not contest automated suggestions.  
- **Societal gaps**: No patient-facing explanations, limiting accessibility and public trust.  

**Summary**: The system lacked any structured explainability strategy, leading to a status of *“Critical gaps – No explanation mechanisms currently available.”*  

### Necesidades de investigación
- **Técnicas**: adaptar métricas de fidelidad y estabilidad a contextos biomédicos con alta sensibilidad clínica.  
- **Organizativas**: estudiar cómo integrar explicaciones en flujos de trabajo médicos de manera usable.  
- **Regulatorias**: mapear sistemáticamente AI Act (Art. 13) → prácticas clínicas verificables.  
- **Sociales**: desarrollar explicaciones accesibles y culturalmente adecuadas para pacientes.  

---

## 2. Metrics Catalog and Selection

Based on the audit, a **metrics catalog** was created to operationalize explainability in this context.  
Metrics were selected to align with the needs of key stakeholders: ophthalmologists (fidelity, stability, comprehensibility), regulators (completeness, transparency), and patients (accessibility, trust calibration).

# XAI Metrics Catalog — ICL Vault Prediction

| Trustworthy Dimension | Metric | How to measure baseline | Function reference | Suggested Target |
|---|---|---|---|---|
| Fidelity (Ability) | Fidelity score | Train an interpretable surrogate on vault predictions; measure R² / correlation with original model outputs. | `fidelity_score(...)` | ≥ 0.85 |
| Stability / Robustness (Integrity) | Explanation stability | Perturb ocular biometric inputs (±2% ACD, LT, WTW) and compare SHAP importances before/after. | `explanation_stability(...)` | ≥ 0.80 |
| Comprehensibility (Benevolence) | Complexity of explanation | Average number of features in SHAP explanation (top-k). | `comprehensibility(...)` | ≤ 5 |
| Transparency (Cross-cutting) | Explanation coverage | % of patient predictions with SHAP explanation generated. | `explanation_coverage(...)` | ≥ 95% |
| Simulatability (Ability + Integrity) | Human simulation accuracy | Ask ophthalmologists to predict outcomes with explanation support → % accuracy. | `human_simulation_accuracy(...)` | ≥ 70% |
| Trust Calibration (Societal impact) | Trust gap | Compare clinicians’ confidence vs. actual accuracy. | `trust_gap(...)` | \|gap\| ≤ 0.10 |
| Regulatory alignment (Integrity) | Completeness check | Verify explanations include inputs, logic, expected impact (AI Act Art. 13). | `completeness_check(...)` | Full compliance |


---

## Function definitions

```python
def fidelity_score(y_pred_model: np.ndarray,
                   y_pred_surrogate: np.ndarray) -> Dict[str, float]:
    """
    Compute fidelity between the black-box model and a surrogate explainer.
    """
    y_pred_model = np.asarray(y_pred_model).ravel()
    y_pred_surrogate = np.asarray(y_pred_surrogate).ravel()
    corr = float(np.corrcoef(y_pred_model, y_pred_surrogate)[0, 1])
    r2 = float(r2_score(y_pred_model, y_pred_surrogate))
    return {"fidelity_corr": corr, "fidelity_r2": r2}
```

### Necesidades de investigación
| Métrica | Necesidad de investigación |
|---------|-----------------------------|
| Fidelity | Desarrollar métricas que capturen **fidelidad clínica localizada** (subgrupos de pacientes con distintas morfologías oculares). |
| Stability | Definir qué perturbaciones son **clínicamente significativas** y cómo medir estabilidad explicativa en biometría ocular. |
| Comprehensibility | Estudiar la **carga cognitiva** real de oftalmólogos: cuántos features son interpretables en práctica. |
| Transparency (Coverage) | Investigar cómo garantizar **cobertura completa** sin afectar rendimiento ni tiempos de respuesta clínica. |
| Simulatability | Definir pruebas de simulación que realmente midan **comprensión clínica**, no solo capacidad de imitar al modelo. |
| Trust calibration | Desarrollar metodologías para medir y corregir **sobreconfianza médica** en escenarios de alto riesgo. |
| Regulatory completeness | Crear **checklists técnicos verificables** que traduzcan regulaciones en criterios de explicabilidad. |

---

## 3. Validation Results (Illustrative)

A validation table was constructed to track progress from baseline to post-intervention across pre-processing, in-processing, and post-processing explainability methods.  
While the numbers below are illustrative, they demonstrate how the framework produces structured and verifiable outcomes.
| Métrica                   | Baseline (sin explicaciones) | Después (con técnicas) | Target   | Estado |
|----------------------------|-------------------------------|-------------------------|----------|--------|
| Fidelidad (R²)             | N/A                           | 0.96                    | ≥ 0.85   | ✅      |
| Estabilidad (cosine sim)   | N/A                           | 0.82                    | ≥ 0.80   | ✅      |
| Comprehensibilidad (#feats)| N/A                           | 4                       | ≤ 5      | ✅      |
| Cobertura (%)              | 0%                            | 98%                     | ≥ 95%    | ✅      |
| Simulabilidad (%)          | 42%                           | 73%                     | ≥ 70%    | ✅      |
| Trust gap (conf-acc)       | N/A                           | +0.07                   | ≤ 0.10   | ⚠️      |
| Cumplimiento regulatorio   | No                            | Sí                      | Full     | ✅      |

### Necesidades de investigación
- Requiere **datasets longitudinales y multicéntricos** para validar generalización.  
- Investigación en metodologías de **validación progresiva** (pre → in → post) sin degradar rendimiento clínico.  
- Retos en **trazabilidad de explicaciones** frente a drift de datos y nuevas técnicas quirúrgicas.  

---

## 4. Key Takeaways

- The framework enabled a **systematic translation** from stakeholder needs → audit findings → measurable metrics → validated improvements.  
- Clinicians benefit from **fidelity, stability, and simulatability**, regulators from **completeness and coverage**, and patients from **trust calibration and accessibility**.  
- Even with illustrative numbers, this case shows how the framework **closes the loop**: from identifying gaps to demonstrating compliance with Trustworthy AI requirements.  

### Necesidades de investigación
- Investigación interdisciplinar:  
  - **IA técnica**: métricas y métodos de explicabilidad contextualizados.  
  - **Medicina**: validación de utilidad clínica.  
  - **Derecho**: mecanismos de cumplimiento regulatorio verificable.  
  - **Sociología/Psicología**: confianza y accesibilidad para pacientes.  
- Persiste la laguna de cómo **estandarizar la explicabilidad** en entornos biomédicos críticos.  
