### Kotlin Vocabulary: Collections and Sequences

#### Introductions

Working with collections is a common task, and the kotlin standard library offers many great utility functions. It also offers two ways of working with containers based on how they evaluated: 

1,) eagerly with collections
2,) lazily with sequences

In this video, you will find out what's the differences between the two, which one you should use and when, and what are the performance implications of each of them. 


#### Differences

##### Collections

The difference between eager and lazy evaluation lies in when each transformation on the collection is performed. Let's say that we have a list of objects of different shapes. We want to make the shapes yellow and take the first square shape. 

```kotlin
	listOf(Circle, Square, Diamond, Triangle)
		.map { it.copy(color = yellow) }
		.first { it.shape == square}
```

1,) The first function we call is `map`. 

A new array list is created. We iterate through all items of the initial collection, transform it by copying the original object and changing its colour. Then add it to the new list.

2,) `first` is called.

We iterate through each item until the first square is found.

Collections are eagerly evaluated. Each operation is performed when it's called on the entire collection. The result of the operation is stored in a new collection. The transformations on collections are implemented using inline functions. 

```kotlin
//_Collections.kt
public inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {
	return mapTo(ArrayList<R>(collectionSizeOrDefault(10)), transform)
}

```

##### Sequences

Now, let's see what happens with sequences. 

```kotlin
listOf(Circle, Square, Diamond, Triangle).asSequence()
	.map { it.copy(color = yellow) }
	.first { it.shape == square }
```

1,) First method we need to call is `asSequence()`.

A sequence is created based on the iterator of the original collection. 

2,) `map` is called.

`map` is an intermediate operation. Now, the transformation is added to the list of operations needed to be performed by the sequence, but the operation is not been performed.

3,) `first` is called.

It's a terminal operation, so all intermediate operations are triggered on each element of the collection. We iterate through the initial collection, applying  `map` and then `first` on each of them. Since the condition is satisfied by the second element of the sequence, then we no longer need to apply `map` on the rest of the other elements. When working with sequences, no intermediate collection is created, and since items are evaluated one by one, `map` is only performed on some of the inputs.

Let's see the implementation of `map`:

```kotlin
// _Sequences.kt
public fun <T, R> Sequence<T>.map(transform: (T) -> R): Sequence<R> {
	return TransformingSequence(this, transform)
}

```

This isn't an inline function because the transformation function is passed to a `TransformingSequence` object, which stores it. Looking further down in the implementation of `TransformingSequence`, we will see that when `next()` is called on the sequence iterator, the transformation stored is also applied. 

```kotlin
override fun iterator(): Iterator<R> = object : Iterator<R> {
	val iterator = sequence.iterator()
	override fun next(): R {
		return transformer(iterator.next())
	}
}

```


#### Performance
Independent of whether you're using collections or sequences, the Kotlin Standard Library offers quite a wide range of operations for both, like `find`, `filter`, `groupBy`, and others. Make sure you check them out before implementing your own version of these. 


Let's talk about performance a little bit. Independent of whether you're using collections or sequences, the order of transformation matters. So in our example, actually at first it doesn't need to happen after `map` since it's not the consequence of map transformation. If we reverse the order of our business logic and call `first` on the collection, and then transform the result, then we only create one new object -- the yellow square. When using sequences, we avoid creating two new objects. When using sequences, we avoid creating an entire new list. Because terminal operations can finish processing early and intermediate operations are evaluated lazily, sequences can, in some cases, help yo avoid doing unnecessary work compared to collections. So make sure you always check the order of the transformations and the dependencies between them. 


#### Summary
Collection operations use inline functions. So the bytecode of the operation together with the bytecode of the lambda passed to it will be inline. Sequences don't use inline functions. Therefore, new function objects are created for each operation. 

On the other hand, collections create a new list for every transformation, while sequences just keep a reference to the transformation functions. When working with small collections with one or two operators, these differences don't have big implications. So working with collections should be ok. But when working with large lists, the intermediate collection equation can become expensive. In such cases, use sequences.

Collections eagerly evaluate your data, while sequences do so lazily. Depending the size of your data, pick the one that fits best -- collections for small lists or sequences for larger ones. And pay attention to the order of the transformations.