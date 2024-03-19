# A improved mempool

Original forked from @Dreamacro

## Why

@xiaci wrote this pool, and it's improved by @Dreamacro.

It's used for the common memory usage case in Go to reduce STW time in every GC round.

However, it will still cause allocation in Dreamacro version, to know why, [see the description about sync.Pool](https://pkg.go.dev/sync#Pool)

Even though using a non-pointer element will be more convenience for developer， it will cause unnecessary allocation.

According to the benchmark, using non-pointer []byte spend 70ns+ while pointer one only spend 20ns.

## How to use
```
buf := mempool.Get( YOUR_SIZE(from 1-65536) )
file.Read(buf.B)
file.Write(buf.B)
...after many read and write operation
mempool.Put(buf)
```

### DON'T DO
This will make buffer starting from 5, and it cannot be resized after that.
```
buf.B = buf.B[5:]
```

### Should DO
```
newbuf := buf.B[5:]
```

But this is allowed
```
buf.B = buf.B[:5]
```

Or more convenience
```
buf.SetLen(5)
```
