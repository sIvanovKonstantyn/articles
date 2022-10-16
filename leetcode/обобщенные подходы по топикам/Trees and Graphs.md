# Trees and Graphs
*Всевозможные задачи на поиски в графах и деревьях, манимуляции над ними (превратить в список, поменять местами ноды...) итд.*

### LCA (Lowest common ancestor)
*Поиск ближайшего общего предка для 2-х нод дерева. Принцип, когда мы ищем 2 таргет значения в детках узла и если в обоих ветках найшли - считаем текущую ноду ощим предком. Алгоритм можем использоваться для поиска пути между нодами в дереве.*
```java
  private TreeNode findLCAInBinaryTree(
  TreeNode node,
  int startValue,
  int destValue
  ) {
    if (node == null || node.val == startValue || node.val == destValue) {
      return node;
    }

    // explore left subtree for LCA
    TreeNode leftNode = findLCAInBinaryTree(node.left, startValue, destValue);
    // explore right subtree for LCA
    TreeNode rightNode = findLCAInBinaryTree(node.right, startValue, destValue);

    if (leftNode == null) return rightNode;
    if (rightNode == null) return leftNode;

    // if both are not null, we found the LCA node
    return node;
  }
```


### DFS
*Алгоритм поиска по дереве в глубину. Используется для поиска какого-то значения или для агрегации каких-то результатов*

#### Аналог с поиском, с расчетом "высоты" от крайних нод:
```java
private int removeLeaves(TreeNode root, List<List<Integer>> res) {

    if(root == null) {
        return -1;
    }

    int leftHight = removeLeaves(root.left, res);
    int rightHight = removeLeaves(root.right, res);
    int currHight = Math.max(leftHight, rightHight) + 1;

    if(res.size() == currHight) {
       res.add(new ArrayList<>());
    }
    res.get(currHight).add(root.val);

    return currHight;
}
```

#### Использование поиска в глубину, для формирования разнообразных вариаций состалвения слова:
```java
private int dfs(
      Set<String> words,
      Map<String, Integer> memo,
      String currentWord
      ) {
    // If the word is encountered previously we just return its value present in the map (memoization).
    if (memo.containsKey(currentWord)) {
        return memo.get(currentWord);
    }
    // This stores the maximum length of word sequence possible with the 'currentWord' as the
    int maxLength = 1;
    StringBuilder sb = new StringBuilder(currentWord);

    // creating all possible strings taking out one character at a time from the `currentWord`
    for (int i = 0; i < currentWord.length(); i++) {
        sb.deleteCharAt(i);
        String newWord = sb.toString();
        // If the new word formed is present in the list, we do a dfs search with this newWord.
        if (words.contains(newWord)) {
            int currentLength = 1 + dfs(words, memo, newWord);
            maxLength = Math.max(maxLength, currentLength);
        }
        sb.insert(i, currentWord.charAt(i));
    }
    memo.put(currentWord, maxLength);

    return maxLength;
}
```

#### Использование поиска в глубину, для поиска возможных для создания рецептов:
```java
    private boolean dfs(
       Map<String, List<String>> id,
       Set<String> available,
       String ing, Set<String>
       notPos
       ) {
        if(available.contains(ing))
            return true;

        if(notPos.contains(ing))
            return false;

        notPos.add(ing); //during the start of checking current recipe, assume current recipe can not be created, helps to check cyclic dependency

        for(String s: id.get(ing)) {
            if(available.contains(s))
                continue;

            if(notPos.contains(s))
            {
                notPos.add(ing);
                return false;
            }

            // if current requirement for current recipe is another recipe
            if(id.containsKey(s)) {
                if(!dfs(id, available, s, notPos)) {
                    notPos.add(ing);
                    return false;
                }
            } else if(!available.contains(s)) { // if current requirement is not a recipe, it should be an ingredient hence should be in available, but its not
                notPos.add(ing);
                return false;
            }
        }

        //current recipe can be created, remove from not possible and add to available
        notPos.remove(ing);
        available.add(ing);
        return true;
    }
```

### BFS
------------
------------

