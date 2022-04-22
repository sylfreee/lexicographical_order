# About

A string generator that helps to implement real-time editing of ordered sequence.

It makes reordering, sorting, interleaving transactions faster and simpler. 



# Usage

- **Get a string between two strings in the lexicographical(lexical or alphabetical) order.**

  ```dart
  final mid = between(prev: 'B', next: 'D');
  assert(
    areEqual(
      [mid, 'D', 'B']..sort(),
      ['B', mid, 'D'],
    ),
  );
  ```

- **Generate order keys**.

  ```dart
  final keyCount = 100; 
  final orderKeys = generateOrderKeys(keyCount);
  ```
  This is useful for the following cases:
  
  1. `between` cannot be used because the table (collection) is empty.
  
     ```dart
     Future<void> addTodo(CreateTodo command) async {
       final String orderKey = todos.isEmpty 
         ? generateOrderKeys(1).first // <==
         : between(prev: todos.last.orderKey);
       
       final todo = await todoRepository.create(command, orderKey);
       todos.add(todo);
     }
     ```
  
  2. when migrating to an efficient ordered system.
  
     ```dart
     Future<void> migrateToLexicalOrderSystem(Table table) async {
       final itemCount = table.count();
       final orderKeys = generateOrderKeys(itemCount);
       /* omitted */
     }
     ```



# Caution

### **The `between` function accepts only allowed characters as arguments**

more precisely, the following code is used inside `between`.

```dart
LexOrderValidator().checkBetweenArgs(prev: prev, next: next);
```

The `LexOrderValidator` checks the following constraints:

 1. both `prev` and `next` must be composed of alphabets.

 2. `prev.isNotEmpty && next.isNotEmpty`

 3. `prev != null && next != null`

 4. `next != 'A'`

 5. `prev != next`

 6. `prev.compareTo(next) == -1`, for example:

    ```dart
    between(prev: 'C', next: 'B');
    ```


In debug mode, you get an error if you violate the above constraints. but not in release mode. you can check the constraints manually by using `LexOrderValidator`.









