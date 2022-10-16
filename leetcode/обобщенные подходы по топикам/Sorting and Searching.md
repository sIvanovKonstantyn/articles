# Sorting and Searching
*Всевозможные задачи на поиски в строках и коллекциях или их сортировки. Есть много общих концептов с "**Arrays and Strings**"*

### BinarySearch
*Бинарный поиск очень широко используется в задачах про сортированные коллекции
или последовательности строк. Он дает нам log(N) что эффективнее, чем линейная
сложность*

#### Мануальная реализация для массива:
```java
private int binarySearch(int[] nums, int min, int max, int target) {

    if (nums[min] == target) {
        return min;
    }

    if (nums[max] == target) {
        return max;
    }

    if (max - min <= 1) {
        return -1;
    }

    int mid = (min + max) / 2;

    if (nums[mid] == target) {
        return mid;
    }

    if (nums[mid] < target) {
        return binarySearch(nums, mid, max, target);
    }

    if (nums[mid] > target) {
        return binarySearch(nums, min, mid, target);
    }

    return -1;
}
```

#### Бинартный поиск для коллекции (дополнительно дает еще смещение до ближайшего найденного, если не нашел таргет):
```java
int index = Collections.binarySearch(list, "value", (s1, s2) -> a.compareTo(b));
```

#### Адаптация бинартный поиска, когда есть дубликаты значений и нам нужно найти самый первый из них (слева-направо):
```java
void bsLeftmost(
       List<Integer> list,
       int target,
       AtomicInteger indexHolder,
       int left,
       int right
       ) {

    if (left > right) return;

    int mid = (left + right) / 2;

    int currentNum = list.get(mid);

    if (currentNum == target) {
        //found tarhet - try to find it on the left side again
        indexHolder.set(mid);
        bsLeftmost(list, target, indexHolder, left, mid - 1);

    } else if (currentNum < target) {

        bsLeftmost(list, target, indexHolder, mid + 1, right);

    } else if (num_now > num_target) {

        bsLeftmost(list, target, indexHolder, left, mid - 1);
    }

    return;
}
```

#### Адаптация бинартный поиска для поиска индекса максимального значения:
```java
int bsMax(int[] arr, int min, int max) {
    if(min >= max) {
        return min;
    }

    int mid = (min + max) / 2;

    if(arr[mid] < arr[mid + 1]) {
        return bsMax(arr, mid + 1, max);
    } else {
        return bsMax(arr, min, mid);
    }
}
```

#### Некий аналог бинарного поиска, но с поиском по нескольким коллекциям:
```java

    public double findMedianSortedArrays(int[] nums1, int[] nums2) {

        //lets define medians index for combined arr:
        int leftMedian = (nums1.length + nums2.length + 1) / 2;
        int rightMedian = (nums1.length + nums2.length + 2) / 2;

        return (findMedian(nums1, 0, nums2, 0, leftMedian) + findMedian(nums1, 0, nums2, 0, rightMedian)) / 2.0;
    }

    private double findMedian(
       int[] firstArr,
       int firstStart,
       int[] secondArr,
       int secondStart,
       int medianIndent
    ) {

        //We have cross the first array. So the median will be in the second one
        if(firstStart >= firstArr.length) {
            return secondArr[secondStart + medianIndent - 1];
        }

         //We have cross the second array. So the median will be in the first one
        if(secondStart >= secondArr.length) {
            return firstArr[firstStart + medianIndent - 1];
        }

        //We already found the median thus our indent is only 1. The minium value will be the first one
        if(medianIndent == 1) {
            return Math.min(firstArr[firstStart], secondArr[secondStart]);
        }

        int midValue1 = Integer.MAX_VALUE;
        int midValue2 = Integer.MAX_VALUE;
        int nextFirstStart = firstStart + medianIndent/2;
        int nextSecondStart = secondStart + medianIndent/2;
        int descreasedIndent = medianIndent - medianIndent/2;

        if(nextFirstStart - 1 < firstArr.length) {
            midValue1 = firstArr[nextFirstStart - 1];
        }

        if(nextSecondStart - 1 < secondArr.length) {
            midValue2 = secondArr[nextSecondStart - 1];
        }

        // we search in a less part
        if(midValue1 < midValue2) {
            return findMedian(firstArr, nextFirstStart, secondArr, secondStart, descreasedIndent);
        } else {
            return findMedian(firstArr, firstStart, secondArr, nextSecondStart, descreasedIndent);
        }
    }
```
------------
------------
