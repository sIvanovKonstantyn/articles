# Bit manioulation

### Слово в число без учета последовательности символов
```java
    private int mask(String word) {
        int res = 0;
        for(char c: word.toCharArray()) {
            res += maskChar(c);//  c - 'a' give as a char int (n), one bit left give us an 2^n 
        }
        return res;
    }

    private int maskChar(char c) {
        return 1 << (c - 'a');
    }
```