---
title: Java数字金额大写转换
date: 2019-03-17 22:41:51
tags: 工具类
---

![](http://oss.forestyoung.top/Java数字金额.jpg)
<!--more-->

### 背景

在工作中遇到结算单报表打印的需求，需要展示中文大写金额，为赶时间首先当然是google了(真实情况是觉得自己水平不够，写出来的util会有问题)

### 最初（来自CSDN，看到的也是转载贴，未注明出处，侵删）

其实这种工具类网上有很多，但个人感觉这个逻辑更加清晰，所以后面都是在这版的基础是进行修改

```java
public class ConvertNumberToUpper {
    /**
     * 数字金额大写转换，思想先写个完整的然后将如零拾替换成零 要用到正则表达式
     */
    public static String digitUppercase(double n) {
        String fraction[] = {"角", "分"};
        String digit[] = {"零", "壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖"};
        String unit[][] = {{"元", "万", "亿"}, {"", "拾", "佰", "仟"}};
        String head = n < 0 ? "负" : "";
        // 如果是负数取绝对值
        n = Math.abs(n);
        // 小数部分
        String s = "";
        for (int i = 0; i < fraction.length; i++) {
            // Math.floor(a)返回大于或等于参数的最小（最接近负无穷大） double值，并等于数学整数，Math.pow(10, i)10的i次幂
            s += (digit[(int)(Math.floor(n * Math.pow(10, i + 1)) % 10)] + fraction[i]).replaceAll("(零.)+", "");
        }
        if (s.length() < 1) {
            s = "整";
        }
        int integerPart = (int)Math.floor(n);
        // 整数部分
        for (int i = 0; i < unit[0].length && integerPart > 0; i++) {
            String p = "";
            for (int j = 0; j < unit[1].length && n > 0; j++) {
                p = digit[integerPart % 10] + unit[1][j] + p;
                integerPart = integerPart / 10;
            }
            s = p.replaceAll("(零.)*零$", "").replaceAll("^$", "零") + unit[0][i] + s;
        }
        return head + s.replaceAll("(零.)*零元", "元").replaceFirst("(零.)+", "").replaceAll("(零.)+", "零").replaceAll("^整$", "零元整");
    }
}
```

自己测试发现能满足业务需要，所以就直接拿来主义了！

后面自己review时发现原贴下面的一条评论是这样的

> Math.floor(n * 10 * Math.pow(10, i)) % 10)第一个方法里面，是否会因为double*10精度问题进行取整导致数据不正确？ 比如40899.84 * 10的double值408998.39999999....向下取整只会取到3，正好碰到了这个问题

通过mian方法测试发现确实存在这个问题

```java
public static void main(String[] args) {
    // 这里使用System.err与System.out没有区别，只是会用红色字体显示，可以很清晰的看出与日志的区别，强烈建议在测试的时候使用
    System.err.println(digitUppercase(40899.84));
}
输出结果:肆万零捌佰玖拾玖元捌角叁分
```

原因：Java中浮点类型运算会产生精度问题(二进制浮点运算引起)

尝试解决问题，思路是既然只有小数部分有问题就把小数部分截取后转换成整数处理

```java
public class ConvertNumberToUpper {
    public static String digitCapital(double n) {
        String fraction[] = {"角", "分"};
        String digit[] = {"零", "壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖"};
        String unit[][] = {{"元", "万", "亿"}, {"", "拾", "佰", "仟"}};
	
        String head = n < 0 ? "负" : "";
        // 如果是负数取绝对值
        n = Math.abs(n);
        String s = "";
        // 由于n为double类型，所以整数如100转换后变为100.0
        String nStr = n + "";
        // 小数部分，小数点为特殊符号，在分割时需进行转义
        String split = nStr.split("\\.")[1];
		// 如果小数部分只有一位且为0，直接舍弃
        if (!(split.length() == 1 && "0".equals(split))) {
			// 只精确到小数点后两位
            if (split.length() > 2) {
                split = split.substring(0, 2);
            }
            // 将小数部分转换为整数
            Integer integer = Integer.valueOf(split);
            String p = "";
            for (int i = 0; i < split.length() && i < fraction.length; i++) {
                p = digit[integer % 10] + fraction[split.length() - i - 1] + p;
                integer = integer / 10;
            }
            s = p.replaceAll("(零.)+", "") + s;
        }
        if (s.length() < 1) {
            s = "整";
        }
        int integerPart = (int)Math.floor(n);
        // 整数部分
        for (int i = 0; i < unit[0].length && integerPart > 0; i++) {
            String p = "";
            for (int j = 0; j < unit[1].length && n > 0; j++) {
                p = digit[integerPart % 10] + unit[1][j] + p;
                integerPart = integerPart / 10;
            }
            s = p.replaceAll("(零.)*零$", "").replaceAll("^$", "零") + unit[0][i] + s;
        }
        return head + s.replaceAll("(零.)*零元", "元").replaceFirst("(零.)+", "").replaceAll("(零.)+", "零").replaceAll("^整$", "零元整");
    }
```

测试发现解决了上面的问题

然而又发现新的问题

```java
public static void main(String[] args) {
    System.err.println(digitCapital(40800099.84));
}
输出结果:肆仟捌拾万零玖拾玖元捌分
```

这是因为java中对过大的数据采用科学计数法表示，debug会发现传到方法中的n已经变为`4.080009984E7`

![java金额大写](\uploads\java金额大写.png)

然后就是解决问题了，思路就是不使用科学计数法表示数据

查资料发现有两种方式实现

```java
// 方法一
NumberFormat nf = NumberFormat.getInstance();
nf.setGroupingUsed(false);
String nStr = nf.format(n);
//方法二
BigDecimal bigDecimal = new BigDecimal(Double.valueOf(n).toString());
String nStr = bigDecimal.toString();
```

通过下面简单测试，发现方法二的效率明显要好于方法一

```java
public static String digitCapital(double n) {
    String fraction[] = {"角", "分"};
    String digit[] = {"零", "壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖"};
    String unit[][] = {{"元", "万", "亿"}, {"", "拾", "佰", "仟"}};

    String head = n < 0 ? "负" : "";
    // 如果是负数取绝对值
    n = Math.abs(n);
    String s = "";
    BigDecimal bigDecimal = new BigDecimal(Double.valueOf(n).toString());
    String nStr = bigDecimal.toString();
    // NumberFormat nf = NumberFormat.getInstance();
    // nf.setGroupingUsed(false);
    // String nStr = nf.format(n);
    // 由于n为double类型，所以整数如100转换后变为100.0
    // 小数部分
    String[] split = nStr.split("\\.");
    if (split.length > 1) {
        // 小数点为特殊符号，在分割时需进行转义
        String decimalStr = split[1];
        if (decimalStr.length() > 2) {
            decimalStr = decimalStr.substring(0, 2);
        }
        // 将小数部分转换为整数
        Integer integer = Integer.valueOf(decimalStr);
        String p = "";
        for (int i = 0; i < decimalStr.length() && i < fraction.length; i++) {
            p = digit[integer % 10] + fraction[decimalStr.length() - i - 1] + p;
            integer = integer / 10;
        }
        s = p.replaceAll("(零.)+", "") + s;
    }
    if (s.length() < 1) {
        s = "整";
    }
    int integerPart = (int)Math.floor(n);
    // 整数部分
    for (int i = 0; i < unit[0].length && integerPart > 0; i++) {
        String p = "";
        for (int j = 0; j < unit[1].length && n > 0; j++) {
            p = digit[integerPart % 10] + unit[1][j] + p;
            integerPart = integerPart / 10;
        }
        s = p.replaceAll("(零.)*零$", "").replaceAll("^$", "零") + unit[0][i] + s;
    }
    return head + s.replaceAll("(零.)*零元", "元").replaceFirst("(零.)+", "").replaceAll("(零.)+", "零").replaceAll("^整$", "零元整");
}

public static void main(String[] args) {
    long t1 = System.currentTimeMillis();
    System.err.println(digitCapital(1234340899.011231));
    long t2 = System.currentTimeMillis();
    System.err.println(t2 - t1);
    long t3 = System.currentTimeMillis();
    System.err.println(digitCapital(1234340899.011231));
    long t4 = System.currentTimeMillis();
    System.err.println(t4 - t3);
}
```

### 最终版

```java
public static String digitCapital(double n) {
    String fraction[] = {"角", "分"};
    String digit[] = {"零", "壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖"};
    String unit[][] = {{"元", "万", "亿"}, {"", "拾", "佰", "仟"}};

    String head = n < 0 ? "负" : "";
    // 如果是负数取绝对值
    n = Math.abs(n);
    String s = "";
    BigDecimal bigDecimal = new BigDecimal(Double.valueOf(n).toString());
    String nStr = bigDecimal.toString();
    // 小数部分
    String[] split = nStr.split("\\.");
    if (split.length > 1) {
        // 小数点为特殊符号，在分割时需进行转义
        String decimalStr = split[1];
        if (decimalStr.length() > 2) {
            decimalStr = decimalStr.substring(0, 2);
        }
        // 将小数部分转换为整数
        Integer integer = Integer.valueOf(decimalStr);
        String p = "";
        for (int i = 0; i < decimalStr.length() && i < fraction.length; i++) {
            p = digit[integer % 10] + fraction[decimalStr.length() - i - 1] + p;
            integer = integer / 10;
        }
        s = p.replaceAll("(零.)+", "") + s;
    }
    if (s.length() < 1) {
        s = "整";
    }
    int integerPart = (int)Math.floor(n);
    // 整数部分
    for (int i = 0; i < unit[0].length && integerPart > 0; i++) {
        String p = "";
        for (int j = 0; j < unit[1].length && n > 0; j++) {
            p = digit[integerPart % 10] + unit[1][j] + p;
            integerPart = integerPart / 10;
        }
        s = p.replaceAll("(零.)*零$", "").replaceAll("^$", "零") + unit[0][i] + s;
    }
    return head + s.replaceAll("(零.)*零元", "元").replaceFirst("(零.)+", "").replaceAll("(零.)+", "零").replaceAll("^整$", "零元整");
}
```

**注意**:Integer的取值范围是从-2147483648 至 214748364，超过该范围就会按最大值计算