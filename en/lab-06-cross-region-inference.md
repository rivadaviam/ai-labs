# Lab 06: Resilient LLM Gateway — Anti-Throttling with Cross-Region Inference

> 🇪🇸 [Versión en español](../lab-06-cross-region-inference.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**Resilient LLM Gateway** — Proxy with observability that implements anti-throttling with cross-region inference, intelligent retry, and circuit breaker, demonstrating with data that it works under load

---

## 2. Main skill

**AI systems resilience in production** — Design of resilience patterns (retry, circuit breaker, fallback), observability with real-time metrics, and validation under controlled load conditions.

---

## 3. Problem to solve

An ecommerce application calls Bedrock directly to generate product descriptions and respond to customer questions. During high-demand events (Black Friday, launches), Bedrock returns `ThrottlingException` on a significant percentage of requests. The immediate options are: Provisioned Throughput (expensive and requires a time commitment), ignore the problem (unacceptable in production), or implement a resilience layer.

The team must build that layer and demonstrate with data that it works under load — not just theorize that it works.

---

## 4. Target product

A FastAPI `llm-gateway` service that acts as a proxy between the application and Bedrock, implementing:

1. **Cross-region inference routing:** uses Bedrock cross-region inference profiles (`us.anthropic.claude-3-5-haiku-20251001-v1:0`) to automatically distribute between `us-east-1` and `us-west-2`
2. **Retry with exponential backoff + jitter:** on `ThrottlingException`, retries with exponential delay + random jitter to avoid thundering herd
3. **Circuit breaker:** if more than N% of requests in a sliding window fail, the circuit breaker opens and requests go directly to fallback without trying the primary for X seconds
4. **`/metrics` endpoint:** returns JSON with the gateway's real-time state

`/metrics` payload:
```json
{
  "total_requests": 50,
  "throttled_count": 17,
  "retry_success_count": 15,
  "circuit_breaker_open": false,
  "circuit_breaker_trips": 1,
  "region_distribution": { "us-east-1": 33, "us-west-2": 17 },
  "latency_p50_ms": 312,
  "latency_p95_ms": 847,
  "failure_rate_percent": 0
}
```

**Suggested validations:**

- Run a load test directly against Bedrock (without gateway) and record the throttling rate
- Run the same test through the gateway and verify the success rate improves significantly
- Verify that `/metrics` correctly reflects the request distribution across regions, successful retries, and circuit breaker state
- Force sustained throttling and verify the circuit breaker opens and routes to fallback
- Verify that after the timeout period, the circuit breaker transitions to half-open and probes the primary region

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Base gateway with retry and cross-region:**
- FastAPI with `/invoke` endpoint
- Retry with exponential backoff + jitter
- Cross-region fallback working
- Bedrock mock for local development without cost

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — Circuit breaker + metrics + load test:**
- Circuit breaker with configurable sliding window
- `/metrics` endpoint
- `load_test.py` script
- Full validations working

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

**Out of MVP scope:** gateway authentication, rate limiting toward upstream clients, metrics persistence, ECS deploy.

---

## 6. Extension

- **Three regions:** Add `eu-west-1` as a third fallback. Independent circuit breaker per region. Measure the latency impact with the European region.
- **Metrics in Prometheus + Grafana:** Replace the JSON endpoint with metrics in Prometheus format. Real-time dashboard during the load test.
- **Semantic cache:** For very similar queries, return the cached response. Measure hit rate and impact on latency and cost.
- **Per-client rate limiter:** If multiple services use the gateway, rate-limit by `client_id` in the header.
- **Deploy on ECS Fargate:** Containerize with Docker and deploy with an ALB in front. Compare local vs. AWS latency.

---

## 7. Suggested stack

**AWS (recommended):**

| Service | Role |
|----------|-----|
| Bedrock + Cross-region profiles | `us.anthropic.claude-3-5-haiku-20251001-v1:0` — automatic multi-region routing |
| Python 3.12 + FastAPI + boto3 | Proxy framework |
| asyncio | Request concurrency |
| prometheus_client | Metrics in standard format |

Estimated cost: < $3 for load tests (50-100 requests × 3-5 runs).

**Local alternative ($0 cost):**
A `mock_bedrock.py` mock server that simulates the Bedrock API with configurable throttling:
```
python mock_bedrock.py --throttle-rate 0.4 --port 9090
BEDROCK_ENDPOINT=http://localhost:9090 uvicorn gateway:app --port 8080
```
Lets you develop and demo the full gateway — including the circuit breaker — without incurring any AWS cost. Recommended for the first 1.5 weeks of development.

---

## 8. Terrain to explore

- How does a circuit breaker work? What are the states (closed, open, half-open) and the transitions between them?
- What is throttling in the context of LLM APIs and why does cross-region inference help distribute it?
- What is thundering herd and how does retry jitter mitigate it? How much jitter is enough?
- What's the trade-off between resilience and latency in a gateway? How much extra latency is acceptable to gain resilience in production?
- What limitations does keeping the circuit breaker state in process memory have? What are the implications if the gateway restarts?
- Why is it important that circuit breaker parameters (threshold, window, timeout) are configurable without redeploying?
- When are mock metrics useful for development and when aren't they sufficient to validate real behavior?
- Did AI implement the circuit breaker correctly with all its states (closed, open, half-open), or did it simplify the logic in a way that wasn't useful in practice?
- In which part of the lab did AI save the most time: the Bedrock mock, the resilience logic, or the load test?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — Resilient LLM Gateway — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Copilot | Generating the FastAPI gateway skeleton and async boto3 client | |
| Claude | Designing and implementing the circuit breaker and reviewing jitter logic | |
| Windsurf | Refactoring the code into modules and generating load_test.py | |

### Biggest AI impact in this lab
[Did AI implement the circuit breaker correctly with a sliding window? Did it help
understand the difference between cross-region inference profiles and multi-region endpoints?
Did it generate the Bedrock mock that simulated throttling correctly?]

### Moment where AI didn't help or introduced problems
[Did AI generate a circuit breaker implementation without jitter that caused thundering
herd? Did it use asyncio.sleep incorrectly inside FastAPI? Did the retry code fail to
re-raise the exception correctly after exhausting retries?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Implement the Bedrock mock first and develop the entire gateway against the
mock — it speeds up the cycle and avoids costs. The circuit breaker is the hardest
component and AI tends to simplify it incorrectly: ask it to explain the algorithm
before writing the code."]
```
