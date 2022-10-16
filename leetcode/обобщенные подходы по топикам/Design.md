# Trees and Graphs
*Всевозможные задачи на поиски в графах и деревьях, манимуляции над ними (превратить в список, поменять местами ноды...) итд.

### LRU Cache
*Кеш, удалющий n-ый элемент с конца. По фактру это мапа + очередь, которая контролирует капасити*

#### Расширение LinhedHashMap
```java
class LRUCache extends LinkedHashMap<Integer, Integer> {

    private final int capacity;

    public LRUCache(int capacity) {
        super(capacity , 0.75F, true);
        this.capacity = capacity;
    }

    public int get(int key) {
        return super.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
      super.put(key, value);
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity;
    }
}
```
------------
------------

### Snapshot Array
*Реализация коллекции, которая может хранить в себе несколько версий. Можно просто хранить версии, как полные копии исходных массивов - тогда сложность по занимаемому месту будет NM (размер массива х кол-во версий). Если же хранить в новых версиях только новые данные, а в методе поиска брать данные из заказанной версии ИЛИ ее предшествующей - поиск немного усложнится по времени (log(n) вместо константы) но занимаемое место будет сильно меньше.*

```java
class SnapshotArray {

    int snap = 0;
    List<int[]>[] snapshots;


    public SnapshotArray(int length) {
        snapshots = new ArrayList[length];
        for(int i = 0; i < snapshots.length; i++) {
            snapshots[i] = new ArrayList<>();
            snapshots[i].add(new int[]{0,0});
        }
    }


    public void set(int index, int val) {
        int[] last = snapshots[index].get(snapshots[index].size()-1);

        if(last[0] == snap) {
           last[1] = val;
        } else {
            snapshots[index].add(new int[]{snap,val});
        }
    }

    public int snap() {
        return snap++;
    }

    public int get(int index, int snap_id) {
        int pos = Collections.binarySearch(snapshots[index], new int[]{snap_id,0}, (a,b) -> a[0] - b[0]);
        if(pos < 0) {
           pos = -pos - 2; // inserstion point of unexsiting value - 2 is the previous version element
        }
        return snapshots[index].get(pos)[1];
    }
}
```
------------
------------

### Min Stack
*Стек, который делает все то же, что и обычный + может вернуть минимальное значение за константное время. Идея в том, что бы дополнить хранимые данные
минимальным значением. Таким образом, мы в каждом айтеме хранием его значение + минимальное значение на момент его добавления.*

```java
class MinStack {

    private Stack<int[]> stack;

    public MinStack() {
        stack = new Stack<>();
    }

    public void push(int val) {
        if(stack.isEmpty()) {
            stack.push(new int[]{val, val});
            return;
        }

        int currentMin = stack.peek()[1];
        stack.push(new int[]{val, Math.min(currentMin, val)});
    }

    public void pop() {
        stack.pop()
    }

    public int top() {
        return stack.peek()[0];
    }

    public int getMin() {
        return stack.peek()[1];
    }
}
```
------------
------------
### Serialize and Deserialize Binary Tree
*Основной принцип - обойти как-то одинаково дерево и преобразоывать в строку
или распарсить из строки. Например используя DFS*

```java
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
       return serialize(root, "");
    }

    private String serialize(TreeNode node, String s) {
        if(node == null) {
            return s + "null,";
        }

        return s +
            String.valueOf(node.val)
            + ","
            + serialize(node.left, s)
            + serialize(node.right, s);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        String[] dataArr = data.split(",");
        List<String> dataList = new LinkedList(Arrays.asList(dataArr));
        return deserialize(dataList);
    }

    private TreeNode deserialize(List<String> data) {

        if(data.get(0).equals("null")) {
            data.remove(0);
            return null;
        }

        TreeNode node = new TreeNode(Integer.parseInt(data.get(0)));
        data.remove(0);

        node.left = deserialize(data);
        node.right = deserialize(data);

        return node;
    }
}
```
------------
------------
### Insert Delete GetRandom O(1)
*Задачи про комбинирования нескольких коллекций. Например - Map для отслеживания индексов, что позволи искать за константное время по значению индексы к данным. А уже по ним - данные их коллекции, которая поддерживает поиск по инжексу за константу (ArrayList, например)*
```java
class RandomizedSet {

    private Map<Integer, Integer> indexes = new HashMap<>();
    private List<Integer> data = new ArrayList<>();
    private Random random = new Random();

    public RandomizedSet() {

    }

    public boolean insert(int val) {

        if(indexes.containsKey(val)) {
            return false;
        }

        data.add(val);
        indexes.put(val, data.size()-1);
        return true;
    }

    public boolean remove(int val) {
        if(!indexes.containsKey(val)) {
            return false;
        }

        int lastValue = data.get(data.size()-1);

        data.set(indexes.get(val), lastValue);
        indexes.put(lastValue, indexes.get(val));

        data.remove(data.size()-1);
        indexes.remove(val);

        return true;
    }

    public int getRandom() {
        return data.get(random.nextInt(data.size()));
    }
}
```

------------
------------
### Stock Price Fluctuation
*Еще одна задача на кобминирование коллекций. Хешмапа, для получения и обновления текущих значений и TreeMap для получения макс/мин значения*

```java
class StockPrice {
    int latestTime;
    // Store price of each stock at each timestamp.
    Map<Integer, Integer> timestampPriceMap;
    // Store stock prices in increasing order to get min and max price.
    TreeMap<Integer, Integer> priceFrequency;

    public StockPrice() {
        latestTime = 0;
        timestampPriceMap = new HashMap<>();
        priceFrequency = new TreeMap<>();
    }

    public void update(int timestamp, int price) {
        // Update latestTime to latest timestamp.
        latestTime = Math.max(latestTime, timestamp);

        // If same timestamp occurs again, previous price was wrong. 
        if (timestampPriceMap.containsKey(timestamp)) {
            // Remove previous price.
            int oldPrice = timestampPriceMap.get(timestamp);
            priceFrequency.put(oldPrice, priceFrequency.get(oldPrice) - 1);

            // Remove the entry from the map.
            if (priceFrequency.get(oldPrice) == 0) {
                priceFrequency.remove(oldPrice);
            }
        }

        // Add latest price for timestamp.
        timestampPriceMap.put(timestamp, price);
        priceFrequency.put(price, priceFrequency.getOrDefault(price, 0) + 1);
    }

    public int current() {
        // Return latest price of the stock.
        return timestampPriceMap.get(latestTime);
    }

    public int maximum() {
        // Return the maximum price stored at the end of sorted-map.
        return priceFrequency.lastKey();
    }

    public int minimum() {
        // Return the maximum price stored at the front of sorted-map.
        return priceFrequency.firstKey();
    }
}
```

------------
------------
### Design Search Autocomplete System
*Задачи про всевозможные поиски по префиксам или аводополнения, как в примере ниже. Основной принцип - использовать Trie*
```java
class AutocompleteSystem {

    private TrieNode root = new TrieNode();
    private String prefix = "";

    public AutocompleteSystem(String[] sentences, int[] times) {
        for(int i = 0; i < sentences.length; i++) {
            add(sentences[i], times[i]);
        }
    }

    private void add(String sentence, int count) {
        TrieNode curr = root;
        for(char c : sentence.toCharArray()) {
            TrieNode next = curr.children.get(c);
            if(next == null) {
                next  = new TrieNode();
                curr.children.put(c , next);
            }

            curr = next;
            curr.counts.put(sentence, curr.counts.getOrDefault(sentence, 0) + count);

        }
    }

    public List<String> input(char c) {

        //Cpecial character. Add prefix to our trie. Clean prefix
        if(c == '#') {
            add(prefix, 1);
            prefix = "";
            return new ArrayList<>();
        }

        prefix += c;

        TrieNode curr = root;

        for(char cc : prefix.toCharArray()) {
            TrieNode next = curr.children.get(cc);
             //We don't find prefix in our sentenses - return empty result
            if(next == null) {
                return new ArrayList<>();
            }

            curr = next;
        }

        return curr.counts.entrySet().stream()
            .sorted(
                (e1, e2) ->
                        e2.getValue() == e1.getValue()
                            ? e1.getKey().compareTo(e2.getKey())
                            : e2.getValue() - e1.getValue()
             )
            .limit(3)
            .map(e -> e.getKey())
            .collect(Collectors.toList());
    }

    class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        Map<String, Integer> counts = new HashMap<>();
    }
}
```