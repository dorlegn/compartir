```mermaid
graph TD
    %% Estilos
    classDef period fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef gap fill:#fff4dd,stroke:#d4a017,stroke-width:1px;
    classDef tension fill:#ffebee,stroke:#c62828,stroke-width:1px;
    classDef conclusion fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;

    subgraph P1 ["2020: PhD Research Plan"]
        A["<b>Enfoque: XAI Técnico</b><br/>'Methodology for symbolic explanations'"]
        B["<b>Marco: Responsible AI</b><br/>Fairness + Explainability + Accountability"]
        C["<b>El GAP:</b><br/>Modelos opacos y falta de metodología"]
    end

    A --> B --> C
    C --> GIRO{{"<b>2021-2023: EL GIRO ANALÍTICO</b>"}}

    subgraph P2 ["Manuscrito: Beyond Transparency"]
        D["<b>Revisión Narrativa</b><br/>64 documentos (2019-2025)"]
        
        subgraph Tensions ["Las 4 Tensiones"]
            T1["<b>T1: Regulación</b><br/>Disclosure ≠ Accountability"]
            T2["<b>T2: Ética</b><br/>Riesgo de Ethics-washing"]
            T3["<b>T3: Confianza</b><br/>Reliance vs. Trust"]
            T4["<b>T4: Fragmentación</b><br/>Desconexión Organizativa"]
        end
    end

    GIRO --> D
    D --> T1 & T2 & T3 & T4

    subgraph Final ["Conclusión"]
        E["<b>Transparencia 'Overloaded'</b>"]
        F["<b>Diagnóstico Final</b><br/>La XAI es indispensable pero insuficiente."]
    end

    T1 & T2 & T3 & T4 --> E
    E --> F

    class A,B period;
    class C gap;
    class T1,T2,T3,T4 tension;
    class E,F conclusion;
