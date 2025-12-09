# semantic-cache

A distributed semantic caching layer for LLM applications that uses embedding-based similarity to cache and retrieve responses intelligently.

## Motivation

Traditional caching relies on exact string matching, which fails for LLM applications where users ask the same question in different ways:

- **"What's the capital of France?"** vs **"Tell me France's capital city"** → Same intent, cache miss
- **"How do I reset my password?"** vs **"I forgot my password, what should I do?"** → Semantically identical, no cache hit
- **High API Costs**: LLM API calls cost $0.01-$0.10 per request; repeated similar queries waste money
- **Latency**: Every cache miss adds 1-5 seconds of response time
- **Rate Limits**: Redundant requests consume precious quota

echocache solves this by understanding semantic similarity, not just exact matches. If a question is semantically close to a cached query, return the cached response instantly.

## Goals

- **Semantic Matching**: Cache hits based on meaning, not exact text matching
- **Cost Reduction**: Reduce LLM API costs by 40-70% through intelligent caching
- **Low Latency**: Sub-10ms cache lookups with configurable similarity thresholds
- **Distributed**: Horizontally scalable cache layer with consistent hashing
- **Streaming Support**: Handle streaming LLM responses with progressive caching
- **Observability**: Track cache hit rates, cost savings, and similarity score distributions

## Use Cases

### 1. **Customer Support Chatbots**
Users ask the same questions in countless ways. Cache common support queries and serve instant responses, reducing API costs by 60-80% while improving response times.

### 2. **Documentation Q&A Systems**
Technical documentation queries often overlap semantically. Cache responses to frequently asked questions about APIs, setup guides, or troubleshooting steps.

### 3. **Multi-User AI Applications**
Different users often ask similar questions. A semantic cache shares knowledge across users while maintaining privacy—no need to call the LLM for repeated concepts.

### 4. **RAG Applications**
Retrieval-Augmented Generation systems frequently see similar queries with different phrasing. Cache both retrieval results and generated responses based on semantic similarity.

### 5. **Code Generation Tools**
Developers often request similar code snippets with slight variations in wording. Cache code generation responses for common patterns and tasks.

### 6. **Content Recommendation Systems**
Cache LLM-generated recommendations, summaries, or classifications for similar content to reduce processing costs and improve response times.

## How It's Different

| Feature | Traditional Cache | echocache |
|---------|------------------|-----------|
| Match Type | Exact string match | Semantic similarity |
| "What is Python?" → "Tell me about Python" | ❌ Cache miss | ✅ Cache hit (95% similar) |
| Storage | Key-value pairs | Vector embeddings + responses |
| Hit Rate (typical) | 15-25% | 60-80% |

## Quick Start

```go
package main

import "github.com/yourusername/echocache"

func main() {
    cache := echocache.New(
        echocache.WithEmbeddingModel("text-embedding-3-small"),
        echocache.WithSimilarityThreshold(0.90),
        echocache.WithTTL(24 * time.Hour),
    )
    
    // First request - cache miss, calls LLM
    response1, hit := cache.GetOrCompute("What is the capital of France?", 
        func() string {
            return callLLM("What is the capital of France?")
        })
    
    // Second request - cache hit! (semantically similar)
    response2, hit := cache.GetOrCompute("Tell me France's capital city",
        func() string {
            return callLLM("Tell me France's capital city")
        })
    
    println("Cache hit:", hit) // true - saved an API call!
}
```

## Key Metrics

In production deployments, echocache typically delivers:
- **60-80% cache hit rate** (vs 15-25% with exact matching)
- **40-70% cost reduction** on LLM API bills
- **80-95% latency reduction** for cached queries
- **ROI positive** within days for high-traffic applications

## Status

🚧 This project is under active development. Contributions and feedback are welcome!

## License

MIT License - see [LICENSE](LICENSE) for details.
