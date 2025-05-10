
# AAOIFI Standards Enhancement System

A comprehensive multi-agent AI system for reviewing, enhancing, validating, and reporting on AAOIFI Islamic finance standards.

---

## 📖 Table of Contents
1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Configuration](#configuration)
4. [Workflow](#workflow)
5. [Class Documentation](#class-documentation)
   - [StandardDocument](#standarddocument)
   - [BaseAgent](#baseagent)
   - [ReviewAgent](#reviewagent)
   - [EnhancementAgent](#enhancementagent)
   - [ValidationAgent](#validationagent)
   - [FinalReportAgent](#finalreportagent)
   - [VectorDBManager](#vectordbmanager)
   - [AAOIFIStandardsEnhancementSystem](#aaoifistandardsenhancementsystem)
6. [Usage Examples](#usage-examples)
7. [Project Context](#project-context)
8. [Expected Outcome](#expected-outcome)
9. [Storage & Output](#storage--output)
10. [Notes & Best Practices](#notes--best-practices)

---

## 🚀 Overview

This system processes AAOIFI standard documents (PDFs) and leverages AI agents to:
- **Review**: Extract key elements and definitions.
- **Enhance**: Propose clear, modern, technology-aligned improvements.
- **Validate**: Ensure Shariah compliance and practical applicability.
- **Report**: Compile a structured final report.

Underlying technologies:
- **OpenAI Embeddings** (`text-embedding-ada-002`)
- **Chat Models** (`gpt-4`, `gpt-3.5-turbo`)
- **ChromaDB** for vector storage.

---

## 🏛️ Architecture

```mermaid
flowchart LR
  subgraph PDF Processing
    A[PDF Folder] --> B[extract_text_from_pdf()]
    B --> C[clean_text()]
    C --> D[split_text_into_chunks()]
    D --> E[create_vector_database()]
  end

  subgraph Agents Pipeline
    E --> F[ReviewAgent] --> G[EnhancementAgent] --> H[ValidationAgent] --> I[FinalReportAgent]
  end

  I --> J[results/<standard>_final_report.md]
  I --> K[results/<standard>_results.json]
```

---

## ⚙️ Configuration

```python
class Config:
    PDF_FOLDER = "pdf_eng/"
    OUTPUT_DIR = "results/"
    DB_DIRECTORY = "aaoifi_vector_db/"
    COLLECTION_NAME = "aaoifi_standards"
    CHUNK_SIZE = 1000
    CHUNK_OVERLAP = 200
    EMBEDDING_MODEL = "text-embedding-ada-002"
    GPT4_MODEL = "gpt-4"
    GPT35_MODEL = "gpt-3.5-turbo"
```

- Set `OPENAI_API_KEY` environment variable before running.
- Create directories automatically if missing.

---

## 🔄 Workflow

1. **PDF Extraction**  
   `extract_text_from_pdf(pdf_path: str) -> str`
2. **Text Cleaning**  
   `clean_text(text: str) -> str`
3. **Chunking**  
   `split_text_into_chunks(text: str, standard_name: str) -> List[Dict]`
4. **Vector DB**  
   `create_vector_database(documents: List[Dict]) -> chromadb.PersistentClient`
5. **Agent Pipeline**  
   - `ReviewAgent.execute(standard: StandardDocument) -> Dict`
   - `EnhancementAgent.execute(review: Dict) -> Dict`
   - `ValidationAgent.execute(enhancement: Dict, review: Dict) -> Dict`
   - `FinalReportAgent.execute(all_results: Dict) -> Dict`

---

## 📝 Class Documentation

### StandardDocument
```python
class StandardDocument:
    def __init__(self, name: str, content: str):
        self.name = name
        self.content = content
```

### BaseAgent
Abstract class for all agents.
```python
class BaseAgent:
    def __init__(self, name: str, description: str, model_name: str):
        self.name = name
        self.description = description
        self.model_name = model_name
        self.llm = ChatOpenAI(...)

    def execute(self, input_data):
        raise NotImplementedError
```

### ReviewAgent
Extracts core elements.
```python
class ReviewAgent(BaseAgent):
    def execute(self, standard: StandardDocument) -> Dict[str, Any]:
        # Returns dictionary with sections:
        # - core_principles
        # - key_definitions
        # - main_requirements
        # - compliance_criteria
        # - implementation
```

### EnhancementAgent
Proposes improvements.
```python
class EnhancementAgent(BaseAgent):
    def execute(self, review_result: Dict[str, Any]) -> Dict[str, Any]:
        # Returns 'enhancement_proposals'
```

### ValidationAgent
Validates enhancements.
```python
class ValidationAgent(BaseAgent):
    def execute(self, enhancement_result: Dict[str, Any], original_review: Dict[str, Any]) -> Dict[str, Any]:
        # Returns 'validation_result'
```

### FinalReportAgent
Compiles final report.
```python
class FinalReportAgent(BaseAgent):
    def execute(self, all_results: Dict[str, Any]) -> Dict[str, Any]:
        # Returns 'final_report'
```

### VectorDBManager
Handles ChromaDB operations.
```python
class VectorDBManager:
    def __init__(self, db_directory: str, collection_name: str):
        # Loads or creates collection
    def get_standard_content(self, standard_name: str) -> str
    def list_available_standards(self) -> List[str]
```

### AAOIFIStandardsEnhancementSystem
Orchestrator.
```python
class AAOIFIStandardsEnhancementSystem:
    def list_available_standards(self) -> List[str]
    def process_standard(self, standard_name: str) -> Dict[str, Any]
    def save_results(self)
```

---

## 📋 Usage Examples

```python
from scrap import AAOIFIStandardsEnhancementSystem

system = AAOIFIStandardsEnhancementSystem()
print("Available:", system.list_available_standards())

# Process a standard
results = system.process_standard("AAOIFI_Financial_Instruments")
system.save_results()
```

---

## 🧩 Project Context

This system addresses a challenge to **enhance an AAOIFI standard** using a clear, **multi-agent AI architecture** with segregation of duties:
- **Review Agent**: Extracts key elements.
- **Enhancement Agent**: Suggests AI-driven improvements.
- **Validation Agent**: Ensures Shariah compliance and practical applicability.

---

## 🎯 Expected Outcome

A prototype illustrating:
- Autonomous agent interactions.
- Structured improvement and validation workflow.
- Transparent reporting.
- Compliance with Islamic finance regulations.

---

## 💾 Storage & Output

- **Vector DB**: `aaoifi_vector_db/`
- **Results JSON**: `results/<standard>_results.json`
- **Report Markdown**: `results/<standard>_final_report.md`

---

## 📌 Notes & Best Practices

- **Chunk Size**: Adjust `CHUNK_SIZE` and `CHUNK_OVERLAP` based on document length.
- **Embedding Consistency**: Recreate DB if model changes to prevent dimension mismatches.
- **Error Handling**: Safe DB creation avoids locked-file issues.
- **API Keys**: Securely manage OpenAI credentials.


This prototype is not only a demonstration of AI capabilities but also a blueprint for integrating explainable, compliant AI into regulatory standard development workflows.


