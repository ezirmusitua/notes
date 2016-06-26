## Some Lodash API

### Array

1.  chunk(array, [size=1])

    ```javascript
    _.chunk(['a', 'b', 'c', 'd'], 2);
    // → [['a', 'b'], ['c', 'd']]
    _.chunk(['a', 'b', 'c', 'd'], 3);
    // → [['a', 'b', 'c'], ['d']]
    ```

2.  compact(array)

    移除所有 falsey 的值

3.  concat(array, [values])

    ```javascript
    var array = [1];
    var other = _.concat(array, 2, [3], [[4]]);
    console.log(other);
    // → [1, 2, 3, [4]]
    console.log(array);
    // → [1]
    ```

4.  difference(array, [values])

    ```javascript
    _.difference([3, 2, 1], [4, 2]);
    // → [3, 1]
    ```

5.  drop(array, [n=1])

    ```javascript
    _.drop([1, 2, 3]);
    // → [2, 3]
    _.drop([1, 2, 3], 2);
    // → [3]
    _.drop([1, 2, 3], 5);
    // → []
    _.drop([1, 2, 3], 0);
    // → [1, 2, 3]
    ```

6.  dropWhile(array, [predicate=_.identity)

    ```javascript
    var users = [
      { 'user': 'barney',  'active': false },
      { 'user': 'fred',    'active': false },
      { 'user': 'pebbles', 'active': true }
    ];
    _.dropWhile(users, function(o) { return !o.active; });
    // → objects for ['pebbles']
    // The `_.matches` iteratee shorthand.
    _.dropWhile(users, { 'user': 'barney', 'active': false });
    // → objects for ['fred', 'pebbles']
    // The `_.matchesProperty` iteratee shorthand.
    _.dropWhile(users, ['active', false]);
    // → objects for ['pebbles']
    // The `_.property` iteratee shorthand.
    _.dropWhile(users, 'active');
    // → objects for ['barney', 'fred', 'pebbles']
    ```

7.  fill(array, value, [start=0], [end=array.length])

    ```javascript
    var array = [1, 2, 3];

    _.fill(array, 'a');
    console.log(array);
    // → ['a', 'a', 'a']

    _.fill(Array(3), 2);
    // → [2, 2, 2]

    _.fill([4, 6, 8, 10], '*', 1, 3);
    // → [4, '*', '*', 10]
    ```

8.  findIndex(array, [predicate=_.identity)

    ```javascript
    var users = [
      { 'user': 'barney',  'active': false },
      { 'user': 'fred',    'active': false },
      { 'user': 'pebbles', 'active': true }
    ];
    _.findIndex(users, function(o) { return o.user == 'barney'; });
    // → 0
    // The `_.matches` iteratee shorthand.
    _.findIndex(users, { 'user': 'fred', 'active': false });
    // → 1
    // The `_.matchesProperty` iteratee shorthand.
    _.findIndex(users, ['active', false]);
    // → 0
    // The `_.property` iteratee shorthand.
    _.findIndex(users, 'active');
    // → 2
    ```

9.  flatten<Deep><Depth>(array, <[depth=1]>)

    ```javascript
    _.flatten([1, [2, [3, [4]], 5]]);
    // → [1, 2, [3, [4]], 5]
    _.flattenDeep([1, [2, [3, [4]], 5]]);
    // → [1, 2, 3, 4, 5]
    var array = [1, [2, [3, [4]], 5]];
    _.flattenDepth(array, 1);
    // → [1, 2, [3, [4]], 5]
    _.flattenDepth(array, 2);
    // → [1, 2, 3, [4], 5]
    ```

10.  fromPairs(pairs)

     ```javascript
     _.fromPairs([['fred', 30], ['barney', 40]]);
     // → { 'fred': 30, 'barney': 40 }
     ```

11.  ​