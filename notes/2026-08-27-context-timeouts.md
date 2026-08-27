# Context Timeouts in Go

Date: 2026-08-27

## Notes

- `context.WithTimeout` is the cleanest way to enforce a deadline on outgoing requests.
- Always call the cancel function via `defer cancel()` to avoid leaking resources.
- The returned `ctx.Done()` channel fires when the timeout expires, but the goroutine still needs to stop work.
- For database calls, prefer `context.WithTimeout` over setting timeouts on the driver.
- Check `ctx.Err()` after a call to distinguish deadline exceeded from cancellation.

## Example snippet

```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()

result, err := svc.Get(ctx, id)
if errors.Is(err, context.DeadlineExceeded) {
    return nil, ErrTimeout
}
```

## Takeaways

Use timeouts at the edges of the service, not just inside dependencies. This makes behavior predictable and easier to trace.