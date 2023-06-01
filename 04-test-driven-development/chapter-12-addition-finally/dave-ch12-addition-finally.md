## Chapter 12. Addition, Finally


```
<To-do List>
$5 + 10 CHF = $10 if rate is 2:1 
$5 + $5 = $10 <----
```

For the "$5 + $5 = $10"

```java
public void testSimpleAddition() {
    Money sum= Money.dollar(5).plus(Money.dollar(5)); 
    assertEquals(Money.dollar(10), sum);
}
```

```java
//Money
Money plus(Money addend) {
    return new Money(amount + addend.amount, currency); 
}
```
The solution is to create an object that acts like a Money but represents the sum of two Moneys. I've tried several different metaphors to explain this idea. One is to treat the sum like a wallet: you can have several different notes of different denominations and currencies in the same wallet.
Another metaphor is expression, as in "(2 + 3) * 5", or in our case "($2 + 3 CHF) * 5". A Money is the atomic form of an expression. Operations result in Expressions, one of which will be a Sum. Once the operation (such as adding up the value of a portfolio) is complete, the resulting Expression can be reduced back to a single currency given a set of exchange rates.
```
public void testSimpleAddition() {
...
assertEquals(Money.dollar(10), reduced); 
}
```
The reduced Expression is created by applying exchange rates to an Expression. What in the real world applies exchange rates? A bank.
```
public void testSimpleAddition() {
...
Money reduced= bank.reduce(sum, "USD"); 
assertEquals(Money.dollar(10), reduced);
}
```
* Expressions seem to be at the heart of what we are doing. I try to keep the objects at the heart as ignorant of the rest of the world as possible, so they stay flexible as long as possible(and remain easy to test, and reuse, and understand).

* I can imagine there will be many operations involving Expressions. If we add every operation to Expression, then Expression will grow without limit.

 I'm also perfectly willing to move responsibility for reduction(applies exchange rates) to Expression if it turns out that Banks don't need to be involved. The Bank in our simple example doesn't really need to do anything. As long as we have an object, we're okay:
```java
public void testSimpleAddition() {
    ...
    Bank bank= new Bank();
    Money reduced= bank.reduce(sum, "USD"); 
    assertEquals(Money.dollar(10), reduced);
}
```
The sum of two Moneys should be an Expression:
```java
public void testSimpleAddition() {
    ...
    Expression sum= five.plus(five); 
    Bank bank= new Bank();
    Money reduced= bank.reduce(sum, "USD"); 
    assertEquals(Money.dollar(10), reduced);
}
```
At least we know for sure how to get five dollars:
```java
public void testSimpleAddition() { 
    Money five= Money.dollar(5); 
    Expression sum= five.plus(five); 
    Bank bank= new Bank();
    Money reduced= bank.reduce(sum, "USD"); 
    assertEquals(Money.dollar(10), reduced);
}
```

How do we get this to compile? We need an interface Expression.
```java
//Expression
interface Expression
```

```java
//Money
Expression plus(Money addend) {
    return new Money(amount + addend.amount, currency); 
}
```
Money  has  to  implement Expression.

```java
//Money
class Money implements Expression

//Bank
class Bank{
    Money reduce(Expression source, String to) { 
    return Money.dollar(10);
    }
}

```
* Reduced a big test to a smaller test that represented progress ($5 + 10 CHF to $5 + $5) 
* Thought carefully about the possible metaphors for our computation
* Rewrote our previous test based on our new metaphor 
* Got the test to compile quickly
* Made it run
* Looked forward with a bit of trepidation to the refactoring necessary to make the 
implementation real
