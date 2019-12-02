
# Prompt   
                
Write a function that determines whether an input string has balanced brackets.

You are given an input string consisting of brackets—square `[ ]`, round `( )`, and curly `{ }`. The input string can include other text. Write a function that returns either `true` if the brackets in the input string are balanced or `false` if they are not. Balanced means that any opening bracket of a particular type must also have a closing bracket of the same type.

An empty input string or a string without brackets can also be considered "balanced".

# Examples

```js
hasBalancedBrackets('[][(){}'); // false
hasBalancedBrackets('({)}'); // false
hasBalancedBrackets('({[]})'); // true
hasBalancedBrackets('text ( is allowed ){rwwrwrrww [] ()}'); // true
```

# Solutions

In general an optimal approach is to keep a *stack* of open brackets, pushing as we come across them. As we come across closing brackets, if they match the most-recently-opened bracket we can pop the most-recently-opened bracket. If our stack is empty once we reach the end of the string, then our brackets must have been balanced.

Here's a solid solution:

```js
const bracketPattern = /[[\](){}]/g;
const bracketPairs = { //keeps track of the possible bracket pairings
  '[' : ']',
  '(' : ')',
  '{' : '}'
};
function hasBalancedBrackets (inputString) {
  const inputBrackets = inputString.match(bracketPattern); // returns an array of all the brackets in the input
  const brackets = [];
  if (!inputString.length || !inputBrackets.length) {
    return true; // empty input or no brackets i.e. 'balanced' (throwing an error is fine also)
  }
  inputBrackets.forEach(function (bracket) {
    const lastBracket = brackets[brackets.length - 1];
    if (bracketPairs[lastBracket] === bracket) { // the current bracket and the last bracket are a pair
      brackets.pop(); // we found a pair so remove the opening bracket from the array and move on
    } else {
      brackets.push(bracket);
    }
  });
  return brackets.length === 0; // if the brackets were balanced then we should not have any brackets in the array
}
```

A more optimal solution would cut out early if it comes across a closing bracket that does not match the most recent open bracket. That could look something like:

```js
const opens = {};
const closes = {};
([
  ['{', '}'],
  ['[', ']'],
  ['(', ')'],
]).forEach(([open, close]) => {
  opens[open] = close;
  closes[close] = open;
});

function hasBalancedBrackets (str) {
  const opensStack = [];
  for (const ch of str) {
    if (opens.hasOwnProperty(ch)) {
      opensStack.push(ch);
    } else if (closes.hasOwnProperty(ch)) {
      const mostRecentOpen = opensStack.pop();
      const correspondingClose = opens[mostRecentOpen];
      if (ch !== correspondingClose) return false;
    }
  }
  return opensStack.length === 0;
}
```

Here is another similar solution:

```js
  const bracketPattern = /[[\](){}]/g;
  const bracketPairs = {
    '[' : ']',
    '{' : '}',
    '(' : ')'
  };
  
  const hasBalancedBrackets = str => {
  const bracketStack = [];
  str = str.match(bracketPattern);
  for(let i = 0; i < str.length; i++){
    if(str[i] in bracketPairs) bracketStack.unshift(str[i]);
    else if (bracketPairs[bracketStack[0]] === str[i]) bracketStack.shift();
    else return false;
  }
  return !bracketStack.length;
};

```

