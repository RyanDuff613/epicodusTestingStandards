# Epicodus Testing Standards
 What does "thoroughly tested" mean. 

## For Functions

### Required
- If a method has more than one potential return value, each potential return value should be tested for. 
- All branching logic within a method should be tested.

### Recommended 
- Test for unexpected behaviors and edge cases ie. what happens if a function is given an argument with an incorrect or unexpected data type or an exceptional value.

### Examples:

#### Function that returns a boolean:
```javascript
function isEven(intArg){
  return intArg % 2 === 0;
}
```

#### Required Tests

- `isEven()` can return two values, `true` or `false`. 
-  At minimum, we should write a test for each possible return value.
```javascript
  test('should return true for an even integer', () => {
    expect(isEven(8)).toEqual(true);
  }); //This test passes

  test('should return false for an odd integer', () => {
    expect(isEven(9).toEqual(false));
  }); //This test passes
```

#### Recommended Tests

- The above tests cover expected behaviors of the function. Generally that's all that will be required on your Independent Projects at Epicodus. However, it's always a good idea to test for unexpected behaviors and edge cases. To illustrate, look at what happens when we provide some non-integer arguments to `isEven()`;

- ##### `string` arguments:
```javascript
  test('should return false when given a string argument', () => {
    expect(isEven("hello")).toEqual(false);
  }); // this test passes

  test('should return false when given an empty string argument', () => {
    expect(isEven(" ")).toEqual(false);
  }); // this test fails

  test('should return false when given a string argument', () => {
    expect(isEven("3")).toEqual(false);
  }); // this test passes

  test('should return false when given a string argument', () => {
    expect(isEven("2")).toEqual(false);
  }); // this test fails
```

- ##### `boolean` arguments:
```javascript
  test('should return false when given a boolean argument', () => {
    expect(isEven(true)).toEqual(false);
  }); // this test passes

   test('should return false when given a boolean argument', () => {
    expect(isEven(false)).toEqual(false);
  }); // this test fails  
```
- With these tests, we're asserting that `isEven()` should always return `false` when given a non-integer argument. However, we're getting a number of failing tests. The reasons for this have to do with some of the idiosyncracies of Javascript, things we wouldn't necessarily predict when creating the function. This is the value of testing! Now we know that if we want `isEven()` to always return `false` when given a non-integer argument we have to add some additional logic. 
- We might also want to write some additional tests to see what happens when we provide a negative number as an argument. What about a floating-point number?


#### Function with branching logic: 
```javascript
function positiveOrNegative(intArg) {
  if (intArg > 0) {
    return "positive";
  } else if (intArg < 0) {
    return "negative";
  } else {
    return "zero";
  }
}
```

#### Required Tests
- `positiveNegative()` will return one of three potential `string` values. 
- Our tests should reach every possible branch of the logic and cover each possible return value. 

```javascript
test('returns "positive" for a postitive integer', () => {
  expect(positiveOrNegative(5)).toEqual('positive');
}); //This test passes

test('returns "negative" for a negative integer', () => {
  expect(positiveOrNegative(-3)).toEqual('negative');
}); // This test passes

test('returns "zero" for the integer 0', () => {
  expect(positiveOrNegative(0)).toEqual('zero');
}); // This test passes
```

#### Recommended Tests
- Tests for edge cases and unexpected behavior.

```javascript
test('returns "undefined" for any non-integer argument', () => {
  expect(positiveOrNegative("hello")).toEqual(undefined);
  expect(positiveOrNegative(true)).toEqual(undefined);
  expect(positiveOrNegative(null)).toEqual(undefined);
}); //This test fails

test('returns "positive" for Infinity', () => {
  expect(positiveOrNegative(Infinity).toEqual('positive'));
}); //This test passes
```
- In the first test we're asserting that we want our function to return `undefined` when given a non-integer argument. The test fails so we know we need to add additional logic to handle this behavior. The second test checks to see what our function will do when given Javascript's `Infinity` value. It passes so we don't need to write additional logic to handle this edge-case, good to know!.


### For Classes and Methods
- If the class contains a constructor function it should be tested. 
- Each class method should have at least one equivalent test.
- As with tests for functions, method tests should be written for each potential return value and should cover all branching logic.
- If a method does not return a value, but instead alters a value somewhere else in your app, a test should be written to confirm this behavior.

### Example:

#### A Class `Dog` with a constructor and two methods:

```javascript
function Dog(name, age, weight) {
  this.name = name;
  this.age = age;
  this.weight = weight;
  this.hungry = true;
}

Dog.prototype.bark = function(){
  return "Ruff!";
}

Dog.prototype.eat = function(foodWeight){
  if(this.hungry){
    this.weight += foodWeight;
    this.hungry = false;
  } else {
    this.weight -= 1;
  }
}
```

#### Required Tests
```javascript
describe('Dog', () => {
  // Create a new instance of Dog to be used throughout our tests;
  let dog1;
  beforeEach(() => {
    dog1 = new Dog("Fido", 3, 10);
  })

  // Test for constructor
  test('should create a new instance of a Dog', () => {
   expect(dog1.name).toEqual("Fido");
   expect(dog1.age).toEqual(3);
   expect(dog1.weight).toEqual(10);
   expect(dog1.hungry).toEqual(true);
  }); //This test passes

  test('bark() should return "Ruff!"', () => {
    expect(dog1.bark()).toEqual("Ruff!");
  }) //This test passes

  test('if a Dog is hungry, eat() should add to its weight property and turn the hungry property false', () => {
    dog1.eat(5);
    expect(dog1.weight).toEqual(15);
    expect(dog1.hungry).toEqual(false);
  }); // This test passes

  test('if a Dog is not hungry, calling eat() should reduce its weight', () => {
    // We create a new instance of a Dog and set it's 'hungry' property to 'false' before we call the eat() method.
    let dog2 = new Dog("Rufus", 2, 10)
    dog2.hungry = false;
    dog2.eat(5);
    expect(dog1.weight).toEqual(9);
  }) // This test passes
});
```

- The tests above cover the constructor function and both included methods. We only need one test for the `bark()` method because it doesn't accept arguments, doesn't contain any branching logic and has only one return value. 
- We include two tests for the `eat()` method. `eat()` does not have a return value but it does alter two properties of the object its called upon and contains two branches.


#### Recommended Tests
- Same as with functions, it's a good idea to test for unexpected inputs/ behaviors and edge cases.

```javascript
  test('providing a string number as an argument to the eat() method should not alter any properties of a Dog object', () => {
    dog1.eat("5");
    expect(dog1.weight).toEqual(15);
    expect(dog1.hungry).toEqual(false);
  }); // This test fails. 

  test('providing a string as an argument to the eat() method should not alter any properties of a Dog object', () => {
   dog1.eat("food");
    expect(dog1.weight).toEqual(15);
    expect(dog1.hungry).toEqual(false);
  }) // This test also fails.
```

- These tests are valuable. They alert us to the fact that we may want to add some additional logic to the method to prevent unexpected behavior. 