## Bugs
* * *
* $this不能在静态上下文使用
    > $this指当前类实例.但是静态方法可以被访问不需要实例化类,使用$this在静态上下文将在运行时引起一个致命错误。

    <p>不规范的示例:</p>

    <pre><code>
    class Clazz {
      pubilc $name = null;  // instance variable

      public static function foo(){
        if ($this->name != null) {
          // ...
        }
      }
    }
    </code></pre>
    <p>合理解决示例:</p>

    <pre><code>
    class Clazz {
      pubilc $name = null;  // instance variable

      public static function foo($nameParam){
        if ($nameParam != null) {
          // ...
        }
      }
    }
   </code></pre>  
* 对象不能创建了就立马丢弃而没有使用它

  <p>不规范的示例:</p>
      if ($x < 0) {
        new foo;  // Noncompliant
      }
  <p>合理解决示例:</p>
      $var = null;
      if ($x < 0) {
        $var = new foo;
      }
* 二进制操作符两边不能使用相同表达式
  <p>不规范的示例:</p>
      if ($a == $a) { // always true
        doZ();
      }
      if ($a != $a) { // always false
        doY();
      }
      if ($a == $b && $a == $b) { // if the first one is true, the second one is too
        doX();
      }
      if ($a == $b || $a == $b) { // if the first one is true, the second one is too
        doW();
      }

      $j = 5 / 5; //always 1
      $k = 5 - 5; //always 0
* 跳转语句后面不要跟其他语句
  > 跳转语句(return,break,continue,goto)和throw表达式移出当前控制流代码块.

  <p>不规范的示例:</p>
      function fun($a) {
        $i = 10;
        return $i + $a;
        $i++;             // this is never executed
      }

      function foo($a) {
        if ($a == 5) {
          goto error;
        } else {
          // do the job
        }
        return;

        error:
          printf("don't use 5"); // this is reachable but unreadable    
      }
  <p>合理解决示例:</p>
      function fun($a) {
        $i = 10;
        return $i + $a;
      }

      function foo($a) {
        if ($a == 5) {
          handleError();
        } else {
          // do the job
        }
        return;
      }
* 用短路逻辑来防止空指针解引用在条件判断语句中

  <p>不规范的示例:</p>
      if ($obj == null && $obj->isOpen()) {
        echo "Object is open";
      }

      if ($obj != null || $obj->isOpen()) {
        echo "Object is not open";
      }
  <p>合理解决示例:</p>
      if ($obj == null || $obj->isOpen()) {
        echo "Object is open";
      }

      if ($obj != null && !$obj->isOpen()) {
        echo "Object is not open";
      }
* 变量不要 "自赋值"
  <p>不规范的示例:</p>
      public function setName($name) {
          $name = $name;
      }
  <p>合理解决示例:</p>
      public function setName($name) {
        $this->name = $name;
      }
* 函数参数名要唯一
      function compute($a, $a, $c) { // Noncompliant
      }
      function compute($a, $b, $c) { // Compliant
      }
* 没用的"if(ture){}"和"if(false){}"代码块要移除
  > if语句条件总是false使代码块无效的作用。总是true代码可读性差

      if (true) {  // Noncompliant
        doSomething();
      }
      ...
      if (false) {  // Noncompliant
        doSomethingElse();
      }
      doSomething(); // Compliant
* 非空的语句要改变控制流或者有至少一个作用
  >

      $a == 1; // Noncompliant; was assignment intended?
      $a < $b; // Noncompliant; have we forgotten to assign the result to a variable?
* "if/else if"语句和"cases"在一个"switch"不要有相同条件
  > swtich和if/else if链语句等价于从顶到底，至多只有一个分支执行。
  因此，重复的条件将导致无作用代码。

  <p>不规范的示例:</p>
      if ($param == 1) {
        openWindow();
      } else if ($param == 2) {
        closeWindow();
      } else if ($param == 1) { // Noncompliant
        moveWindowToTheBackground();
      }
      switch($i) {
        case 1:
          //...
          break;
        case 3:
          //...
          break;
        case 1:  // Noncompliant
          //...
          break;
        default:
          // ...
          break;
      }
* "exit()"和"die()"语句不要用
  > 在web php页exit和die一定不能用，可能导致不好的用户体验 cli可以用

* 定义符号的文件不能引起"副作用"
  > 定义符号（如类和变量）的文件可能会被包含在许多文件中。执行该包含对于除了声明新符号之外的那些文件应该没有影响。例如，包含类定义的文件也不应该包含副作用,例如打印语句.逻辑应分隔为符号仅文件和副作用文件。符号定义文件中不允许的操作类型包括但不限于：
    * 产生输出
    * 修改ini设置
    * 发出错误或例外
    * 修改全局或静态变量
    * 读/写文件

  SEE [PHP-FIG Basic Coding Standard PSR1]("http://www.php-fig.org/psr/psr-1/"), 2.3 - Side Effects
* "require_once"和"include_once"应该替代"require"和"include"
* 错误不应该被沉默
  > 不适用 @ 清除函数错误

* 文件不能包含字符在"<?php"之前
  > 有字符可能会引起"不能修改头信息"错误和相似问题ajax请求

* 方法可见性应该明确声明
  > 类方法可以生命为public,protected,private。不声明默认为public。

* 静态成员要用"static::"引用
  > 可以用self::和static::引用静态成员(字段或方法,常量?) 区别作用域：self引用原始成员定义, static引用运行时调用的类

  <p>不规范的示例:</p>
      class Toy
      {  
        public static function status()
        {
          self::getStatus();  // Noncompliant; will always print "Sticks are fun!" even when called from a subclass which overrides this method;
        }
        protected static function getStatus()
        {
          echo "Sticks are fun!";
        }
      }

      class Ball extends Toy
      {
        protected static function getStatus()  // Doesn't actually get called
        {  
          echo "Balls are fun!";
        }
      }
      $myBall = new Ball();
      $myBall::status();  // Prints "Sticks are fun!"
  <p>排除: 没有问题产生当self用于private成员或者private方法</p>
      class A
      {
        private static $somevar = "hello";
        const CONSTANT = 42;

        private static function foo()
        {
          $var = self::$somevar . self::CONSTANT;  // Should be OK
          self::foo();                               // Should be OK
        }
      }
* * *
## Code Smells
* * *
* Switch cases要以"break"结尾

  <p>不规范的示例:</p>
      switch ($myVariable) {
        case 1:  
          foo();
          break;
        case 2:  // Both 'doSomething()' and 'doSomethingElse()' will be executed. Is it on purpose ?
          do_something();
        default:  
          do_something_else();
         break;
      }
  <p>合理解决示例:</p>
      switch ($myVariable) {
        case 1:  
          foo();
          break;
        case 2:  
          do_something();
          break;
        default:  
          do_something_else();
         break;
      }
  <p>例外:</p>
      switch ($myVariable) {
        case 0:                  // Empty case used to specify the same behavior for a group of cases.
        case 1:  
          do_something();
          break;
        case 2:                  // Use of continue statement
          continue;
        case 3:                  // Case includes a jump statement (exit, return, break &etc)
          exit(0);
        case 4:
          echo 'Second case, which falls through';
          // no break        <- comment is used when fall-through is intentional in a non-empty case body
        default:                 // For the last case, use of break statement is optional
          doSomethingElse();
      }
* 动态命名变量不能使用
  <p>不规范的示例:</p>
      $var = 'foo';
      $$var = 'bar';      //Noncompliant
      $$$var = 'hello';  //Noncompliant

      echo $foo; //will display 'bar'
      echo $bar; //will display 'hello
* 函数不能嵌套太深
  > 嵌套太深可读性差

  <p>不规范的示例:默认3层阀值</p>
      function f () {
        function f_inner () {
          function f_inner_inner() {
            function f_inner_inner_inner() { // Noncompliant
            }
          }
        }
      }
* 字符串字面量不要重复
  > 重复的字符串字面量，你需要确保出现的地方全部更新. 可以用常量引用

  <p>不规范的示例: 默认3层阀值</p>
      function run() {
        prepare('action1');                              // Non-Compliant - 'action1' is duplicated 3 times
        execute('action1');
        release('action1');
      }
* 控制结构要用花括号
  > 省略花括号可能会导致误导，并可能导致在维护过程中引入错误。

      if (condition)  // Noncompliant
       executeSomething();

      if (condition) { //Compliant
       executeSomething();
      }
* 常量名称必须完全遵守一个命名约定
  > 共享编码约定让团队有效协作。此规则检查所有常量名称是否与提供的正则表达式匹配,默默认正则 ^[A-Z][A-Z0-9]\(\_[A-Z0-9]+)$

  <p>不规范的示例:</p>
      define("const1", true);
      class Foo {
          const const2 = "bar";
      }
