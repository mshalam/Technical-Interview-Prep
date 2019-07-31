# Prompt

Given a string, return an array of all the permutations of that string. The permutations of the string should be the same length as the original string (i.e. use each letter in the string exactly once) but do not need to be actual words.

The array that is returned should only contain unique values and its elements should be in alphabetical order.

# Examples

```javascript
stringPermutations('one');
// should return  [ 'eno', 'eon' 'neo', 'noe', 'oen', 'one']
stringPermutations('app');
// should return  [ 'app','pap','ppa']
stringPermutations('nn'); //should return  [ 'nn' ]
```

---

# Resources

- [Slides](http://slides.com/seemaullal/reacto#/)
- [REPL walkthrough](https://repl.it/lFv/49)

---

# Solutions

In general we're pretty stuck with `O(n!)` (factorial) time and space complexity (where `n` is the number of unique characters in the string). Frankly, the end result of the algorithm demands it, because for `n` possible characters, it turns out there are `n!` permutations of those characters.

For generating all possible permutations we could imagine doing so "position by position". There are `n` possible characters for the first position in the string. Each of those possibilities has `n-1` possibilities for the second position (i.e. excluding the character we put in the first position). In turn each of those possibilities has `n-2` possibilities for the third position (i.e. excluding the two characters already used for the first two positions). This continues until we reach the last character, which actually just has 1 possibility, because we will have used all other characters at that point. So `n` possibilities times `n-1` possibilities times `n-2` possibilities until we reach 1—that is exactly what `n!` is! You may find an explanation that is figuratively and literally more drawn out [here](https://www.khanacademy.org/math/precalculus/prob-comb/combinatorics-precalc/v/factorial-and-counting-seat-arrangements).

So one important lesson to take from this exercise: permutations problems will tend to be factorial time and space complexity.

---

The following solution iteratively generates all possible permutations then sorts that result:

```js
function stringPermutations(str) {
  var results = [];
  var letters = str.split('');
  results.push([letters.shift()]); //add first letter (as an array) to results
  while (letters.length) {
    var curLetter = letters.shift();
    var tmpResults = [];
    results.forEach(function(curResult) {
      for (var i = 0; i <= curResult.length; i++) {
        var tmp = curResult.slice(); //make copy so we can modify it
        //insert the letter at the current position
        tmp.splice(i, 0, curLetter);
        tmpResults.push(tmp);
      }
    });
    results = tmpResults; //overwrite the previous results
  }
  return results
    .map(function(letterArr) {
      return letterArr.join('');
    })
    .filter(function(el, index, self) {
      return self.indexOf(el) === index; //filter out non-unique words
    })
    .sort();
}
```

---

...a similar solution, but recursive might look like:

```javascript
function recursiveStringPermutations(str) {
  var results = [];
  getPerms(str, []);
  function getPerms(str, arr) {
    if (typeof str === 'string')
      //on first call, split the string into an array
      str = str.split('');
    if (!str.length)
      //base case- push the compiled results into the results variable
      results.push(arr.join(''));
    for (var i = 0; i < str.length; i++) {
      var letter = str.splice(i, 1);
      arr.push(letter);
      getPerms(str, arr); //recursive call
      arr.pop();
      str.splice(i, 0, letter);
    }
  }
  return results
    .filter(function(el, index) {
      return results.indexOf(el) === index; //filter out non-unique words
    })
    .sort();
}
```

---

Here is a solution that implicitly keeps the results sorted as it generates them (an optimization):

Without sorting before we start finding permutations, we will get n! _ log(n!) -- we have an array that is n! in length at that point. If we sort before our sort time is n _ log(n). In both situations, n is the length of the input string. Overall, finding all string permutations is n!

[Video Solution](https://www.youtube.com/watch?v=tqH1hAmK7DM)

---

```js
// finds all possible permutations *while* maintaining the order of the characters
function stringPermutations(str) {
  if (str.length === 1) return [str]; // base case
  const all = [];
  // go through each character in the string
  let i = 0;
  while (i < str.length) {
    // get each individual character
    const letter = str[i];
    // get all the other characters surrounding it
    const otherChars = str.slice(0, i) + str.slice(i + 1);
    // compute all permutations of the *other* characters
    stringPermutations(otherChars).forEach(submpermut => {
      // add the current letter to the front of each of these "sub-permutations"
      // include *that* into the full result set
      all.push(letter + submpermut);
    });
    // increment until we reach a new letter (to avoid duplicates in the result set)
    while (str[i] === letter) i++;
  }
  return all;
}
function sortedStringPermutations(str) {
  // first sort the characters in the string
  const sortedStr = str
    .split('')
    .sort()
    .join('');
  // then find the ordered permutations of that sorted string
  return stringPermutations(sortedStr);
}
```

