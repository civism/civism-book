# java新特性
* **java8**
1. 接口默认方法  
    用途: 可以扩展接口类，使得以前的接口不需要实现该方法。
    注意： 一个接口中可以同时拥有多个 默认方法
```
public interface Formula {
    double calculate(int a);
    default double sqrt(int a) {
        return Math.sqrt(a);
    }
    default double sqrt(double b) {
        return Math.sqrt(b);
    }
}
class TestA {
    public static void main(String[] args) {
        Formula formula = new Formula() {
            @Override
            public double calculate(int a) {
                return sqrt(a);
            }
        };
        System.out.println(formula.calculate(9));
    }
}
```
2. Lambda表达式(不过多表达)
```
案例一:
Collections.sort(names, (String a, String b) -> {
    return b.compareTo(a);
});
案例二:
Collections.sort(names, (a, b) -> b.compareTo(a));
```
3. @FunctionalInterface介绍  
      + 给接口添加@FunctionalInterface注解
      + 函数式接口，只能有一个未实现的方法声明
      + 可以有0到多个default和static方法声明
      + FunctionalInterface本质上还是Interface，声明与使用跟正常接口一样，只是附加了lambda表达式功能  
 **jdk自带常用接口分类**  
      + 应用(apply)函数(Function)，有进有出	**Function<T, R>	R apply(T t)**
      + 接受(accept)消费(Consumer)，只进不出	**Consumer<T>	void accept(T t)**
      + 测试(test)谓词(Predicate)，返回真假	**Predicate<T>	boolean test(T t)**
      + 获得(get)供给(Supplier)，不劳而获	**Supplier<T>	T get()**
4. Date/Time时间工具类
      + Clock类  
      + LocalDate
      + LocalTime
