
## Summary

On a 1 M–vector dataset (dbpedia-1536), **Qdrant** achieves a median latency of **3.54 ms** (p95: 4.95 ms; p99: 8.62 ms), whereas **Redis + RediSearch** records a median of **140.65 ms** for the same query. With its upgraded Query Engine, Redis can hit sub-millisecond latencies on text searches and average under **10 ms** for ANN queries, yet still trails Qdrant on pure vector workloads. In head-to-head comparisons with Milvus, Weaviate, and Elasticsearch, Qdrant consistently delivers the lowest latencies without sacrificing accuracy.

---

## 1. Redis + RediSearch

### 1.1 General Features  
- RediSearch adds ANN capabilities to Redis’s key-value core[^1].  
- Single-threaded benchmark on dbpedia-1M:  
  - **p50 = 140.65 ms**  
  - **p95 = 160.85 ms**  
  - **p99 = 167.35 ms**[^2].  
- With its multi-threaded Query Engine, Redis claims sub-millisecond text-search latencies (< 1 ms) and averages < 10 ms for ANN[^3].

### 1.2 Behavior Under Load  
- Latency increases with more threads due to multiple inverted-index scans (O(log n) per segment)[^4].  
- Excellent for exact key-value lookups and full-text search, but less optimal for high-dimensional ANN.

---

## 2. Qdrant

### 2.1 Official Benchmarks  
- dbpedia-1M (1536 d, single thread):  
  - **p50 = 3.54 ms**  
  - **p95 = 4.95 ms**  
  - **p99 = 8.62 ms**[^2].  
- January/June 2024 updates show improvements across all precision thresholds versus competitors[^5].

### 2.2 Historical Performance  
- In 2022, on deep-image-96 d: median latency of **24.7 ms**[^6].

### 2.3 Low-Latency Optimization  
- Parallelism by segment equal to the number of CPU cores[^7].  
- Immutable data structures (PHF) and I/O optimizations minimize “cold read” latency[^8].

---

## 3. Latency Comparison

| Metric               | Redis + RediSearch | Qdrant      |
|----------------------|--------------------|-------------|
| **p50 latency**      | 140.65 ms          | 3.54 ms     |
| **p95 latency**      | 160.85 ms          | 4.95 ms     |
| **p99 latency**      | 167.35 ms          | 8.62 ms     |
| **Multi-threading**  | ↑ latency          | → stable    |
| **Text searches**    | < 1 ms             | N/A         |
| **Avg. ANN latency** | < 10 ms            | ≈ 3.5 ms    |

---

## Conclusion

For **high-dimensional vector search**, **Qdrant** delivers the lowest and most consistent latencies, even under parallel load. Although **Redis + RediSearch** excels at text and exact K-V lookups, if pure ANN speed is your priority, **Qdrant** is the optimal choice.

---

## Updated References

[^1]: [RediSearch: A High Performance Search Engine as a Redis Module](https://redis.io/resources/redisearch-a-high-performance-search-engine-as-a-redis-module/) :contentReference[oaicite:0]{index=0}  
[^2]: [Single-node benchmarks – Qdrant](https://qdrant.tech/benchmarks/single-node-speed-benchmark/) :contentReference[oaicite:1]{index=1}  
[^3]: [Introducing Redis 7.2 – Redis Blog](https://redis.io/blog/introducing-redis-7-2/) :contentReference[oaicite:2]{index=2}  
[^4]: [Redis Improves Performance of Vector Semantic Search with Multi-threading – InfoQ](https://www.infoq.com/news/2024/07/redis-vector-database-genai-rag/) :contentReference[oaicite:3]{index=3}  
[^5]: [Qdrant Benchmarks 2024 – Qdrant Blog](https://qdrant.tech/blog/qdrant-benchmarks-2024/) :contentReference[oaicite:4]{index=4}  
[^6]: [ann-benchmarks – GitHub (deep-image dataset)](https://github.com/erikbern/ann-benchmarks) :contentReference[oaicite:5]{index=5}  
[^7]: [What is a Vector Database? (Qdrant Architecture)](https://qdrant.tech/articles/what-is-a-vector-database/) :contentReference[oaicite:6]{index=6}  
[^8]: [Qdrant Internals: Immutable Data Structures](https://qdrant.tech/articles/immutable-data-structures/) :contentReference[oaicite:7]{index=7}  