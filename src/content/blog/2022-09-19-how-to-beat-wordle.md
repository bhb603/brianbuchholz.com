---
title: 'How to Beat Wordle Every Time'
description: 'How to beat wordle'
pubDate: 2022-09-19
publish: true
---

**WARNING:** This may spoil the challenge and fun of Wordle.

Wordle is a word game that gained popularity in 2021/22.
It was developed by Josh Wardle, a Brooklyn-based Software Engineer,
and [acquired by the New York Times for "low seven figures"][1] in early 2022.
It's now showcased alongside the Crossword, Spelling Bee, Sudoku, and other games in NY Time's App and website, where it is played by thousands (maybe millions) of people daily.

<center>

![Wordle](/images/wordle.png)

</center>

The rules of the game are simple:

- the goal is to guess a five-letter word in <= 6 guesses
- each guess must be a valid five-letter word
- the tile colors change after each guess to indicate how close it is to the answer
    - 🟩 Green: correct letter, correct position
    - 🟨 Yellow: correct letter, incorrect position
    - ⬜ Grey: incorrect letter

## Five-Letter Words

The first point to acknowledge is that the Wordle solution set is finite: there are only so many five-letter words in English.
For example [Collins Scrabble Words][2] has ~13k five-letter words.

A little digging on the nytimes.com `wordle.js` source code reveals there are two hardcoded lists of words:

<center>

![Wordle words](/images/wordle-words.png)

</center>

The first list contains **2,309 words**.
The second list contains **12,546 words**.

The clue for how these two lists are used appear in another [NY Times article][3]:

> An initial list of all of the five-letter words in the English language — about 12,000 — contained a lot of obscure words that would have been near impossible to guess.

So, the article goes on, they "narrowed down the list of Wordle words to about 2,500" based on whether they knew the word or not.

What can be inferred from all this:

- the Wordle solution is selected from the short list of 2,309 common words
- the full set of 12k+ five-letter words is probably used to validate guesses (remember that each guess must also be a valid English word)

## Solving Wordle Programmatically

Now that we have the list of all possible solutions, it is fairly trivial to write a program that filters the possible solutions based on all the previous guesses.

For example, consider the following game:

| Turn | Guess | Result  | Possible Solutions Remaining |
| --- | --- | --- | --- |
| 0 |  -   | -   |  2,309 |
| 1 |  ORATE | ⬜⬜🟨⬜🟩 | 45 |
| 2 | GLUMS | 🟩⬜🟩⬜⬜ | 2 |
| 3 | GAUZE | 🟩🟩🟩🟩🟩 | 1 |


The first guess, "ORATE", results in a yellow and green tile, requiring the solution to meet the following criteria:
- have an "A" somewhere, but NOT in the third position
- have an "E" in the fifth position
- NOT have an "O"
- NOT have an "R"
- NOT have an "T"

Applying those rules to the list of 2,309 words filters it down to 45 after just one guess!

The second guess "GLUMS", results in two green tiles, adding five more rules:
- have a "G" in the first position
- have a "U" in the third position
- NOT have a "L"
- NOT have an "M"
- NOT have an "S"

Applying the filter now leaves just two possible solutions, "GAUGE" and "GAUZE".
At this point it is a coin toss, but you are guaranteed to solve the Wordle in either the 3rd or 4th guess.

The filtering logic is fairly straightforward, but how do we know what words to guess in the first place? Why "ORATE" then "GLUMS"?

## Strategic Guessing

The success of the above example depends on the initial guesses.
If, on the second turn, we attempt to solve with "ABIDE", there is a 1/45 chance we win the game, but most likely it is wrong.
Even though "ABIDE" is a possible solution at that point, it isn't actually great at providing more clues.
In the above example, a second guess of "ABIDE" narrows the solution set from 45 -> 21, vs "GLUMS" which narrows from 45 -> 2.

| Turn | Guess | Result  | Possible Solutions Remaining |
| --- | --- | --- | --- |
| 0 |  -   | -   |  2,309 |
| 1 |  ORATE | ⬜⬜🟨⬜🟩 | 45 |
| 2 | ABIDE | 🟨⬜⬜⬜🟩 | 21 |

So what makes "GLUMS" better than "ABIDE" on average?

The key is using letters that are expected to reveal the most NEW information about the solution.
If we already know the solution has an "A" and "E", there is not much value in guessing those letters again.
Likewise, if none of the remaining solutions have an "R", there is no value in guessing "R".

The most valuable letter, in terms of its expected filtering ability, would be one that appears in 50% of the remaining solutions.
Guessing a word with such a letter would guarantee to eliminate 1/2 the remaining solutions, just from that single character.
In a way it's like binary search, and the optimal letters are the closest to the "midpoint", which will cut the problem in half with each pass.

Caveat: there is slightly more value in guessing a correct letter than an incorrect letter, because if you get a green tile, knowing the correct position of a given letter usually filters the solutions more than any exclusionary rule.

To rank each character (A-Z), count the number of remaining solutions WITH and WITHOUT each one.
The ratio of WITH:WITHOUT would correlate with the filtering-value discussed above, with 1.0 (50/50) being the best ratio.
To bias the score in favor of characters that appear more, add a coefficient proportional to the rate of occurence.
Scoring each character looks something like this:

```
ratio = abs(numWordsWith / numWordsWithout)

// bias toward wordsWith:
score = ratio + (numWordsWith/numTotalWords) * coefficient
```

At the start of every Wordle game, with all 2,309 possible solutions remaining, here is what that score looks like for the best and worst characters:


| char | words with | words without | ratio | score |
| ---- | ---------- | ------------- | ----- | ----- |
| E    | 1053       | 1256          | 0.84  | 0.93  |
| A    | 906        | 1403          | 0.65  | 0.72  |
| R    | 835        | 1474          | 0.57  | 0.64  |
| O    | 672        | 1637          | 0.41  | 0.47  |
| T    | 667        | 1642          | 0.41  | 0.46  |
| ...  | ...        | ...           | ...   | ...   |
| Z    | 35         | 2274           | 0.02   | 0.02   |
| Q    | 29         | 2280           | 0.01   | 0.02   |
| J    | 27         | 2282           | 0.01   | 0.01   |

"E" is the best letter to guess, because it appears in nearly half the words, so any guess with "E" will eliminate at least 1000 possible solutions.
"J" is the worst letter because it barely occurs.
If the Wordle answer happens to contain "J", you've hit the jackpot, but more likely it won't and the most it can do is eliminate 27 solutions.

Once each character is scored, we can score potential guess words simply by summing the score of each character.
For these, we can use all 12k+ valid words, not just the possible solutions, because many times their combination of letters will provide more value than any potential answer.

According to this algorithm, "ORATE" (or any anagram like "ROATE") is a great first guess.

## Limitations + Results

The approach outlined here is great for narrowing down the possible solution set in the first two guesses.
When there are < ~10 solutions remaining, it may be better to start guessing.

Once you start guessing there is a degree of luck, but narrowing the solution set to single digits in the first two guesses is key.

There are probably more optimal algorithms for calculating the best gueses, but I've found that this approach does work quite well in practice.
Here are my results for the past week's Wordles:

- 2022-09-19, "PRIZE", 4 guesses
- 2022-09-18, "ONSET", 3 guesses
- 2022-09-17, "CHIEF", 5 guesses
- 2022-09-16, "GAUZE", 3 guesses
- 2022-09-15, "RUDER", 4 guesses
- 2022-09-14, "IRONY", 4 guesses
- 2022-09-13, "WIDOW", 4 guesses
- 2022-09-12, "CLOWN", 3 guesses
- 2022-09-11, "NEEDY", 3 guesses
- 2022-09-10, "OCTAL", 2 guesses
- 2022-09-09, "WOVEN", 3 guesses
- 2022-09-08, "MERIT", 3 guesses

This approach can almost always solve Wordle in 3-4 guesses, and hasn't required more than five yet.

[1]: https://www.nytimes.com/2022/01/31/business/media/new-york-times-wordle.html
[2]: https://en.wikipedia.org/wiki/Collins_Scrabble_Words
[3]: https://www.nytimes.com/2022/01/03/technology/wordle-word-game-creator.html

