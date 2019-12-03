# Dictionary Word Finder
 
--- 
   
# Interviewer Prompt

Given an alphabetical array of dictionary entries and a word to search for, find that word's definition (if it exists). A dictionary entry is just a string where the word's name appears first, followed by ` - [definition]`.

```javascript
const dictionary = [
  'a - Used when mentioning someone or something for the first time in a text or conversation',
  'and - Used to connect words of the same part of speech, clauses, or sentences, that are to be taken jointly',
  'be - Exist',
  'in - Expressing the situation of something that is or appears to be enclosed or surrounded by something else',
  'of - Expressing the relationship between a part and a whole',
  'that - Used to identify a specific person or thing observed or heard by the speaker',
  'the - Denoting one or more people or things already mentioned or assumed to be common knowledge',
  'to - Expressing motion in the direction of (a particular location)'
];
definitionOf('be', dictionary); // should return 'Exist'
definitionOf('that', dictionary); // should return 'Used to identify a specific person or thing observed or heard by the speaker'
definitionOf('to', dictionary); // should return 'Expressing motion in the direction of (a particular location)'
definitionOf('wizbing', dictionary); // should return undefined
```

---


# Interviewer Guide

---

# Primary focuses

- Time / space complexity analysis
- Binary search
- Indexing / caching (for repeated executions)

---

# Complexity analysis

- *Push* your candidate to analyze the time and/or space complexity
- Do so when they're going over a possible approach
- ...but leave them an option to cover it later

---

# Binary search

- If your candidate quickly comes up with the naive solution, push them to come up with other approaches
- ...before they start implementing the naive approach
- If they don't come up with binary search, suggest it

---

# Indexing / caching

- Probably only worth covering this *after* the candidate has successfully implemented binary search
- ...so you probably will not get to this

---


# Solutions

---

# Brute force solution

The naive (brute force) solution is to loop down the dictionary each time, looking for an entry that starts with the search word. This is `O(n)` time and `O(m)` space complexity, where `n` is the size of the dictionary and `m` is the average size of an entire entry.

--

If we consider the length of a definable word an "input that can grow arbitrarily large", then the time complexity is `O(n * p)`, where `p` is average word length.

In reality, we know that the longest word is no longer than, say, 100 letters. That means `p` doesn't grow arbitrarily large.

---

# Brute force solution

```javascript
function definitionOf (word, dict) {
  for (let i = 0; i < dict.length; i++) {
    if (dict[i].startsWith(word + ' - ')) {
      return dict[i].slice(word.length + 3); // "subtract" the word itself (plus the ' - ' part)
    }
  }
}
```

...with fancy `.find` array method...

```javascript
function definitionOf (word, dict) {
  const foundEntry = dict.find(entry => entry.startsWith(word + ' - '));
  if (!foundEntry) return;
  return foundEntry.slice(word.length + 3); // "subtract" the word itself (plus the ' - ' part)
}
```

---

# Interviewer note

Your candidate might get stuck figuring out the *precise* code for confirming a dictionary match, e.g.:

```javascript
entry.startsWith(searchWord + ' - ');
```

...and/or for getting the definition *only* from an entire entry:

```javascript
entry.slice(searchWord.length + 3);
```

You can freely decide to let them struggle with these parts or to help them out (depending on how you want to challenge your candidate).

---

# Binary search solution

The optimized binary search solution can be `O(m * log n)` time (n is dict array length, m is word length) and `O(1)` space.

---

# Interviewer note

The less than `<` and greater than `>` operators can compare strings by alphabetical order (or really by character code, but that's close enough for what we're doing here). If a candidate goes down a rabbit-hole trying to implement a `compareByAlphabeticalOrder` function, just let them know that they can use `<`/`>`.

---

# Binary search solution

```javascript
function definitionOf (word, dict) {
  // initialize indexes at the beginning and end of the dictionary, these define the bounds of our "search window"
  let prevLeft = 0;
  let prevRight = dict.length - 1;
  let index;
  // continue until the index has not changed from the previous cycle
  while (index !== prevLeft && index !== prevRight) {
    // find the middle of the existing search window
    index = Math.floor((prevLeft + prevRight) / 2);
    if (dict[index].startsWith(word + ' - ')) { // startsWith is a string comparison, takes O(m) time
      return dict[index].slice(word.length + 3); // "subtract" the word itself (plus the ' - ' part)
    }
    if (word < dict[index]) {
      // "shrink" the right half of the search window
      prevRight = index - 1;
    } else {
      // "shrink" the left half of the search window
      prevLeft = index + 1;
    }
  }
}
```

---

# Note: 

`String.prototype.startsWith()` is a method built in to the String prototype with ECMAScript 2015 (aka: ES2015/ES6). So, it might not be available across all browsers or all JavaScript implementations.

You could implement it yourself with the following code (as per MDN):

```javascript
if (!String.prototype.startsWith) {
  String.prototype.startsWith = function(search, pos) {
    return this.substr(!pos || pos < 0 ? 0 : +pos, search.length) === search
  }
}
``` 

Notice that this is using the method `String.prototype.substr()`...

---

# Three methods that do similar things

```javascript
someStr.slice(start, end)
someStr.substring(start, end)
someStr.substr(start, length)
```

### Similarities:
- They all **do not** mutate the original string
- The first argument is required, the second is optional
- If there's only a `start`, the returned string goes to length

### Differences:
- `end` is exclusive in `.slice` and `.substring`, `length` is inclusive in `.substr` 
- If `end`/`length` < 0 in `.slice`, it grabs from end, but `.substring` and `.substr` return empty strings
- If `end` < `start` in `.slice`, it will return an empty string, while `.substring` will swap args

---

# Binary search solution

The more modular version:

```javascript
function definitionOf (word, dict) {
  const foundEntry = binaryFind(
    dict,
    entry => entry.startsWith(word + ' - '),
    entry => word < entry
  );
  return foundEntry.slice(word.length + 3); // "subtract" the word itself (plus the ' - ' part)
}
```

Notice that `binaryFind` is "assumed". In an interview, you can say something like "if I have time, I'll implement `binaryFind`".

This not only leads to more modular code, but also is a good way to get to a solution more quickly.

---

# Binary search solution

Possible implementation of `binaryFind`...

```javascript
function binaryFind (arr, matcher, comparator) {
  // initialize indexes at the beginning and end of the array, these define the bounds of our "search window"
  let prevLeft = 0;
  let prevRight = arr.length - 1;
  let index;
  // continue until the index has not changed from the previous cycle
  while (index !== prevLeft && index !== prevRight) {
    // find the middle of the existing search window
    index = Math.floor((prevLeft + prevRight) / 2);
    if (matcher(arr[index])) {
      return arr[index];
    }
    if (comparator(arr[index])) {
      // "shrink" the right half of the search window
      prevRight = index - 1;
    } else {
      // "shrink" the left half of the search window
      prevLeft = index + 1;
    }
  }
}
```

---

# Caching solution

The further-optimized precomputed hash map solution, `O(n)` time for the first run, `O(1)` for every subsequent run (AKA `O(n)` "pre-processing time"), and `O(n)` space:

```javascript
// this cache will hold ALL dictionaries (though we'll only ever have one)
const cache = new Map();
function findOrCreateHashMap (dict) {
  // use the dictionary array object itself as a KEY
  if (cache.has(dict)) return cache.get(dict);
  const hashMap = {};
  dict.forEach(entry => {
    const [word, definition] = entry.split(' - ');
    hashMap[word] = definition;
  });
  cache.set(dict, hashMap);
  return hashMap;
}
function definitionOf (word, dict) {
  const hashMap = findOrCreateHashMap(dict);
  return hashMap[word];
}
```

---

# Map...?

`Map` is an ES6 class that works a lot like an object, except...

## A `Map` can have objects as keys

```javascript
const exampleMap = new Map();
const exampleKey = { foo: 'bar' };
// woah an object is a key!?
exampleMap.set(exampleKey, 'this is any value');
exampleMap.get(exampleKey); // 'this is any value'
// must be the same object, not a different object with the same properties
exampleMap.get({ foo: 'bar' }); // undefined
```

---

# Summary

- Keep your candidate focused on time / space complexity analysis
- Push them to find a binary search solution
- ...If they finish that, push them to find the caching solution

