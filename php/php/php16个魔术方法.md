# PHP 16 个魔术方法

## 前言

PHP 中把两个下划线 __ 开头的方法称为**魔术方法**(Magic Methods), 魔术方法包括：

1. __construct(), 类的构造函数
2. __destruct(), 类的析构函数
3. __call(), 在对象调用一个不可访问的方法时调用
4. __callStatic(), 用静态方式调用一个不可访问的方法时调用
5. __get(), 获得一个类的成员变量时调用 
6. __set(), 设置一个类的成员变量时调用 
7. __isset(), 当对不可访问属性调用 isset() 或 empty() 时调用
8. __unset(), 当对不可访问属性调用 unset() 时调用
9. __sleep(), 执行 serialize() 时会先调用这个函数
10. __wakeup(), 执行 unserialize() 时会先调用这个函数
11. __toString(), 类被当成字符串时的回应方法
12. __invoke(), 调用函数的方式调用一个对象时的回应方法
13. __set_state(), 调用 var_export() 导出类时，此静态方法会被调用
14. __clone(), 当对象复制完成时调用
15. __autoload(), 尝试加载未定义的类
16. __debuginfo(), 打印所调试的信息

## 一、 __construct()，类的构造函数

php 中构造方法是对象创建完成后第一个被对象自动调用的方法。在每一个类中都有一个构造方法，如果没有显示地声明它，那么类中都会默认存在一个没有参数且内容为空的构造方法。

1. 构造方法的作用

通常构造方法被用来执行一些有用的初始化任务，如对成员属性在创建对象时赋予初始值。

2. 构造方法在类中的声明格式

```php
function __construct(){
    // 方法体
}
```

3. 在类中声明构造函数需要注意的事项

+ 在同一个类中只能声明一个构造函数，原因是 PHP不支持构造函数重载
+ 构造函数名称以双下划綫开始 `__construct()`

4. 范例
```php
<?php
class Marvel
{
    public $name;
    public $color;
    public $gender;

    /**
     * 显示声明一个构造方法且带参数
     */  
    public function __construct($name = "", $color = "red", $gender = 1){
        $this->name = $name;
        $this->color = $color;
        $this->gender = $gender;
    }

    /**
     * getName 方法
     */
    public function getName(){
        echo "my name is" . $this->name;
    }
}
```

## 二、 __destruct()，类的析构函数

与构造函数相对的就叫析构函数

析构函数允许在销毁一个类之前执行一些操作，如释放结果集，关闭文件等

析构函数是 PHP5 才引进的新内容

1. 析构函数在类中的声明格式

```java
function __destruct() {
    // 方法体
}
```

**注意**：析构函数不能带有任何参数。

2. 析构函数的作用

一般来说，析构函数在 PHP 不是很常用，它属于类中可选择的一部分，通常用来完成一些在对象销毁前的清理任务。

3. 范例
```php
<?php
class Marvel
{
    public $name;
    public $color;
    public $gender;

    /**
     * 显示声明一个构造方法且带参数
     */  
    public function __construct($name = "", $color = "red", $gender = 1){
        $this->name = $name;
        $this->color = $color;
        $this->gender = $gender;
    }

    /**
     * getName 方法
     */
    public function getName(){
        echo "my name is" . $this->name;
    }

    /**
     * 声明一个析构方法
     */  
     public function __destruct() {
         echo "goodbye, I'm" . $this->name;
     }
}

$ironman = new Marvel('钢铁侠');
unset($ironman);    // 输出：goodbye, I'm 钢铁侠
```

## 三、 __call()，在对象中调用一个不可访问方法时调用

该方法有两个参数，第一个参数 $function_name 会自动接收不存在的方法名，第二个 $arguments 则以数组的形式接收不存在方法的多个参数。

1. __call() 方法的格式

```php
function __call(string $function_name, array $arguments) {
    // 方法体
}
```

2. __call() 方法的作用

为了避免当调用的方法不存在时产生错误导致程序意外中止，可以使用 __call() 来避免。

该方法在调用的方法不存在时会自动调用，程序仍会继续执行下去。

3. 范例

```php
<?php
class Marvel
{
    /**
     * getName 方法
     */
    public function getName(){
        echo "I'm ironman";
    }

    /**
     * 声明此方法用来处理调用对象中不存在的方法
     */  
     public function __call($func_name, $arguments) {
        echo "你所调用的函数：" . $func_name . "(参数：" ;  // 输出调用不存在的方法名
        print_r($arguments); // 输出调用不存在的方法时的参数列表
        echo ")不存在！<br>\n"; // 结束换行
     }
}
```

```php
$hero = new Marvel();
$hero->setName("Ironman");  // 调用对象中不存在的方法，则自动调用了对象中的 __call() 方法
$hero->setGender(1);
$hero->getName();
```

执行结果：

```txt
你所调用的函数：setName(参数：Array ( [0] => Ironman ) )不存在！

你所调用的函数：setGender(参数：Array ( [0] => 1 ) )不存在！

I'm ironman
```

## 四、 __callStatic()，用静态方式中调用一个不可访问方法时调用

除了注意是用**静态方式**调用之外，其他的均与上述 __call() 函数一致，不再赘述。

## 五、__get(), 获得一个类的成员变量时调用

在 php 面向对象编程中，类的成员属性被设定为 private 后，如果我们试图访问该属性，则会抛出"不能访问私有属性"的错误。为了解决这个问题，我们可以用 __get() 魔术方法。

1. __get() 方法的作用

在程序运行的过程中，通过它可以在类的外部获取私有属性的值。

2. 范例

```php
<?php
class Marvel
{
    private $name;
    private $gender;
    
    public function __construct($name = "", $gender = 1){
        $this->name = $name;
        $this->gender = $gender;
    }

    /**
     * 在类中添加__get()方法，在直接获取属性值时自动调用一次，以属性名作为参数传入并处理
     * @param $property_name
     *
     * @return int
     */
    public function __get($property_name) {
        if ($property_name != "name" || $property_name != "gender"){
            return null;
        }else{
            return $this->property_name;
        }
    }
}
```

```php
$hero = new Marvel("IronMan", 1);
echo $hero->hero_name;  // null
echo $hero->name;   // IronMan
```

## 六、__set()，设置一个成员变量时调用
1. __set() 方法的作用

`__set(property, value)` 方法用来设置私有属性，给一个未定义的属性赋值时，此方法会被触发，传递的参数是被设置的属性名和值。

2. 范例

```php
class Marvel
{
    private $name;
    private $gender;
    
    public function __construct($name = "", $gender = 1){
        $this->name = $name;
        $this->gender = $gender;
    }

    /**
     * 声明 __set() 方法需要两个参数，直接为私有属性赋值时自动调用，并可以屏蔽一些非法赋值
     * @param $property
     * @param $value
     *
     * @return int
     */
    public function __set($property, $value) {
        if ($property == "gender") {
            if (!in_array($value, [1, 2, 3])) {
                return;
            }
        }
        
        $this->gender = $value;
    }

    public function show() {
        echo "My name is " . $this->name . ", My gender is " . $this->gender;
    }
}

$hero = new Marvel();
$hero->name = "Ironman";    //赋值成功。如果没有__set()，则出错。
$hero->gender = 1;  // 赋值成功
$hero->gender = 7;  // 赋值失败
$hero->show();  // My name is Ironman, My gender is 1
```

## 七、__isset()，当对不可访问属性调用 isset() 或 empty() 时调用

在看这个方法之前我们看一下 `isset()` 函数的应用，`isset()` 是测定变量是否设定用的函数，传入一个变量作为参数，如果传入的变量存在则传回 true，否则传回 false。

那么如果在一个对象外使用 `isset()` 函数去测定对象里面的成员是否被设定可不可以使用它呢？

分两种情况：

如果对象里面成员是公有的，我们就可以使用这个函数来测定；

如果是私有的，这个函数就不起作用了，原因是私有的被封装了，在外部不可见。

那么在对象的外部如何测定私有成员属性呢？

这里就要用到 `isset()` 方法了。

1. __isset() 方法的作用

当对不可访问属性调用 `isset()` 或 `empty()` 时，`__isset()` 会被调用。

2. 范例

```php
class Marvel
{
    public $name;
    private $gender;
    private $age;
    
    public function __construct($name = "", $gender = 1, $age = 22){
        $this->name = $name;
        $this->gender = $gender;
        $this->age = $age;
    }

    /**
     * @param $content
     *
     * @return bool
     */
    public function __isset($content) {
        return isset($this->$content);
    }
}

$hero = new Marvel("Ironman", 1);
print_r(isset($hero->name));
print_r(isset($hero->gender));
print_r(isset($hero->age));
```

## 八、__unset()，当对不可访问属性调用 unset() 时调用

在看这个方法之前我们看一下 `unset()` 函数的应用，`unset()` 是销毁指定的变量并返回 true，参数是要销毁的变量。

具体功能与 `isset()` 非常相似，这里不再赘述。

## 九、__sleep()，执行 serialize() 时会先调用这个函数

`serialize()` 函数会检查类中是否存在一个魔术方法 `__sleep()`。如果存在，则该方法会优先被调用，然后才执行序列化操作。

此功能可以用于清理对象，并返回一个包含对象中所有应被序列化的变量名称的数组。

如果该方法未返回任何内容，则 NULL 被序列化，并产生一个 `E_NOTICE` 级别的错误。

注意 ： `__sleep()` 不能返回父类的私有成员的名字。这样做会产生一个 E_NOTICE 级别的错误。可以用 `Serializable` 接口来替代。

1. __sleep() 方法的作用

`__sleep()` 方法常用于提交未提交的数据，或类似的清理操作。同时，如果有一些很大的对象，但不需要全部保存，这个功能就很好用。

2. 范例

```php
class Marvel
{
    public $name;
    private $gender;
    private $age;
    
    public function __construct($name = "", $gender = 1, $age = 22){
        $this->name = $name;
        $this->gender = $gender;
        $this->age = $age;
    }

    /**
     * @return array
     */
    public function __sleep() {
        echo "当在类外部使用serialize()时会调用这里的 __sleep() 方法<br>";
        $this->name = base64_encode($this->name);
        return array('name', 'age');
    }
}

$hero = new Marvel("Ironman", 1);
echo serialize($hero);
```

运行结果如下：

```
当在类外部使用serialize()时会调用这里的 __sleep() 方法
O:6:"Marvel":2:{s:4:"name";s:12:"SXJvbm1hbg==";s:11:"Marvelage";i:22;}
```

## 十、__wakeup()，执行 unserialize() 时，会先调用这个函数

如果说 `__sleep()` 是白的，那么 `__wakeup()` 就是黑的了。

那么为什么呢？

因为：

与之相反，`unserialize()` 会检查是否存在一个 `__wakeup()` 方法。如果存在，则会先调用 `__wakeup()` 方法，预先准备对象需要的资源。

1. __wakeup() 的作用

`__wakeup()` 经常用在反序列化操作中，例如重新建立数据库连接，或执行其它初始化操作。

2. 范例

```php
class Marvel
{
    public $name;
    private $gender;
    private $age;
    
    public function __construct($name = "", $gender = 1, $age = 22){
        $this->name = $name;
        $this->gender = $gender;
        $this->age = $age;
    }

    /**
     * @return array
     */
    public function __sleep() {
        echo "当在类外部使用serialize()时会调用这里的 __sleep() 方法<br>";
        $this->name = base64_encode($this->name);
        return array('name', 'age');
    }

    /**
     * __wakeup
     */
    public function __wakeup(){
        echo "当在类外部使用unserialize()时会调用这里的__wakeup()方法<br>";
        $this->name = "SpiderMan";
        $this->age = 17;
        // 这里不需要返回数组
    }
}

$hero = new Marvel("Ironman", 1);
var_dump(serialize($hero));
var_dump(unserialize(serialize($hero)));
```

第二个 `var_dump()` 执行结果如下：

```
当在类外部使用serialize()时会调用这里的 __sleep() 方法
当在类外部使用unserialize()时会调用这里的__wakeup()方法
object(Marvel)#2 (3) {
  ["name"]=>
  string(9) "SpiderMan"
  ["gender":"Marvel":private]=>
  NULL
  ["age":"Marvel":private]=>
  int(17)
}
```

## 十一、__toString()，类被当成字符串时的回应方法

1. __toString() 的作用

`__toString()` 方法用于一个类被当成字符串时该怎么回应。例如 `echo $obj;` 应该显示什么。

2. 注意

此方法必须返回一个字符串，否则将发出一条 `E_RECOVERABLE_ERROR` 级别的致命错误。

3. 警告

不能在 `toString()` 方法中抛出异常。这么做会导致致命错误。

4. 范例

```php
class Marvel
{
    public $name;
    private $gender;
    private $age;
    
    public function __construct($name = "", $gender = 1, $age = 22){
        $this->name = $name;
        $this->gender = $gender;
        $this->age = $age;
    }

    public function __toString()
    {
        return 'go go go';
    }
}

$hero = new Marvel("Ironman", 1);
echo $hero;     // go go go
```

5. 反向范例

如果类中没有 `__toString()` 这个魔术方法运行后会发生什么呢，让我们来试一试：

```php
class Marvel
{
    public $name;
    private $gender;
    private $age;
    
    public function __construct($name = "", $gender = 1, $age = 22){
        $this->name = $name;
        $this->gender = $gender;
        $this->age = $age;
    }
}

$hero = new Marvel("Ironman", 1);
echo $hero;
```

结果：

```
Catchable fatal error: Object of class Person could not be converted to string in D:\phpStudy\WWW\test\index.php on line 15
```

很明显，页面报了一个致命错误，这是语法所不允许的。

## 十二、__invoke()，调用函数的方式调用一个对象时的回应方法

1. 作用

当尝试以调用一个函数的方式调用对象时，`__invoke()` 方法会被自动调用。

2. 注意

本特性只在 PHP5.3.0 及以上版本有效。

3. 范例

```php
class Marvel
{
    public $name;
    private $gender;
    private $age;
    
    public function __construct($name = "", $gender = 1, $age = 22){
        $this->name = $name;
        $this->gender = $gender;
        $this->age = $age;
    }

    public function __invoke()
    {
        return '这可是一个对象哦';
    }
}

$hero = new Marvel("Ironman", 1);
$hero();     // 这可是一个对象哦
```

## 十三、 __set_state()，调用 var_export() 导出类时，此静态方法会被调用。

1. 作用

自 PHP 5.1.0 起，当调用 var_export() 导出类时，此静态方法会被自动调用。

2. 参数

本方法的唯一参数是一个数组，其中包含按 `array('property' => value, ...)` 格式排列的类属性。

3. 没有 __set_state() 示例

```php
class Marvel
{
    public $name;
    private $gender;
    private $age;
    
    public function __construct($name = "", $gender = 1, $age = 22){
        $this->name = $name;
        $this->gender = $gender;
        $this->age = $age;
    }
}

$hero = new Marvel("Ironman", 1);
var_export($hero);
```

结果：

```php
Marvel::__set_state(array('name'=>'Ironman', 'gender'=>1, 'age'=>22))
```

很明显，将对象的属性都打印出来了。

4. 加上 __set_state()

```php
class Marvel
{
    public $name;
    private $gender;
    private $age;
    
    public function __construct($name = "", $gender = 1, $age = 22){
        $this->name = $name;
        $this->gender = $gender;
        $this->age = $age;
    }

    public function __set_state($hero_array){
        $h = new Marvel();
        $h->name = $hero_array['name'];
        return $h;
    }
}

$hero = new Marvel("Ironman", 1);
$hero->name = 'Spider man';
var_export($hero);
```

结果：

```php
Marvel::__set_state(array('name'=>'Spider man', 'gender'=>1, 'age'=>22))
```


[思否](https://segmentfault.com/a/1190000007250604)