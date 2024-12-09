---
title: Memory Alignment
weight: 1
---

# Memory alignment

Memory alignment is a concept that is important to understand when working with memory in modern computer systems. It is
a concept that is often overlooked by developers, but it can have a significant impact on the performance of your code.

In this article, we will explore what memory alignment is, why it is important, and how it can affect the performance of
your code.

## What is memory alignment?

Memory alignment refers to the way that data is arranged in memory. In modern computer systems, memory is typically
organized into bytes, which are the smallest unit of memory that can be addressed. When data is stored in memory, it is
stored in blocks of bytes, with each block containing a certain number of bytes. The size of the block is determined by
the system's memory architecture. For example, in a system with a 64-bit memory architecture, memory is organized into
blocks of 8 bytes.

When data is stored in memory, it is important that it is aligned on certain boundaries. These boundaries are determined
by the size of the data being stored. For example, data that is 4 bytes in size should be aligned on a 4-byte boundary,
data that is 8 bytes in size should be aligned on an 8-byte boundary, and so on.

## Why is memory alignment important?

Memory alignment is important for several reasons. First, memory alignment can affect the performance of your code. When
data is not aligned on the correct boundaries, it can result in slower memory access times. This is because the system
may need to perform additional operations to access the data, which can slow down the execution of your code.

Second, memory alignment is important for ensuring that your code is portable across different systems. Different
systems have different memory architectures, and the way that data is aligned in memory can vary between systems. By
ensuring that your data is aligned correctly, you can make sure that your code will run correctly on different systems.

## How does memory alignment affect performance?

Memory alignment can have a significant impact on the performance of your code. When data is not aligned on the correct
boundaries, it can result in slower memory access times. This is because the system may need to perform additional
operations to access the data, which can slow down the execution of your code.

## Example in Go

Let's consider an example in go to illustrate this point. Suppose you have a struct that contains three fields: an
8-byte uint64, a 2-byte uint16, and a 1-byte uint8.

```go
type First struct {
A uint8  // Uses 1 byte, followed by 7 bytes of padding to align the next field
B uint64 // 8-byte aligned uint64 for the passport ID
C uint16 //2-byte aligned uint16
}
```

In the `First` struct, the `A` field takes up 1 byte from the 8 byte word. The next field, `B`, is a 64-bit integer that
requires an 8-byte alignment. To align `B` on an 8-byte boundary, the compiler inserts 7 bytes of padding after `A`. The
`C` field is a 16-bit integer that requires a 2-byte alignment. Since `B` is already aligned on an 8-byte boundary, no
additional padding is required for `C`. This results in structure that is 24 bytes in size.

```go
type Second struct {
B uint64 // 8 bytes
C uint16 // 2 bytes
A uint8  // 1 byte
}
```

In the `Second` struct, the fields are reordered to optimize memory alignment. The 8 byte `B` field is placed first,
followed by the 2 byte `C` field, and finally the 1 byte `A` field. This results in a structure that is 16 bytes in
size.

To verify the memory alignment of the struct, we can use the `unsafe` package in Go. The `unsafe.Offsetof` function
returns the offset of a field within a struct in bytes.

```go
fmt.Printf("Field offsets in First: A = %d, B = %d, C = %d\n",
unsafe.Offsetof(First{}.A),
unsafe.Offsetof(First{}.B),
unsafe.Offsetof(First{}.C),
)
```

    Field offsets in First: A = 0, B = 8, C = 16
    Field offsets in Second: B = 0, C = 8, A = 10

The size of structs can be determined using the `unsafe.Sizeof` function.

```go
fmt.Printf("Size of First: %d bytes\n", unsafe.Sizeof(First{}))
```

    Size of First: 24 bytes
    Size of Second: 16 bytes

Now let's compare the memory access times for the two structs. Let's create simple function to populate the struct
fields with consecutive numbers and sum them up.

```go
func populate[T TestSubject]() ([]T, time.Duration) {
t := reflect.TypeOf((*T)(nil)).Elem().Name()
start := time.Now()
var subjects []T
for i := 0; i < NumStructs; i++ {
subjects = append(subjects, newStruct(t, i).(T))
}
return subjects, time.Since(start)
}
```

```go
func iterate[T TestSubject](subjects []T) (int, time.Duration) {
start := time.Now()
sum := 0
for _, s := range subjects {
sum += s.sum()
}
return sum, time.Since(start)
}
```

TestSubject interface is defined as follows:

```go
type TestSubject interface {
sum() int
}
```

Number of structs to create is defined to be 1_000_000.

    Populated array of First structs in 42.558638ms
    Populated array of Second structs in 29.349922ms
    Iterated over array of First structs in 2.010232ms
    Iterated over array of Second structs in 1.52971ms

As we can see, the Second struct, which is optimized for memory alignment, has better performance compared to the First struct. This is because the fields in the Second struct are ordered in such a way that they are aligned on the correct boundaries, resulting in faster memory access times.

