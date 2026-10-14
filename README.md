# Day 35: Production Readiness — Hardened AI Assistant

Production AI systems require explicit reliability standards before users can depend on them. This repository delivers a hardened backend built with FastAPI, Redis, FAISS, and Pytest, engineered to handle slow upstream calls, vector store crashes, and network degradation reliably without returning silent errors.

---

## 🛠️ Implemented Architectural Safeguards

- **OpenAI Circuit Breaker**: Tracks consecutive call failures in an internal state machine. After 3 consecutive failed requests, the breaker trips to `OPEN` for 60 seconds, instantly returning `HTTP 503 service temporarily unavailable` to avoid cascading latency and wasted API quotas.
- **Active Multi-Dependency Health Check (`GET /health`)**: A structured JSON probe checking:
  1. FAISS index readiness via an active probe vector.
  2. Redis ping connectivity.
  3. OpenAI API upstream connectivity and circuit breaker state.
- **Graceful Retrieval Degradation**: If vector search throws an unhandled hardware or memory exception, the system automatically falls back to an in-memory TF-IDF keyword retrieval before returning a response, guaranteeing a working answer over a hard crash[cite: 1].
- **Configured Logging**: Configured via Python's standard `logging` library across `DEBUG`, `INFO`, `WARNING`, and `ERROR` levels[cite: 1]. Switching levels to `WARNING` cleanly suppresses routine traces in production[cite: 1].
- **Mocked Integration Test Suite**: 5 comprehensive Pytest test cases covering end-to-end flows with mocked OpenAI responses to run deterministically with zero API cost[cite: 1].

---

## 📂 Project Structure

```text
├── app.py              # FastAPI app, CircuitBreaker, fallback logic, and /health endpoint[cite: 1]
├── test_app.py         # 5 integration tests covering degradation, breaker trips, and probes[cite: 1]
├── requirements.txt    # Project dependencies (FastAPI, Redis, FAISS, scikit-learn, Pytest)[cite: 1]
└── README.md           # Documentation, baseline benchmarks, and deployment checklist[cite: 1]
