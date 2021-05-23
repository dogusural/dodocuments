## argv & argc

```c
./pointer one two

argv           
+----+          +----+
| .  |   --->   | .  |  ---> "./pointer\0"
+----+          +----+
                | .  |  ---> "one\0"
                +----+
                | .  |  ---> "two\0"
                +----+
```

