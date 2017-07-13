# Reactive JavaScript (rxjs) Nota Bene List

## Observables
  * [Reference](https://medium.com/@benlesh/learning-observable-by-building-observable-d5da57405d87)
  * An observable is a function that accepts a object with `next`, `error` and `complete` method in it (observer)
  * Observables are inert. They’re just functions. They sit there until you `subscribe` to them, they set up your observer, and they’re done, back to being boring old functions waiting to be called. The observers on the other hand, stay active and listen for events from your producers.

## Hot (Multicast) vs Cold (Unicast)
  * Observable is hot when `producer` is created outside of the observable
    - Ex

          // HOT
          var producer = new Producer();
          var hot = new Observable((observer) => {
            // have observer listen to producer here
          });

  * Observable is cold when `producer` is created and activated during subscription
    - Ex 

          // COLD
          var cold = new Observable((observer) => {
            var producer = new Producer();
            // have observer listen to producer here
          });


## Subject
  * [Reference](https://github.com/ReactiveX/rxjs/blob/master/doc/subject.md)
  * A rxjs subject is an Observable that can be multicast to multiple listeners
  * Methods:
      1. next(v)
      2. error(e)
      3. complete()
        
              var subject = new Rx.Subject();

              subject.subscribe({
                next: (v) => console.log('observerA: ' + v)
              });
              subject.subscribe({
                next: (v) => console.log('observerB: ' + v)
              });

              subject.next(1);
              subject.next(2);

## Observables vs Promises
  * Promises are always multicast and its resolution and rejection is always async
  * Observables can be unicast or multicast, asyn or syn

## Observer Rules
  1. If you pass an Observer doesn’t have all of the methods implemented, that’s okay.
  2. You don’t want to call `next` after a `complete` or an `error`
  3. You don’t want anything called if you’ve unsubscribed.
  4. Calls to `complete` and `error` need to call unsubscription logic.
  5. If your `next`, `complete` or `error` handler throws an exception, you want to call your unsubscription logic so you don’t leak resources.
  6. `next`, `error` and `complete` are all actually optional. You don’t have to handle every value, or errors or completions. You might just want to handle one or two of those things.
