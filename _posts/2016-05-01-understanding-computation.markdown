---
layout: post
title:  "计算的本质 python版代码(一)"
date:  2016-05-01 08:24:28
categories: jekyll update
---

# 第一章

原书是用Ruby来解释的, 这里换做Python.

主要说了通过定义对象是否以继续规约, 来实现树形表达式/语句的计算.

代码内容分为表达式和语句两种, 简单来讲表达式是必然可以计算出结果的(例如四则运算, 与或运算等), 语句则是为程序的逻辑结构, 赋值等行为服务的(例如=, while, if 等).

基本类型, 如Number和Boolean 是不可再规约的. 而例如Add, Sub之类的表达式, 会接受参数然后进行规约, 直到最后返回的是基本类型的结果(不可继续规约)为止.

```python
# coding: utf-8
class Expression(object):
    pass


class Statement(object):
    pass


class Boolean(Expression):
    def __init__(self, value):
        self.value = value
        self.reducible = False

    def __str__(self):
        return str(self.value)


class Or(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right
        
        self.reducible = True

    def reduce(self, env):
        if self.left.reducible:
            return Or(self.left.reduce(env), self.right)
        elif self.right.reducible:
            return Or(self.left, self.right.reduce(env))
        else:
            return Boolean(self.left.value or self.right.value)

    def __str__(self):
        return "{} or {}".format(self.left, self.right)


class And(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right

        self.reducible = True

    def reduce(self, env):
        if self.left.reducible:
            return And(self.left.reduce(env), self.right)
        elif self.right.reducible:
            return And(self.left, self.right.reduce(env))
        else:
            return Boolean(self.left.value and self.right.value)

    def __str__(self):
        return "{} and {}".format(self.left, self.right)


class Number(Expression):
    def __init__(self, value):
        self.value = value
        self.reducible = False

    def __str__(self):
        return "{}".format(self.value)


class Add(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right

        self.reducible = True

    def reduce(self, env):
        if self.left.reducible:
            return Add(self.left.reduce(env), self.right)
        elif self.right.reducible:
            return Add(self.left, self.right.reduce(env))
        else:
            return Number(self.left.value + self.right.value)

    def __str__(self):
        return "({} + {})".format(self.left, self.right)


class Multiply(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right

        self.reducible = True

    def reduce(self, env):
        if self.left.reducible:
            return Multiply(self.left.reduce(env), self.right)
        elif self.right.reducible:
            return Multiply(self.left, self.right.reduce(env))
        else:
            return Number(self.left.value * self.right.value)

    def __str__(self):
        return "({} * {})".format(self.left, self.right)


class LessThan(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right

        self.reducible = True

    def reduce(self, env):
        if self.left.reducible:
            return LessThan(self.left.reduce(env), self.right)
        elif self.right.reducible:
            return LessThan(self.left, self.right.reduce(env))
        else:
            if self.left.value < self.right.value:
                return Boolean(True)
            else:
                return Boolean(False)

    def __str__(self):
        return " {} < {} ".format(self.left, self.right)


class Variable(Expression):
    def __init__(self, name):
        self.name = name
        self.reducible = True

    def reduce(self, env):
        return env[self.name]

    def __str__(self):
        return "{}".format(self.name)


class DoNothing(Statement):
    def __init__(self):
        self.reducible = False

    def __eq__(self, other):
        return isinstance(other, self.__class__) and self.__dict__ == other.__dict__

    def __str__(self):
        return "do nothing"
        

class Assign(Statement):
    def __init__(self, name, expression):
        self.name = name
        self.expression = expression
        self.reducible = True

    def reduce(self, env):
        if self.expression.reducible:
            return Assign(self.name, self.expression.reduce(env)), env
        else:
            env.update({self.name: self.expression})
            return DoNothing(), env

    def __str__(self):
        return "{} = {}".format(self.name, self.expression)


class Sequence(Statement):
    def __init__(self, first, second):
        self.first = first
        self.second = second

        self.reducible = True

    def reduce(self, env):
        if self.first == DoNothing():
            return self.second, env
        else:
            reduced_first, env = self.first.reduce(env)
            return Sequence(reduced_first, self.second), env

    def __str__(self):
        return "{}; {}".format(self.first, self.second)


class If(Statement):
    def __init__(self, condition, consequence, alternative):
        self.condition = condition
        self.consequence = consequence
        self.alternative = alternative

        self.reducible = True

    def reduce(self, env):
        if self.condition.reducible:
            return If(self.condition.reduce(env), self.consequence, self.alternative), env
        else:
            if self.condition.value:
                return self.consequence, env
            else:
                return self.alternative, env

    def __str__(self):
        return "if ({0}) { { {1} } } else { { {2} } }".format(self.condition, self.consequence, self.alternative)


class While(object):
    def __init__(self, condition, body):
        self.condition = condition
        self.body = body

        self.reducible = True

    def reduce(self, env):
        return If(self.condition, Sequence(self.body, While(self.condition, self.body)), DoNothing()), env

    def __str__(self):
        return " while ( {0} ) { { {1} } }".format(self.condition, self.body)


class ExpressionMachine(object):
    def __init__(self, expression, env={}):
        self.env = env
        self.expression = expression

    def step(self):
        self.expression = self.expression.reduce(self.env)

    def run(self):
        while self.expression.reducible:
            print(self.expression)
            self.step()
        return self.expression.value


class StatementMachine(object):
    def __init__(self, statement, env):
        self.env = env
        self.statement = statement

    def step(self):
        self.statement, self.env = self.statement.reduce(self.env)

    def run(self):
        while self.statement.reducible:
            print(self.statement)
            self.step()
        print(self.env)


if __name__ == "__main__":
    pass
```


以下是单测:

```python
# coding: utf-8

from sample_1 import *
import unittest


class TestExpressionMachine(unittest.TestCase):
    def test_add(self):
        expression = Add(Multiply(Number(12), Number(11)), Number(12))
        machine = ExpressionMachine(expression)
        self.assertEqual(144, machine.run())

    def test_and(self):
        expression2 = And(Boolean(True), Boolean(False))
        machine2 = ExpressionMachine(expression2)
        self.assertFalse(machine2.run())

    def test_less(self):
        machine3 = ExpressionMachine(LessThan(Number(12), Number(13)))
        self.assertTrue(machine3.run())

    def test_variable(self):
        machine4 = ExpressionMachine(Multiply(Variable('x'), Variable('y')), {'x': Number(10), 'y': Number(20)})
        self.assertEqual(200, machine4.run())


class TestStatmentMachine(unittest.TestCase):
    def test_assign(self):
        machine = StatementMachine(Assign('x', Add(Variable('x'), Number(10))), {'x': Number(2)})
        machine.run()
        self.assertEqual(12, machine.env['x'].value)

    def test_if(self):
        machine = StatementMachine(If(
                Variable('x'),
                    Assign('y', Number(1)),
                    Assign('y', Number(2))),
                {'x': Boolean(False)})
        machine.run()
        self.assertEqual(False, machine.env['x'].value)
        self.assertEqual(2, machine.env['y'].value)

    def test_sequence(self):
        machine = StatementMachine(Sequence(
                Assign('x', Add(Number(1), Number(1))),
                Assign('y', Add(Variable('x'), Number(3)))), {})
        machine.run()
        self.assertEqual(2, machine.env['x'].value)
        self.assertEqual(5, machine.env['y'].value)

    def test_while(self):
        machine = StatementMachine(While(
            LessThan(Variable('x'),
                     Number(5)),
            Assign('x', Multiply(
                Variable('x'),
                Number(2)))), {'x': Number(1)})
        machine.run()
        self.assertEqual(8, machine.env['x'].value)

```
