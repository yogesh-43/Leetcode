# Minimum Price with Discounts

## Problem Description

You are given two integer arrays:

- `prices` — the prices of items.
- `discounts` — the available discount percentages.

Each discount can be applied to **at most one item**, and each item can receive **at most one discount**.

An item may also receive **no discount**.

If an item has price `p` and receives a discount of `d%`, its final price is:

```text
(p * (100 - d)) / 100
```

The final price is **not rounded**.

The goal is to assign the discounts to the items in such a way that the **total final price is minimized**.

---

# Example 1

## Input

```python
prices = [10, 30, 21]
discounts = [50, 60]
```

We have:

```text
Prices:
10
30
21

Discounts:
50%
60%
```

We want to assign the largest discounts to the items where they provide the greatest savings.

The optimal assignment is:

```text
30 gets 60%
21 gets 50%
10 gets no discount
```

Calculate the final prices:

```text
30 * (100 - 60) / 100
= 30 * 40 / 100
= 12
```

```text
21 * (100 - 50) / 100
= 21 * 50 / 100
= 10.5
```

The item with price `10` receives no discount:

```text
10
```

Therefore:

```text
12 + 10.5 + 10
= 32.5
```

## Output

```text
32.50000
```

---

# Example 2

## Input

```python
prices = [100, 70]
discounts = [10, 40, 50]
```

There are more discounts than items.

We only need to use two discounts because each item can receive at most one discount.

The optimal assignment is:

```text
100 gets 50%
70 gets 40%
```

Calculate:

```text
100 * (100 - 50) / 100
= 50
```

```text
70 * (100 - 40) / 100
= 42
```

Total:

```text
50 + 42
= 92
```

The `10%` discount is unused.

## Output

```text
92.00000
```

---

# Example 3

## Input

```python
prices = [7, 3, 9]
discounts = [100, 100]
```

A `100%` discount makes an item completely free.

Apply the discounts to the two most expensive items:

```text
9 → 100% → 0
7 → 100% → 0
```

The price `3` receives no discount:

```text
3
```

Total:

```text
0 + 0 + 3
= 3
```

## Output

```text
3.00000
```

---

# Key Observation

The important part of the problem is deciding **which discount should be assigned to which price**.

Suppose we have:

```text
Prices:
10, 20

Discounts:
50%, 60%
```

The best assignment is:

```text
20 → 60%
10 → 50%
```

rather than:

```text
20 → 50%
10 → 60%
```

Why?

Because a discount produces more savings on a more expensive item.

For example:

```text
20 with 60%:
20 * 60 / 100 = 12 saved

10 with 50%:
10 * 50 / 100 = 5 saved

Total saved = 17
```

The opposite assignment:

```text
20 with 50%:
20 * 50 / 100 = 10 saved

10 with 60%:
10 * 60 / 100 = 6 saved

Total saved = 16
```

Therefore, we should pair:

```text
Largest price  ↔ Largest discount
Second largest price ↔ Second largest discount
...
```

---

# Approach

The solution can be implemented by:

1. Sorting `prices`.
2. Sorting `discounts`.
3. Taking the largest price and largest discount together.
4. Continuing until one of the arrays becomes empty.
5. Adding any remaining prices without discounts.

---

# Your Original Solution

Your original implementation was:

```python
def minPrice(self, prices, discounts):
    """
    :type prices: List[int]
    :type discounts: List[int]
    :rtype: float
    """

    prices.sort()
    discounts.sort()

    print(prices)
    print(discounts)

    mini = 0.0

    while prices and discounts:
        price = prices.pop()
        discount = discounts.pop()

        test = price * (100 - discount) / 100

        print(price, discount, test)

        mini += test

    if prices:
        mini += sum(prices)

    return mini
```

---

# How Your Solution Works

Consider:

```python
prices = [10, 30, 21]
discounts = [50, 60]
```

After sorting:

```text
prices:
[10, 21, 30]

discounts:
[50, 60]
```

Because `.pop()` removes the last element, we get the largest values first.

First iteration:

```python
price = prices.pop()
```

gives:

```text
price = 30
```

and:

```python
discount = discounts.pop()
```

gives:

```text
discount = 60
```

Calculate:

```text
30 * (100 - 60) / 100
= 12
```

Then:

```text
mini = 12
```

---

Second iteration:

```text
price = 21
discount = 50
```

Calculate:

```text
21 * (100 - 50) / 100
= 10.5
```

Now:

```text
mini = 12 + 10.5
     = 22.5
```

The remaining price is:

```text
10
```

There are no discounts left, so:

```text
mini += 10
```

Final result:

```text
22.5 + 10
= 32.5
```

Therefore:

```text
Output = 32.50000
```

---

# Important Issue You Encountered

You originally had:

```python
test = price * (100 - discount) // 100
```

The `//` operator performs **floor division**.

This is incorrect because the problem says:

> The final price is not rounded.

For example:

```text
21 with 50% discount
```

The correct calculation is:

```text
21 * 50 / 100
= 10.5
```

But with floor division:

```python
21 * 50 // 100
```

you get:

```text
10
```

That loses the `.5`.

This caused your earlier result to be:

```text
32.00000
```

instead of:

```text
32.50000
```

---

# Correct Division

Use:

```python
/
```

instead of:

```python
//
```

Correct:

```python
test = price * (100 - discount) / 100
```

Incorrect:

```python
test = price * (100 - discount) // 100
```

---

# Why Sorting Works

Suppose:

```text
prices = [10, 30]
discounts = [20, 50]
```

There are two possible assignments.

### Assignment 1

```text
30 → 50%
10 → 20%
```

Savings:

```text
30 * 50 / 100 = 15
10 * 20 / 100 = 2

Total savings = 17
```

### Assignment 2

```text
30 → 20%
10 → 50%
```

Savings:

```text
30 * 20 / 100 = 6
10 * 50 / 100 = 5

Total savings = 11
```

Therefore, assignment 1 is better.

The reason is:

```text
Higher price × higher discount = greater savings
```

So sorting both arrays in ascending order and matching from the end gives the optimal assignment.

---

# Why `pop()` Is Useful

After sorting:

```python
prices.sort()
discounts.sort()
```

we have:

```text
prices:
[10, 21, 30]

discounts:
[50, 60]
```

Python's:

```python
.pop()
```

removes the last element.

Therefore:

```python
prices.pop()
```

returns:

```text
30
```

and:

```python
discounts.pop()
```

returns:

```text
60
```

So we automatically pair the largest values:

```text
30 ↔ 60%
```

Then:

```text
21 ↔ 50%
```

---

# Complete Correct Implementation

```python
class Solution(object):
    def minPrice(self, prices, discounts):
        """
        :type prices: List[int]
        :type discounts: List[int]
        :rtype: float
        """

        # Sort both arrays
        prices.sort()
        discounts.sort()

        total = 0.0

        # Apply the largest discounts to the largest prices
        while prices and discounts:
            price = prices.pop()
            discount = discounts.pop()

            final_price = price * (100 - discount) / 100.0

            total += final_price

        # Remaining prices receive no discount
        if prices:
            total += sum(prices)

        return total
```

---

# Example Walkthrough

Input:

```python
prices = [10, 30, 21]
discounts = [50, 60]
```

After sorting:

```text
prices = [10, 21, 30]
discounts = [50, 60]
```

### Iteration 1

```python
price = 30
discount = 60
```

Final price:

```text
30 * 40 / 100
= 12
```

Total:

```text
12
```

### Iteration 2

```python
price = 21
discount = 50
```

Final price:

```text
21 * 50 / 100
= 10.5
```

Total:

```text
12 + 10.5
= 22.5
```

No discounts remain.

Remaining price:

```text
10
```

Final:

```text
22.5 + 10
= 32.5
```

Output:

```text
32.50000
```

---

# Edge Cases

## 1. More Prices Than Discounts

Input:

```python
prices = [10, 20, 30]
discounts = [50]
```

The largest price gets the only discount:

```text
30 → 50% → 15
```

The remaining items stay unchanged:

```text
10
20
```

Total:

```text
15 + 10 + 20
= 45
```

Output:

```text
45.0
```

---

## 2. More Discounts Than Prices

Input:

```python
prices = [100, 50]
discounts = [10, 40, 80]
```

Only two discounts can be used because there are only two items.

The optimal assignment is:

```text
100 → 80%
50 → 40%
```

Final prices:

```text
100 * 20 / 100 = 20
50 * 60 / 100 = 30
```

Total:

```text
50
```

The `10%` discount is unused.

Output:

```text
50.0
```

---

## 3. 100% Discount

Input:

```python
prices = [10, 20]
discounts = [100]
```

Apply the discount to the largest item:

```text
20 → 100% → 0
```

Remaining item:

```text
10
```

Total:

```text
10
```

Output:

```text
10.0
```

---

## 4. No Discounts

Input:

```python
prices = [10, 20, 30]
discounts = []
```

No items can be discounted.

Therefore:

```text
10 + 20 + 30 = 60
```

Output:

```text
60.0
```

The implementation handles this because:

```python
while prices and discounts:
```

does not execute when `discounts` is empty.

Then:

```python
if prices:
    total += sum(prices)
```

adds all prices normally.

---

## 5. No Prices

If:

```python
prices = []
discounts = [10, 20]
```

there are no items to discount.

The answer is:

```text
0
```

The `while` loop is skipped because `prices` is empty.

---

## 6. Fractional Final Prices

This is one of the most important edge cases.

Input:

```python
prices = [21]
discounts = [50]
```

Correct:

```text
21 * 50 / 100
= 10.5
```

Therefore:

```text
Output = 10.5
```

Do NOT use:

```python
// 
```

because:

```text
21 * 50 // 100 = 10
```

which is incorrect.

Always use:

```python
/
```

---

# Why We Pair Largest With Largest

There is a mathematical reason behind the greedy strategy.

The amount saved by applying discount `d` to price `p` is:

```text
saving = p * d / 100
```

The constant `/100` does not affect which assignment is better.

So we essentially want to maximize:

```text
p * d
```

Suppose:

```text
p1 > p2
```

and:

```text
d1 > d2
```

Compare the two assignments:

```text
p1 × d1 + p2 × d2
```

versus:

```text
p1 × d2 + p2 × d1
```

The difference is:

```text
(p1 × d1 + p2 × d2)
-
(p1 × d2 + p2 × d1)
```

which simplifies to:

```text
(p1 - p2)(d1 - d2)
```

Since:

```text
p1 > p2
d1 > d2
```

the result is positive.

Therefore:

```text
Largest price ↔ Largest discount
```

produces the maximum savings and therefore the minimum final price.

---

# Complexity Analysis

Let:

```text
n = number of prices
m = number of discounts
```

Sorting the prices:

```text
O(n log n)
```

Sorting the discounts:

```text
O(m log m)
```

The `while` loop processes at most:

```text
min(n, m)
```

elements:

```text
O(min(n, m))
```

Therefore the total complexity is:

```text
O(n log n + m log m)
```

The extra space depends on the sorting implementation, but the algorithm itself uses:

```text
O(1)
```

additional space apart from the arrays being sorted.

---

# Final Algorithm

```text
1. Sort prices in ascending order.
2. Sort discounts in ascending order.
3. Take the largest price and largest discount.
4. Apply the discount.
5. Add the discounted price to the answer.
6. Repeat while both arrays contain elements.
7. Add any remaining prices without discounts.
8. Return the total.
```

In code:

```python
prices.sort()
discounts.sort()

total = 0.0

while prices and discounts:
    price = prices.pop()
    discount = discounts.pop()

    total += price * (100 - discount) / 100.0

if prices:
    total += sum(prices)

return total
```

---

# Key Takeaways

- Use `/`, **not `//`**, because final prices can be fractional.
- Sort both arrays.
- Match the **largest price with the largest discount**.
- If there are more prices than discounts, the remaining prices receive no discount.
- If there are more discounts than prices, the extra discounts are unused.
- `pop()` after sorting lets us process the largest elements first.
- The greedy strategy works because it maximizes the total discount/savings.

```text
Largest Price
      ↓
Largest Discount
      ↓
Maximum Savings
      ↓
Minimum Final Price
```