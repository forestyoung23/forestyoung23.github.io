# json反序列化:can only instantiate non-static inner class by using default, no-argument constructor

### 示例代码

``` java
@Data
public class Car {
    private String type;
    private Size size;
    @Data
    public class Size {
        private Integer length;
        private Integer width;
        private List<Nmd> nmd;
        @Data
        public class Nmd {
            private String name;
        }
    }
}

public class InnerClassTest {
    public static void main(String[] args) throws IOException {
         String json = "{\n" +
                "    \"type\": \"bba\",\n" +
                "    \"size\": {\n" +
                "        \"length\": 5,\n" +
                "        \"width\": 3,\n" +
                "        \"nmd\": [\n" +
                "            {\n" +
                "                \"name\": \"123\"\n" +
                "            }\n" +
                "        ]\n" +
                "    }\n" +
                "}";
        ObjectMapper objectMapper = new ObjectMapper();
        Car car = objectMapper.readValue(json, Car.class);
    }
```
运行main方法控制台报错

``` text
Exception in thread "main" com.fasterxml.jackson.databind.exc.MismatchedInputException: Cannot construct instance of `com.forest.concurrency.Car$Size$Nmd` (although at least one Creator exists): can only instantiate non-static inner class by using default, no-argument constructor
 at [Source: (String)"{
    "type": "bba",
    "size": {
        "length": 5,
        "width": 3,
        "nmd": [
            {
                "name": "123"
            }
        ]
    }
}"; line: 8, column: 17] (through reference chain: com.forest.concurrency.Car["size"]->com.forest.concurrency.Car$Size["nmd"]->java.util.ArrayList[0])
```

### 问题处理

参考博客：https://blog.csdn.net/zhangxiaoyang0/article/details/86022122
问题本质为：内部非静态类无法实例化
按照博主的解决方法