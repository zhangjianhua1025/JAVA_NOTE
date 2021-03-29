# Java8新特性

------

## 1.Optional

### 1.1声明一个空的Optional  

Optional<Car> optCar = Optional.empty();  

### 1.2.依据一个非空值创建Optional  

Optional<Car> optCar = Optional.of(car);  

如果car是一个null，这段代码会立即抛出一个NullPointerException，而不是等到你
试图访问car的属性值时才返回一个错误。  

### 1.3.可接受null的Optional

Optional<Car> optCar = Optional.ofNullable(car);  

### 1.4.使用 map 从 Optional 对象中提取和转换值  

传统取值方法：

String name = null;
if(insurance != null){
	name = insurance.getName();
}  

为了支持这种模式， Optional提供了一个map方法。  

Optional<Insurance> optInsurance = Optional.ofNullable(insurance);
Optional<String> name = optInsurance.map(Insurance::getName);  

### 1.5.使用 flatMap 链接 Optional 对象  

public String getCarInsuranceName(Optional<Person> person) {
	return person.flatMap(Person::getCar)
		.flatMap(Car::getInsurance)
		.map(Insurance::getName)
		.orElse("Unknown");
}  

注意：链接值不可使用map，无法通过编译。

### 1.6.默认行为及解引用 Optional 对象  

get()是这些方法中最简单但又最不安全的方法。如果变量存在，它直接返回封装的变量值，否则就抛出一个NoSuchElementException异常 。

orElse(T other)  它允许你在Optional对象不包含值时提供一个默认值。  

orElseGet(Supplier<? extends T> other)是orElse方法的延迟调用版  

orElseThrow(Supplier<? extends X> exceptionSupplier  它们遭遇Optional对象为空时都会抛出一个异常 ，使用orElseThrow你可以定制希
望抛出的异常类型。  

ifPresent(Consumer<? super T>)让你能在变量值存在时执行一个作为参数传入的方法，否则就不进行任何操作。  

### 1.7.使用 filter 剔除特定的值 

Optional<Insurance> optInsurance = Optional.ofNullable(insurance);
optInsurance.filter(insurance -> "CambridgeInsurance".equals(insurance.getName()))
	.ifPresent(x -> System.out.println("ok"));  

备注：还有其它Optional类，如：OptionalDouble、OptionalInt、OptionalLong

## 2.函数接口

接口中有且只有一个接口方法，并且在接口上标注@FunctionalInterface注解

注：提供的函数接口：Consumer、BooleanSupplier、Function、Predicate、Supplier 。。。等等

可以配合lamda表达式进行函数式编程

## 3.Stream流

### 3.1.filter  筛选

List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
List<Integer> numberList = numbers.stream().filter(i -> i % 2 == 0) .collection(Collectors.toList());

### 3.2.distinct去重

List<Dish> dishes = menu.stream().filter(d -> d.getCalories() > 300).distinct().collect(Collectors.toList());  

### 3.3.limit截短流  返回前n个

List<Dish> dishes = menu.stream().filter(d -> d.getCalories() > 300).limit(3).collect(Collectors.toList());  

### 3.4.map映射

。

。

。

## 4.时间类

LocalDate、 LocalTime、 Instant、 Duration 以及 Period  

## 5.引用传递

使用关键字::