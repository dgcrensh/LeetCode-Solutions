# 1704. Determine if String Halves are Alike
You are given a string s of even length. Split this string into two halves of equal lengths, and let a be the first half and b be the second half.

Two strings are alike if they have the same number of vowels ('a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'). Notice that s contains uppercase and lowercase letters.

Return true if a and b are alike. Otherwise, return false.

## Intuition
There is an extremely straightforward way to solve this. Treat the first half of the string like an independent collection from the last half, iterate over both, and compare each character in the collections to each character in a collection of vowels. If the character in the input collections is present in the collection of vowels, increment a counter specific to that collection. If the counters for the two collections are equal, the two collections are "alike."

That approach has a very reasonable time complexity of $O(n*k)$ where $n$ is the length of the input string and $k$ is the length of the collection of vowels. There is absolutely nothing wrong with that solution, and because $k$ is constant for the purposes of the algorithm, its complexity is still $O(n)$. But because I find the easy way boring, I decided to come up with an algorithm that has a faster, constant-time test for membership in any collection of ASCII letters regardless of length $k$.

This proved quite simple. Since there are only 26 letters in the English alphabet, it is possible to use a 32-bit integer as a bitset in which each bit represents membership in the desired collection of characters. Each bit at index $i$ is either 1, indicating that the character in the alphabet at index $i$ is part of the desired collection, or 0, indicating that it is not. All that's left to do is find what this integer is for the collection of English vowels.

To do that, I opened a Python REPL and wrote the following simple script:

```python
magicNumber = 0
for n in [ord(x) - 65 for x in ['A','E','I','O','U']]:
    magicNumber |= 1 << n
print(hex(magicNumber))
```

This script gave me the "bitset" represented by the hex number 0x104111. Now for any character, I can apply a straightforward mathematical transformation to its ASCII value to determine whether it's a vowel in constant time. The rest of the algorithm is identical to the naive approach.

## Code
```c++
class Solution {
public:
    bool halvesAreAlike(string s) {
        int vowelTruthTable = 0x104111, firstHalfVowels = 0, lastHalfVowels = 0;
        for (auto startIterator = s.begin(), endIterator = s.end() - 1; startIterator < endIterator; ++startIterator, --endIterator) {
            if ((vowelTruthTable & (1 << ((*startIterator & ~32) - 65))) > 0) ++firstHalfVowels;
            if ((vowelTruthTable & (1 << ((*endIterator & ~32) - 65))) > 0) ++lastHalfVowels;
        }
        return firstHalfVowels == lastHalfVowels;
    }
};
```

In the LeetCode benchmarks, this code ran in 0ms and used less memory than ~97% of all other C++ solutions. Is it strictly necessary? Of course not, but "strictly necessary" isn't worth publishing.
