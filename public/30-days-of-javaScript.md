---
title: 30-days-of-javaScript
tags:
  - JavaScript
private: true
updated_at: '2024-03-10T01:26:56+09:00'
id: 2682035fa1f2c46afbb1
organization_url_name: null
slide: false
ignorePublish: false
---

# 概要

[30 Days of JavaScript](https://leetcode.com/studyplan/30-days-of-javascript/) でJavaScriptの基礎を学ぶためのプランをやってみる。

# 進捗

## 2667. Create Hello World Function

> Write a function createHelloWorld. It should return a new function that always returns "Hello World".

```javascript
function createHelloWorld() {
  return function (...args) {
    return "Hello World";
  };
}

// Example 1
const f1 = createHelloWorld();
console.log(f1()); // Output: "Hello World"

// Example 2
const f2 = createHelloWorld();
console.log(f2({}, null, 42)); // Output: "Hello World"
```

## 2620. Counter

> Given an integer n, return a counter function. This counter function initially returns n and then returns 1 more than
> the previous value every subsequent time it is called (n, n + 1, n + 2, etc).

```javascript
/**
 * @param {number} n
 * @return {Function} counter
 */
var createCounter = function (n) {
  return function () {
    return n++;
  };
};

/**
 * const counter = createCounter(10)
 * counter() // 10
 * counter() // 11
 * counter() // 12
 */
```

##

```javascript
/**
 * @param {string} val
 * @return {Object}
 */
var expect = function (val) {
  return {
    /**
     * toBe(val) accepts another value and returns true
     * if the two values === each other. If they are not equal,
     * it should throw an error "Not Equal".
     * @param expected
     */
    toBe: function (expected) {
      if (val !== expected) {
        throw new Error("Not Equal");
      }

      return true;
    },
    /**
     * notToBe(val) accepts another value and returns true
     * if the two values !== each other. If they are equal,
     * it should throw an error "Equal".
     */
    notToBe: function (expected) {
      if (val === expected) {
        throw new Error("Equal");
      }

      return true;
    },
  };
};
/**
 * expect(5).toBe(5); // true
 * expect(5).notToBe(5); // throws "Equal"
 */
```

## 2704. To Be Or Not To Be

> Write a function expect that helps developers test their code. It should take in any value val and return an object
> with the following two functions.
>
> toBe(val) accepts another value and returns true if the two values === each other. If they are not equal, it should
> throw an error "Not Equal".
> notToBe(val) accepts another value and returns true if the two values !== each other. If they are equal, it should
> throw an error "Equal".

```javascript
/**
 * @param {string} val
 * @return {Object}
 */
var expect = function (val) {
  return {
    /**
     * toBe(val) accepts another value and returns true
     * if the two values === each other. If they are not equal,
     * it should throw an error "Not Equal".
     * @param expected
     */
    toBe: function (expected) {
      if (val !== expected) {
        throw new Error("Not Equal");
      }

      return true;
    },
    /**
     * notToBe(val) accepts another value and returns true
     * if the two values !== each other. If they are equal,
     * it should throw an error "Equal".
     */
    notToBe: function (expected) {
      if (val === expected) {
        throw new Error("Equal");
      }

      return true;
    },
  };
};
/**
 * expect(5).toBe(5); // true
 * expect(5).notToBe(5); // throws "Equal"
 */
```
