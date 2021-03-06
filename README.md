# collection-deep-merge [![Build Status](https://travis-ci.org/a-x-/collection-deep-merge.svg?branch=master)](https://travis-ci.org/a-x-/collection-deep-merge)

Genereal purpose arrays of objects by key merger.

Ready for browsers (`const`, `let` compatible) and node.js v4+.
IE11+, FF36+, Safari5.1+, Android

Works well as redux store helper.

Extensible via custom merger and comparator (Dependency Injection factory). Look more below.

It does not mutate arguments, it returns a new collection (Immutability).

## usage

> Open [test.js](https://github.com/a-x-/collection-deep-merge/blob/master/test.js) with examples

```js
const mergeById = mergeCollectionsBy('id')

//
// Deep merge object-items
const list1 = [ { a: 1, b: { c: 1 } }, { a: 2, b: { d: 2 } } ];
const list2 = [ { a: 1, b: { c: 3 } }, { a: 2, b: { c: 4 } } ];
const merged = [ { a: 1, b: { c: 3 } }, { a: 2, b: { c: 4, d: 2 } } ];

mergeById(list, [ item ]) ==== merged // deep equal

//
// Merge item with a collection
const list = [ { a: 1, b: 2 }, { a: 2, d: 4 } ];
const item = { a: 1, c: 3 };
const merged = [{ a: 1, b: 2, c: 3 }, { a: 2, d: 4 }]

mergeById(list, [ item ]) ==== merged // deep equal
```

## tips & tricks

### Custom `item[key]`s comparators and `item`s mergers
```js
const mergeCollectionsBy = require('collection-deep-merge');
const is = (a, b) => a !== undefined && a === b;

// You can merge in a shallow manner
// or implement very custom strategy
const merge = (a, b) => {/* custom merger */};

const mergeDeepById = mergeCollectionsBy('id', { is, merge });
```

### Shallow merge
```js
import mergeCollectionsBy, { mergeShallow } = 'collection-deep-merge';
const mergeShallowById = mergeCollectionsBy('id', { merge: mergeShallow });
```

### Nested collections
You can implement custom merger based on
`const { mergeDeep } = require('collection-deep-merge')`
with an especial behaviour on nested collections.

You should support followed api:

- merger is immutable
- merger invokes with `(Object a, Object b)` args
- merger returns `Object c`

## Alternatives
### [array-join](https://github.com/iredchuk/array-join)
collection-deep-merge like its `fullJoin` with deep item-objects merge

### Map from ES6
New native Map has all you need:
- preserve order
- O(1) access
- iterable

You can implement tiny union function (w/o deep items merging):
```js
function union(map1, map2) {
  return new Map([...map1, ...map2]);
}
```

or you can use the collection-deep-merge if you want to merge items deeply
`O(n*m) + O(?)` complexity (`n*m` not so good; `?` stands for deep merge).
```js
import mergeArrays from 'collection-deep-merge';
const mergeById = mergeArrays('id', { eq: (item1, item2) => {} });
const mergeMapsById = (map1, map2) => {
  mergeById([...map1], [...map2])
};
```

or ... // todo: create `map-deep-union` library special for Maps with `O(n)+O(?)` complexity.
