# Reflex 的变量系统（Var System）

## 动机

Reflex 在前端支持状态变量的一些基本操作。
Reflex 会自动将变量操作从 Python 转换为等效的 JavaScript。

以下是一个 Reflex 条件表达式的 Python 示例，如果阈值等于或大于 50 则返回 "Pass"，否则返回 "Fail"：

```py
rx.cond(
    State.threshold >= 50,
    "Pass",
    "Fail",
)
```

该条件表达式大致相当于以下 JavaScript：

```js
state.threshold >= 50 ? "Pass" : "Fail";
```

## 概述

简单来说，Reflex 中的 `Var` 代表一个 JavaScript 表达式。
如果类型已知，它可以是以下任何一种：

- `NumberVar` 代表求值为 JavaScript `number` 的表达式。`NumberVar` 可以支持整数和浮点值
- `BooleanVar` 代表布尔表达式。例如：`false`、`3 > 2`。
- `StringVar` 代表求值为字符串的表达式。例如：`'hello'`、`(2).toString()`。
- `ArrayVar` 代表求值为数组对象的表达式。例如：`[1, 2, 3]`、`'words'.split()`。
- `ObjectVar` 代表求值为对象的表达式。例如：`{a: 2, b: 3}`、`{deeply: {nested: {value: false}}}`。
- `NoneVar` 代表空值。这些可以是 `undefined` 或 `null`。

## 创建变量（Vars）

状态字段默认转换为 `Var`。此外，你可以使用 `rx.Var.create()` 从 Python 值创建 `Var`：

```py
rx.Var.create(4)  # NumberVar
rx.Var.create("hello")  # StringVar
rx.Var.create([1, 2, 3])  # ArrayVar
```

如果你想从原始 JavaScript 字符串显式创建 `Var`，可以直接实例化 `rx.Var`：

```py
rx.Var("2", _var_type=int).guess_type()  # NumberVar
```

在上面的示例中，`.guess_type()` 将尝试从通用 `Var` 类型向下转换为 `NumberVar`。
对于此示例，也可以调用函数 `.to(int)` 来代替 `.guess_type()`。

## 操作

`Var` 系统还支持一些其他基本操作。
例如，`NumberVar` 支持像 `+` 和 `-` 这样的基本算术运算，与 Python 中一样。
它还支持返回 `BooleanVar` 的比较运算。

也可以定义自定义 `Var` 操作：

```py
from reflex.vars import var_operation, var_operation_return, ArrayVar, NumberVar


@var_operation
def multiply_array_values(a: ArrayVar):
    return var_operation_return(
        js_expression=f"{a}.reduce((p, c) => p * c, 1)",
        var_type=int,
    )


def factorial(value: NumberVar):
    return rx.cond(value <= 1, 1, multiply_array_values(rx.Var.range(1, value + 1)))
```

使用 `js_expression` 传递显式的 JavaScript 表达式；在 `multiply_array_values` 示例中，我们传入了一个 JavaScript 表达式，该表达式通过使用 reduce 方法将每个元素与累积结果相乘（从初始值 1 开始）来计算数组 `a` 中所有元素的乘积。
随后，我们在 'factorial' 函数中利用 `rx.cond`，使用 `range` 函数实例化一个数组，并将此数组传递给 `multiply_array_values`。
