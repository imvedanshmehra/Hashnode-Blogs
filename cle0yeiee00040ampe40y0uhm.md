# How to find a valid anagram in Javascript?

In this blog post, I will discuss how we can write a Javascript function to find if two given strings are valid anagrams or not. This problem is on the list of one of the most commonly asked questions in tech interviews on [leetcode](https://leetcode.com/explore/featured/card/top-interview-questions-easy/).

## What is an anagram?

Anagrams are words that are made by rearranging the letters of the original word in a different order. For example, the word ***cat*** can be rearranged into ***act*** which is a valid anagram because each letter of the first word appears once in the second word, also the word ***javascript*** can be converted into ***vajascript.***

> 💡 The word "**anagram**" itself can be converted into "**nagaram",** which is a valid anagram.
> 
> (I know it sound very confusing to say anagram word over & over again.)

## Javascript solution

So there are a lot of different ways to solve this problem, in this article, I am going to use a method that I personally call "Frequency counter". (I am not sure if there is an official name for this algorithm, please let me know in the comments).

So, here is the solution:

```javascript
const isValidAnagram = (str1, str2) => {
    // Edge case
    if(str1.length !== str2.length) return false;

    const temp = {}

    for(let val of str1) {
        temp[val] = (temp[val] || 0) + 1;
    }

    for(let i = 0; i < str2.length; i++) {
       if(!(str2[i] in temp)) {
           return false;
       } else {
           temp[i] -= 1;
       }
    }
    return true;
}
```

We created a function called `isValidAnagram` which takes `str1` & `str2` and it will return `true` if the two words are an anagram and will return `false` if they are not.

The first thing that we are doing is, checking if the given two strings have the same length or not which is our edge case. If given two strings are not of the same length they cannot be a valid anagram.

Then, we created a temporary object `temp` which will store all the letters of `str1` as a key and the number of times they appear in `str1` as a value.

For example, if `str1="Javascript"`, our `temp` will look like this:

```javascript
temp = {
    a: 2,
    c: 1,
    i: 1,
    j: 1,
    p: 1,
    r: 1,
    s: 1,
    t: 1,
    v: 1
}
```

Then we loop over each letter of `str2` and check if that letter exists in `temp` , if it doesn't, we will return `false`.

If the letter exists in `temp` then we decrement the length of that letter in `temp`.

In the end, if the given two strings are an anagram each property of the `temp` should have `0` as the value.

*Time complexity: O(n)*

So, that was our solution to find if two given strings are valid anagrams in Javascript.

As I mentioned that there are different ways to solve this problem in Javascript, if you have a better solution let me know in the comments. I would love to see different implementations.

Lastly, if you enjoyed this article, consider following me on [Twitter](https://twitter.com/imvedanshmehra) where I keep sharing Javascript and coding-related content.