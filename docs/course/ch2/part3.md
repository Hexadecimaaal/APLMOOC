# Arithmetic operations on vectors

!!! abstract "This part will cover"

    - Operations work on vectors all at once
    - Length error

---

So now we have vectors. You might wonder, why do we want to put a bunch of numbers inside a vector? Let’s say you, the astronaut on the ISS, mistakenly bought a bunch of measuring equipment from America, and only found out later that all the readings are in Fahrenheit. Terrible news! After about 2000 milliseconds of googling about it, you found this formula to convert it into Celsius:

Celsius = (Fahrenheit - 32) * 5 / 9

To test it out, you looked up the current room temperature reading, which says 72.1. You did the calculation in APL:

```apl
      (5 × 72.1 - 32) ÷ 9
22.27777778
```

Great! What’s not so great is that, the temperature sensor has been left generating data for the whole week, and there is a bunch of readings to convert to Celsius.

```apl
      TEMP_F ← 71.2 71.4 73.3 73.0 73.1 72.8 72.5
```

You are going to spend ages plugging the data from this one sensor, and you have 200 of them lying around! Fortunately, APL is designed to deal with data assorted in a vector. You can:

```apl
      ⎕ ← TEMP_CELSIUS ← (5 × TEMP_F - 32) ÷ 9
21.77777778 21.88888889 22.94444444 22.77777778 22.83333333 22.66666667 22.5
```

There’s all the Celsius! What’s going on here?

In APL, all the basic arithmetic functions apply "component-wise". If you are familiar with functional programming in Python or maybe Haskell, you might know the `map` function, which applies another function to each element of a vector. In APL, this is done automatically for all the basic arithmetic functions, if one of the parameters is a scalar:

```apl
      TEMP_F - 32
39.2 39.4 41.3 41 41.1 40.8 40.5
      TEMP_F + 32
103.2 103.4 105.3 105 105.1 104.8 104.5
      32 - TEMP_F
¯39.2 ¯39.4 ¯41.3 ¯41 ¯41.1 ¯40.8 ¯40.5
      100+1 2 3      ⍝ The space here takes precedent over +
101 102 103
```

Now you can do arithmetic to a list of numbers however you like! Just use them in place of a scalar value.

After figuring this out, you decided to also check the temperature reading of a sensor outside the ISS:

```apl
      TEMP_OUTSIDE ← 118.5 97.1 59.5 30.0 ¯9.7 ¯62.3 ¯113.2
```

That’s some extreme temperature right there! And it makes you start to wonder, how much temperature difference is the hull bearing? Turns out it’s also very simple in APL:

```apl
      ⎕ ← TEMP_DIFF ← TEMP_OUTSIDE - TEMP_CELSIUS
96.72222222 75.21111111 36.55555556 7.222222222 ¯32.53333333 ¯84.96666667 ¯135.7
```

So applying a basic arithmetic function to two vectors also just applies them "component-wise"! It just applies this function for the first element on the left and the first element on the right, note the result, then the second element on the left and the second element on the right, and so on. This is analogous to `map` with a binary operation and two iterators in Python, and `zipWith` in Haskell. There is one serious caveat though:

```apl
      1 2 3 - 4 5
LENGTH ERROR: Mismatched left and right argument shapes
      1 2 3-4 5
           ∧
```

The length of the vectors must match, APL will not silently truncate the longer one or fill in the difference.

The length of the vectors must match, APL will not silently truncate the longer one or fill in the difference.

```apl
      1 2 3 ÷ 2 2 0
DOMAIN ERROR: Divide by zero
      1 2 3÷2 2 0
           ∧
```

Unfortunately, APL doesn’t point out on which element the error occurred. If you get this kind of error in a complex expression, you can use the intermediate assignment and printout to help debug the situation.