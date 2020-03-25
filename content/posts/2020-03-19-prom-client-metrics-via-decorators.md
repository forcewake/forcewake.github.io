---
categories:
- code
comments: true
date: "2020-03-24T00:00:00Z"
description: How to measure of typescript method execution time using decorators and publish it to Prometheus.
tags:
- english
- typescript
- prometheus
- decorators
- nodejs
title: 'How to measure of typescript method execution time using decorators and publish it to Prometheus?'
---

TL;DR `perf_hooks` + wrapper which will redefine the original method to new method who will call the original method + publish information to Prometheus.

Define a `Histogram` from `prom-client` library:

{{< highlight typescript >}}
import { Histogram } from 'prom-client'

const requestDuration = new Histogram({
  name: 'request_duration_ms',
  help: 'Duration of requests',
  labelNames: ['method'],
  buckets: [
    0.1,
    50,
    100,
    200,
    300,
    400,
    500,
    1000
  ] // buckets for response time from 0.1ms to 1000ms
})
{{< / highlight >}}

The decorator for non-async methods will be simple - redefine the method to this new method who will call the original method. Make sure that you use the function's this context instead of the value of this when it is called (no arrow function)

{{< highlight typescript >}}
export function calculateRequestDuration(operation: string) {
  return (target: any, propertyKey: string, descriptor: PropertyDescriptor) => {
    const originalMethod = descriptor.value
    
    descriptor.value = function(...args: any[]) {
      const startTime = performance.now()
      const result = originalMethod.apply(this, args)
      const endTime = performance.now()
      const timespan = endTime - startTime

      tpRequestDuration.observe({ operation }, timespan)

      return result
    }

    return descriptor
  }
}
{{< / highlight >}}

For async methods please make sure that you marked your wrapper function as `async` and called the original method with `await`:

{{< highlight typescript >}}
export function calculateAsyncRequestDuration(operation: string) {
  return (target: any, propertyKey: string, descriptor: PropertyDescriptor) => {
    const originalMethod = descriptor.value
    
    descriptor.value = async function(...args: any[]) {
      const startTime = performance.now()
      const result = await originalMethod.apply(this, args)
      const endTime = performance.now()
      const timespan = endTime - startTime

      tpRequestDuration.observe({ operation }, timespan)

      return result
    }

    return descriptor
  }
}
{{< / highlight >}}

The usage is simple:

{{< highlight typescript >}}
class SomeCoolClass {
  @calculateAsyncRequestDuration('promiseString')
  public async promiseString(): Promise<string> {
    return Promise.resolve('Some async method')
  }
  
  @calculateRequestDuration('simpleString')
  public simpleString(): string {
    return 'Some non-async method'
  }
}
{{< / highlight >}}

In order to test this code, please:
1. Spy on `observe` methods of `Histogram` class
2. Call the method
3. Check that `observe` was called once and it was called with correct params

{{< highlight typescript >}}
describe('test', () => {
  let sandbox

  afterEach(() => {
    mock.stopAll()
    sandbox.restore()
  })

  beforeEach(() => {
    sandbox = createSandbox()
  })

  describe('calculateAsyncRequestDuration', () => {
    it('should calculate duration for non-async methods', () => {
      const service = new SomeService()

      const observeSpy = sandbox.spy(Histogram.prototype, 'observe')

      service.simpleString()

      sinonAssert.calledOnce(observeSpy)
      sinonAssert.calledWith(
        observeSpy,
        { operation: 'simpleString' },
        match.number
      )
    })

    it('should calculate duration for non-async methods', async () => {
      const service = new SomeService()

      const observeSpy = sandbox.spy(Histogram.prototype, 'observe')

      service.promiseString()

      sinonAssert.calledOnce(observeSpy)
      sinonAssert.calledWith(
        observeSpy,
        { operation: 'promiseString' },
        match.number
      )
    })
  })
})
{{< / highlight >}}
