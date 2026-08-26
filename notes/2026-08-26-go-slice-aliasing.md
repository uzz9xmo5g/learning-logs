# Go slice aliasing note

While debugging a service today I hit a subtle slice aliasing bug.

## What happened

I passed `s[:2]` into a helper that appended an element. Because the sub-slice still shared the same backing array, the append overwrote an element outside the sub-slice.

```go
s := []int{1,2,3,4}
sub := s[:2]
sub = append(sub, 9)
```

After this, `s[2]` is now `9`, not `3`.

## Fix

Use a full slice expression to limit capacity:

```go
sub := s[:2:2]
```

Now append allocates a new array instead of reusing the old one.

## Reminder

- Beware when passing slices to functions that may append.
- Use `copy` or `slices.Clone` when ownership is unclear.