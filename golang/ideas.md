## Go's OO

- Methods provide message sending mechanism on any type

- Interfaces provide reusability through dynamic dispatch polymorphism

## Concurrency
- Concurrency is not parallelism. ... Concurrency is about ***dealing with*** lots of things at once. Parallelism is about ***doing*** lots of things at once. -- Rob Pike

## 为什么Go不允许循环引用
- 因为互相依赖是一种非常糟糕的设计，应该尽量保持单向依赖。
