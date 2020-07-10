# Presenting vs Presented

presenting View Controller 是指将自己呈现的 VC，presented View Controller 则是自己呈现的 VC。

举例来说：A present B，此时，A 在底下，B 覆盖在 A 上面。

那么 

```
A.presenting -> nil
A.presented -> B

B.presenting -> A
B.presented -> nil
```

如果 presenting 和 presented 不是很好理解，可以用 parent 和 child 来代替，会好理解一些。

