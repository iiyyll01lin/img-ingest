# PDF Image Ingestion

## Version

This project is still in development, see the latest branch: **202504-rc1**

## Summary

Image to Pre-Embedding Dataset by generation & validation pre-steps.

#### Image Extraction Workflow

```mermaid
flowchart LR
    A(Extraction<br>Datasets<br>Generation) --> B(Extraction<br>Validation)
```

#### Image Extraction Pre-Validation Datasets Generation

```mermaid
flowchart LR
    %% Data Nodes
    Z(PDF)
    A(IMG)
    B(BASE64)
    N(CODE-1)
    W(TEXT-1)
    Y(IMG)
    F(TEXT-2)
    G(TEXT-3)
    O(CODE-2)
    H{{Pre-Embedding Set}}
    I{{Validation Set}}
    
    %% Flow Paths
    Z -- NV-Ingest/<br>Unstructured/<br>Pytesseract --> A
    A -- VLLM-1 --> N
    N -- LLM-1 --> F
    F --> I
    N -- render --> Y
    Y -- VLLM-1--> G --> I
    N -- Embedding<br>Model--> H
    A -- Encoder --> B
    B -- VLLM-2 --> W
    W -- Embedding<br>Model --> H
    W -- LLM-2 --> O
    O --> I
```
#### Pre-Validation Datasets Distributions

- Pre-Embedding dataset:
  - Code-1: VLLM-1
  - Text-1: VLLM-2
- Validation Set Store:
  - Code-2: LLM-2
  - Text-2: LLM-1
  - Text-3: VLLM-1

#### PDF Image Extraction Validation Flow

```mermaid
flowchart LR
    %% Data Nodes
    Z(Text-2)
    A(Text-3)
    B(Text-1)
    N(Code-1)
    W(Text-3)
    Y(Code-2)
    F{{Embeddings}}
    O@{ shape: lin-cyl, label: "Vector<br>Database" }
    
    %% Flow Paths
    subgraph Val-1
    Z <-. Contextual<br>Keyword<br>Similarity .-> A
    end

    subgraph Val-2
    Y <-. Keyword<br>Check .-> N
    end

    subgraph Val-3
    W <-. Keywork<br>Check .-> B
    end

    subgraph Store
    B --> F
    N --> F
    F --> O
    end

    Val-1 --> N
```

#### Validation Explained

- Val-1: Represents the quality of Code-1 
- Val-2: Validates the quality of Code-1 
- Val-3: Validates the quality of Text-1 

**Contextual Keyword Similarity** please refer to GitHub: **[domain-specific-llm-eval](https://github.com/iiyyll01lin/domain-specific-llm-eval)**

#### Image Extraction Pre-Embedding Dataset Schema

- image_metadata
  - image_id
  - page_num
  - source_metadata
  - ... TBD
- content_code: Code-1
- content_description: Text-1
- content_base64: debug
