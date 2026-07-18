#  FileFlow
### Forensic Local AI File Organisation Engine for Legal & Professional Archives

---

> ###  Data Privacy & Verification Notice
> **Proprietary Codebase:** The complete source codebase for this project is hosted in a private repository to maintain local system sovereignty, protect LLM orchestration logic, and safeguard personal data handling. 
> 
> **Live Verification:** This repository serves as the public system architecture map and technical documentation. I am highly comfortable performing a live, end-to-end system demonstration and code walk-through during an interview to verify all architectural implementations and logic flows.

---

##  Executive Summary

**FileFlow** is an offline, forensic file organization engine designed specifically for legal professionals and complex administrative environments. 

Instead of relying on fragile file names (e.g., `CV_FINAL_v3.pdf`), FileFlow recursively scans messy directories, reads the *actual contents* of every document using a local AI cognition stack, and organizes them into a clean, structured archive. It handles corrupted files, unreadable scans, and duplicates seamlessly — without ever risking original files or sending data to the cloud.

---

##  The Four Architectural Guarantees

FileFlow is built around four strict safety constraints that cannot be overridden by configuration:

1.  **Non-Destructive:** Original files are never modified or deleted until a bit-perfect copy is MD5-verified at the destination.
2.  **100% Local:** All AI inference runs locally via [Ollama](https://ollama.ai) on the host machine. Zero network calls to external APIs. No file content ever leaves the computer.
3.  **Full Rollback:** A persistent `TransactionLedger` logs every operation. A single command can completely reverse any organization session back to its original state.
4.  **Forensic Audit Trail:** A `Forensic_Manifest.json` is generated for every run, mapping every file's source path, target path, and MD5 hash.

---

##  The Phase 2 Cognition Stack

Every discovered file passes through a multi-tier analysis pipeline before any movement is planned.

```text
File
 │
 ├─► UnifiedExtractor     — raw text/metadata extraction (PDF, DOCX, image OCR)
 │       └─► ProcessPool  — each PDF extracted in an isolated child process
 │                          (prevents corrupted files from hanging the main process)
 │
 ├─► Judge (AI)           — Sovereign Archivist prompt → category + confidence ruling
 │       └─► Bridge       — Ollama reliability layer (health checks, fallback)
 │
 ├─► Inspector            — folder context propagation (fixes grounding errors)
 │
 └─► MD5 hash             — content-based deduplication fingerprint

Classification Decision Tree

File arrives at Judge
       │
       ├─ Bridge healthy?
       │     YES → Sovereign Archivist prompt → AI ruling (confidence 0.0–1.0)
       │     NO  → Skip to rule-based path
       │
       ├─ confidence >= 0.75?
       │     YES → Use AI category + entity
       │     NO  → Fall back to rule-based classification
       │
       └─ Rule-based: keyword match against config.json category definitions
             MATCH → Assign category
             NO MATCH → "Unknown" → Inspector context check → Quarantine

The Four Execution Phases

1.  Forensic Discovery (scanner.py): Deep recursive scan of the source
    directory. Ghost files (<1KB) and corrupted files are instantly quarantined.
    Built to be cycle-safe via resolved-path deduplication.
2.  Cognition Staging (manager.py): Runs the Cognition Stack (AI + rule-based
    parsing). If Ollama is offline, the system degrades gracefully to rule-based
    classification silently.
3.  Virtual Reconstruction (StagingManager): Builds a complete picture of the
    organized structure in memory. Generates the Forensic_Manifest.json and
    Duplicate Map (MD5 exact matches + semantic cosine similarity). Nothing
    physical happens in this phase.
4.  Atomic Commitment (executor.py): On user approval, files are copied,
    MD5-verified, and logged in the TransactionLedger. Any failure flags the
    session for rollback.

Technology Stack & Benchmarks

  - Core Logic: Python 3.10+ (requires := operator for hash loops), FastAPI
    (pywebview bridge)
  - Inference Engine: Ollama local server running ministral-3:3b
    (classification) and qwen3-embedding:4b (deduplication)
  - Vision OCR: qwen3-vl:4b for scanned images and unreadable PDFs
  - Vector Memory: LanceDB (vectors.lance) for semantic similarity search
  - PDF Extraction Architecture: Isolated child processes via
    multiprocessing.Pool with hard timeouts to prevent malformed PDFs from
    crashing the system.
  - Performance: Scans and processes standard legal folders at
    approximately 200+ files per second (I/O bound) on consumer hardware.

Architected and maintained by Sandiso Mazibuko.


Let me know when this one is successfully created! We only have one left (`Windows-Security-Audit`).
