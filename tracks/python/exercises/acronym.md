## Problem and challenges

The acronym problem requires to student to filter a string, removing non-letters, split at word boundaries, and select the first letter of each word.

### Reasonable solutions

There are two characters in the test set that require special attention:

* the hypen, used to tie together words such as "metal-oxide"
* the underscore, which must **not** be included
* *Empty* words

The underscore can be silently remmoved, but the 
hypen marks the boundary between two words, and 
it is simplest to replace it with a space.

The hypen can create an *empty* words, as it does in "'Something - I made up...".  
Care must be taken to remove empty words.  

```python
def abbreviate(words):
    words = words.upper()
    words = words.replace('_', '')     
    words = words.replace('-', ' ')
    
    tla = [ word[0] for word in words.split(' ') if word ]
    return ''.join(tla)
```

#### Assembling Strings

Some solutions will assemble the result string one character at a time:

```python
def abbreviate(words):
    words = ...

    tla = ''
    for word in word_list:
        if word:
            tla = tla + word[0].upper()

    return tla
```

If student is concatenating strings, they should be told about buiding lists and join() them.

#### Regular Expressions

Regular Expressions can help to prune the cruft:

```python
iimport re

def abbreviate(words):
    return "".join(item[0] for item in re.findall(r"[a-zA-Z']+", words.upper()))

```

### Common Suggestions

Filtering is better done by looking for letters than by looking
for things that are not letters. The solutions
above assume that there are no digits or stray punctuation at the start of a word.
However, the hypen has a
special place as a pseudo-space.

As noted above, assembling a string character by character is slow.

Once string addition is replace with list joining(), this
is a natural place for a List Comprehension.

It would be more efficient to call the string method upper() only once,
either at the start or the end.

### Talking points

This is a simple problem: it should have a simple solution.

Little checking of inputs is required by the test cases, 
but solutions that do deserve praise.
