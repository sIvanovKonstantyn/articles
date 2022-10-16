# Arrays and Strings
*Разнообразные задачи на работу со строками и массивами/коллекциями.*

## Common

### Часто используемые функции jdk

### Округление
```java
//round up
Math.ceil(myDouble);

//round down
Math.floor(myDouble);
```

------------
------------

## Arrays & Collections

### Дополнение с 'i' данными части другого массива
```java
System.arraycopy(res, i + 1, digits, i, digits.length - i);
```

### Копия массива по 'i'
```java
res = Arrays.copyOf(result, i);
```

### Отсортировать массив/коллекцию
*Подход, когда мы облегчаем себе дальнейшее решение тем, что сортируем
вх. массив/коллекцию*

#### Отсортировать массив по возрастанию элементов:
```java
Arrays.sort(arr);
```
#### Отсортировать массив с кастомной функцией компарации:
```java
//asc
Arrays.sort(arr, (a,b) -> a - b);
//desc
Arrays.sort(arr, (a,b) -> b - a);

//sort by a function based on multiple fields
Arrays.sort(points, (a,b) -> Double.compare(
    euclideanDistanceToOrigin(a[0], a[1]),
    euclideanDistanceToOrigin(b[0], b[1])
));
```

#### Отсортировать массив по нескольким критерями (уменьшение одного и увеличение другого)
```java
Arrays.sort(properties, (a,b) -> {
    if(a[0] == b[0] ) {
        return b[1] - a[1];
    } else {
        return a[0] - b[0];
    }
});
```

#### Аналоги сортировки для коллекции
```java
//default
sortedList = Collections.sort(list);
//as
sortedList = Collections.sort(list, (a,b) -> a - b);

//sort by a function based on multiple fields
sortedMap = Collections.sort(map, (a,b) -> Double.compare(
    euclideanDistanceToOrigin(a.getKey(), a.getValue()),
    euclideanDistanceToOrigin(b.getKey(), b.getValue())
));
```

### Обход массива 2-мя указателями:
*Подход может использоваться для реверса массива или для задач,
завязаных на расчете растояния между индексами (как в примере про
глубину басейна ниже)*

```java
int max = 0;
int i = 0, j = height.length - 1;
while (i < j) {

    max = Math.max(max, Math.min(height[i], height[j]) * (j - i));

    if (height[i] < height[j]) {
        i++;
    } else {
        j--;
    }
}
```

### Sliding window - обход коллекции "окнами":
*Подход, позволяющий собрать нужную ифнормацию из массива, если она находится на разных индексах и зависит друг от друга за один проход (точнее за N сложность без циклов в циклах). Некое развитие 2-х указателей.
2 указателя у нас стартуют на одном индексе, сначала, по нужным условиям увеличивается один (например, пока не найдем все нужные символы), далее
начинает увеличиваться второй показатель до тех пор, пока условия соблюдаются
и после этого можно, например, получить длину диапазона который удовлетворяет условиям.
На примере задачи, где нужно расчитать длину какой-то последовательности строк по условию (например содержат все символы из другой строки).
*

```java
int i = 0;
int j = 0;
int counter = t.length();

while(j < s.length()) {

    // We take chars from the second index and decrease counter
    // untill we have char in a target string
    // thus we will stop when there is no string[j] in a target string
    char currentChar = s.charAt(j);
    if(targetChars.getOrDefault(currentChar, 0) > 0) {
        counter--;
    }

    targetChars.put(currentChar, targetChars.getOrDefault(currentChar, 0) - 1);
    j++;


    // We take chars from the first index and increase counter
    // untill we DONT have char in a target string
    // thus we will stop when it string[i] in a target string
    // if our current difference between second and start indexes is less than minLen -
    // we change min len by this diff and min start by the first index
    while(counter == 0) {
        if(minLen > j - i) {
            minLen = j - i;
            minStart = i;
        }

        char currentStartChar = s.charAt(i);
        targetChars.put(currentStartChar, targetChars.getOrDefault(currentStartChar, 0) + 1);
        if(targetChars.getOrDefault(currentStartChar, 0) > 0) {
            counter++;
        }
        i++;
    }
}
```

### Использование приоритетной очередли для работы с коллекциями
*Например есть задача смерджить коллекции и сохранить порядок, или временно хранить n элементов коллекции в сортированном виде*

#### Создание ноды, с восхожящей сортировкой по одноиу из полей:
```java
PriorityQueue<ListNode> queue = new PriorityQueue<ListNode>((a,b) -> a.val - b.val);
```
#### Создание очереди, с восхожящей сортировкой по одноиу из полей:
```java
PriorityQueue<ListNode> queue = new PriorityQueue<ListNode>((a,b) -> a.val - b.val);
```

#### TreeMap для получения больших/меньших значений:
```java
TreeMap<Integer, Integer> nextCells = new TreeMap<>();
//take the next greater key
Integer nextGreaterKey = nextCells.ceilingKey(arr[i]);

//take the next smaller key
Integer nextSmallerKey = nextCells.floorKey(arr[i]);
```

#### Добавление в очередь:
```java
queue.add(node);
```

#### получение и удаление первого элемента из очереди:
```java
value = queue.poll();
```
#### получение БЕЗ удаления первого элемента из очереди:
```java
value = queue.peek();
```

### Заполнение мапы счетчиком:
```java
Map<Character, Integer> targetChars = new HashMap<>();

for(Character c : t.toCharArray()) {
    targetChars.put(c, targetChars.getOrDefault(c, 0) + 1);    
}
```

### Минимальное значение из коллекции:
```java
Collections.min(diffChars.values());
```

------------
------------


## Strings

### Преобразование символов строк в интеджеры:
*Подход полезен для задач, где не разрешается использовать jdk
для такого преобразования или где нужна проверка определенного скоупа символов (например англ. алфавит или цифры)*

#### Каст строки в число:
```java
int res;
for(int i= 0; i < numStr1.length(); i++){
    res = res * 10 + (numStr1.charAt(i)-'0');
}
```

#### Умножение "в столбик" 2-х строк:
```java
for(int i=numStr1.length() - 1; i >= 0; i--){
    for(int j=numStr2.length() - 1 ; j >= 0; j--){
        int currentRes = (numStr1.charAt(i)-'0') * (numStr2.charAt(j)-'0');

        dp[high][i+j + 1] += currentRes%10;
        dp[high][i+j] += currentRes/10;
    }
    high++;
}
```

#### Подсчет всех букв англ. алфавита в строке:
```java
int[] res = new int[26];
for(int i= 0; i < text.length(); i++){
    res[text.charAt(i)-'a']++;
}
```

### Contains строки на определенном индексе:
```java
s.substring(i, sources[indice].length() + i).equals(sources[indice])
```

### Использования стеков для решеня задач со строками
*Очереди и стеки часто используются для проверки валидности каких-то выражений. Например открытия/закрытия скобок. Используется их способность возвращать первый/последний вошедший объект.*

#### Добавление в стек:
```java
Deque<Character> deque = new ArrayDeque<>();
deque.push(с);

Stack<Character> stack = new Stack<>();
stack.push(с);
```

#### Получение последнего айтема из стека:
```java
Deque<Character> deque = new ArrayDeque<>();
deque.pop();

Stack<Character> stack = new Stack<>();
stack.pop();
```

#### Пример на валидации скобок:
```java
Map<Character, Character> mapping = new HashMap<>();
mapping.put('(', ')');
mapping.put('{', '}');
mapping.put('[', ']');

Deque<Character> parentheses = new ArrayDeque<>();
for (Character c: s.toCharArray()) {

    //it's an open char - push into a queue
    if (mapping.containsKey(c)) {
        parentheses.push(c);
        continue;
    }
    //it's a close char and we don't have anything to close
    if (parentheses.isEmpty()) {
        return false;
    }

    //it's a close char and the last char in queue is not an open char
    //(looks like not reachable case)
    Character target = parentheses.pop();
    if(!mapping.containsKey(target)) {
        return false;
    }
    //it's a close char and the last open char has different type
    if (!mapping.get(target).equals(c)) {
        return false;
    }
}

return parentheses.isEmpty();
```