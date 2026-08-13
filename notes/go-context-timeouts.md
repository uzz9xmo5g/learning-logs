# Go Context Timeouts

Quick notes from today's debugging session.

- Always set a timeout on outgoing HTTP calls using `context.WithTimeout`.
- Default `http.Client` has no timeout — surprising in production.
- Use `defer cancel()` immediately after creating the context.
- For microservices, pass the same context through the call chain to propagate deadlines.

Example:
```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()
req, _ := http.NewRequestWithContext(ctx, "GET", url, nil)
```

Remember: a canceled context frees resources sooner than waiting for a slow response.