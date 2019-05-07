## Links:
[Apache common](https://commons.apache.org/)


## Examples:

### Converter
```
package com.demos.apachecommonexamples;

import org.apache.commons.beanutils.converters.ArrayConverter;
import org.apache.commons.beanutils.converters.StringConverter;

import java.util.Arrays;

public class ArrayConverterExample {

    public static void main(String[] args) {

        /*
        * ArrayConverter Object --> String
        * */
        System.out.println("---ArrayConverter---");
        ArrayConverter arrayConverter = new ArrayConverter(String[].class, new StringConverter());
        String[] res = arrayConverter.convert(String[].class, new Object[]{123,"f2f23f23", new SomeClass()});
        System.out.println("Object --> String: " + Arrays.toString(res));
    }
}
```



### BagUtils
```
package com.demos.apachecommonexamples;

import org.apache.commons.collections.Bag;
import org.apache.commons.collections.BagUtils;
import org.apache.commons.collections.bag.HashBag;
import org.apache.commons.collections.functors.InstanceofPredicate;

public class BagUtilsExample {
    public static void main(String[] args) {

        System.out.println("---BagUtils--------------------------------------------------------------------------------------------------");
        System.out.println("Bag with elements: ");
        Bag bag = new HashBag();
        bag.add("Some_good_1");
        bag.add("Some_good_1", 10);
        System.out.println(bag);

        System.out.println("Bag with String only: ");
        final Bag predicatedBag = BagUtils.predicatedBag(bag, new InstanceofPredicate(String.class));
        System.out.println(predicatedBag);
    }
}
```



 
### BeanUtils
```
package com.demos.apachecommonexamples;

import org.apache.commons.beanutils.BeanUtils;

import java.lang.reflect.InvocationTargetException;
import java.util.Map;

public class BeanUtilsExample {

    public static void main(String[] args) throws IllegalAccessException, NoSuchMethodException, InvocationTargetException {

        System.out.println("---BeanUtils------------------------------------------------------------------------------------");

        System.out.println("--describe SomeClass: " + BeanUtils.describe(new SomeClass()));

        System.out.println();

        SomeAnotherClass someAnotherClass = new SomeAnotherClass();
        BeanUtils.copyProperties(someAnotherClass, new SomeClass());
        System.out.println("--copyProperties SomeClass --> SomeAnotherClass: " + BeanUtils.describe(someAnotherClass)); // Dest. object should have setters

        System.out.println();

        System.out.println("--map with optional synchronous reading/writing");
        final Map<String, Object> cache = BeanUtils.createCache(); // wrapped by WeakFastHashMap;
        BeanUtils.setCacheFast(cache, true);//false
        System.out.println("  Fast mode: " + BeanUtils.getCacheFast(cache));

        System.out.println();

        System.out.println(BeanUtils.getNestedProperty(new SomeClassWithInnerProperty(), "someInnerClassLevel1.someInnerClassLevel2.someInnerProperty")); // mb helpful. But I don't think so;
    }
}
```

### CollectionUtils
```
package com.demos.apachecommonexamples;

import org.apache.commons.collections.CollectionUtils;

import java.util.ArrayList;
import java.util.Collection;
import java.util.List;

public class CollectionUtilsExample {
    public static void main(String[] args) {

        System.out.println("---CollectionUtils-------------------------------------------------------------------------------------------");
        System.out.println("addIgnoreNull: ");
        List<String> list = new ArrayList<>();

        CollectionUtils.addIgnoreNull(list, "VALUE");
        CollectionUtils.addIgnoreNull(list, "aaa_VALUE");
        CollectionUtils.addIgnoreNull(list, null);
        System.out.println(list);
        System.out.println();

        System.out.println("cardinality: ");
        System.out.println(CollectionUtils.cardinality("VALUE", list));
        System.out.println();

        System.out.println("collect: "); // Could be helpful for migration data to new collection with some transform
        final List listAfterCollect = (List)CollectionUtils.collect(list, input -> "aaa_" +  input);
        System.out.println(listAfterCollect);
        System.out.println();

        System.out.println("intersection: ");
        final Collection listAfterIntersection = CollectionUtils.intersection(list, listAfterCollect);
        System.out.println(listAfterIntersection);
        System.out.println();

        System.out.println("select: ");
        final Collection listAfterSelection = CollectionUtils.select(list, object -> !((String)object).contains("aaa_"));
        System.out.println(listAfterSelection);
        System.out.println();


        System.out.println("selectRejected: ");
        final Collection listAfterSelectionRejected = CollectionUtils.selectRejected(list, object -> !((String)object).contains("aaa_"));
        System.out.println(listAfterSelectionRejected);
        System.out.println();

        System.out.println("union: ");
        final Collection unionList = CollectionUtils.union(list, listAfterCollect);
        System.out.println(unionList);
        System.out.println();

        System.out.println("filter: ");
        CollectionUtils.filter(unionList, object -> true); // Implementation as in stream API (for java 7-. certainly  without lambda)
        System.out.println(unionList);
        System.out.println();


        System.out.println("forAllDo: ");
        CollectionUtils.forAllDo(unionList, System.out::println); // Implementation as in stream API (for java 7-. certainly  without lambda)
        System.out.println();

        System.out.println("--------------------------------------------------------------------------------------------------------------");

    }
}
```

### CommonsCodec
```
package com.demos.apachecommonexamples;

import org.apache.commons.codec.DecoderException;
import org.apache.commons.codec.binary.Hex;
import org.apache.commons.codec.binary.StringUtils;

import java.util.Arrays;

public class CommonsCodecExample {
    public static void main(String[] args) throws DecoderException {

        System.out.println("---CommonsCodecExample----------------------------------------------------------------------------");
        System.out.println("-- hex encoding"); // Hex - it's just example. It's much more implementations in apache.commons.codec.binary
        final String initialString = "ABCDEFGH";
        System.out.println("initialString: " + initialString);
        final char[] encodedChars = Hex.encodeHex(initialString.getBytes());
        System.out.println("encodedChars: " + Arrays.toString(encodedChars));
        final byte[] decodeHex = Hex.decodeHex(encodedChars);
        System.out.println("decoded string: " + StringUtils.newStringUtf8(decodeHex)); // Also using binary.StringUtils;
        System.out.println("---------------------------------------------------------------------------------------------------");
    }
}
```


### DateConverter

```
package com.demos.apachecommonexamples;

import org.apache.commons.beanutils.converters.DateConverter;

import java.time.LocalDate;
import java.util.Date;
import java.util.Locale;

public class DateExample {

    public static void main(String[] args) {

        /*
        * DateConverter
        * */

        System.out.println("---DateConverter---");
        DateConverter dateConverter = new DateConverter();
        dateConverter.setPattern("yyyy+MM++dd");
        System.out.println("yyyy+MM++dd --> Date: "
                                    + dateConverter.convert(Date.class, "2080+12++21"));

        DateConverter dateConverterWithSqlDate = new DateConverter();
        System.out.println("util.Date --> sql.Date: "
                + dateConverterWithSqlDate.convert(java.sql.Date.class, new Date()));

        System.out.println("sql.Date --> : util.Date: "
                + dateConverterWithSqlDate.convert(null, new java.sql.Date(new Date().getTime()))); // Default type using


        DateConverter dateConverterWithLocalDate = new DateConverter();
        dateConverterWithLocalDate.setPattern("yyyy-MM-dd");
        System.out.println("LocalDate --> util.Date: "
                + dateConverterWithLocalDate.convert(null, LocalDate.now())); // Convert from local date by using string pattern

    }
}
```
