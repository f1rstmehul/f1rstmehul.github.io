---
title: Integer overflow (in Go)
summary: this post shows how integer overflow happens
date: 2022-10-28
author: f1rstmehul
---

![overflow](https://upload.wikimedia.org/wikipedia/commons/5/53/Odometer_rollover.jpg)

# Overview

[Go](go.dev) provides four distinct sizes of signed and unsigned integers of size 8, 16, 32, and 64.

| sign     | size                          |
| -------- | ----------------------------- |
| signed   | uint8, uint16, uint32, uint64 |
| unsigned | int8, int16, int32, int64     |

for a variable like `uint8` and `int8` compiler allocates an 8-bit address register and a 32-bit register for `uint32` and `int32`.

To see integer overflow in action we'll take two examples of both int8 and uint8 to cover signed and unsigned integers.

## uint8

```Go
var u uint8 = 255
fmt.Println(u, u+1, u*u) // "255 0 1"
```

we add one to variable `u` then it becomes _256_ (_100000000_ in base 2), as the register is only 8-bit long. Still, we have 9-bit to store so, in this case, the most significant (also known as left-most or high-order) bits are discarded except the last 8-bit and those last 8-bit are stored in the variable `u`. last 8-bit of `100000000` are `00000000` which is 0 (base 10).

next we store `255*255= 65025` which is `1111111000000001` (base 2). So after discarding the high-order bits we left with `00000001` which is 1 (base 10).

## int8

Now take another example for a signed integer.

```Go
var i int8 = 127
fmt.Println(i, i+1, i*i) // "127 -128 1"
```

For a signed integer the most significant (left-most) bit indicates the sign of the integer, which is `1` for negative and `0` for positive.

So `127` is `01111111` (base 2) which starts with `0`. So when we add `1` to it. it becomes `10000000` (base 2).
if it was a type of `uint8` then the value would be `128`. but it is a signed number so the number is negative. `10000000` (base 2) represents `-128` in two's complement representation of the negative number.

again we calculate `127 * 127 = 16129` which is `11111100000001` (base 2) and store it in the `i` then only the last 8-bit will be kept which is `00000001` (base 2) i.e. `1`.

## bonus

### **_how 10000000 (base 2) is -128?_**

According to Two's complement method we invert all the bits and add one to it.

| -128 :       | 10000000 |
| ------------ | -------- |
| invert bits: | 01111111 |
| add one:     | 10000000 |

Final bits we get is 10000000, which is 128.
