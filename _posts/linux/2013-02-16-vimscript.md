---
layout: post
title:  "vimscript: OOP"
date:   2013-02-16 13:31:01 +0800
categories: linux
tags: vim
---

* content
{:toc}


# QuickStart

Please install plugin [vim-eval](https://github.com/amiorin/vim-eval) which can be used to execute current snippets code.

# OOP
[modularizing-vimscript](http://bling.github.io/blog/2013/08/16/modularizing-vimscript/)  
[vim-jslike-oop](https://github.com/t9md/vim-jslike-oop/blob/master/vim-jslike-oop.vim)  

## Modularizing VimScript

Let’s take a look at how we can create an object that is transient, has state, and contains methods you can invoke, like any modern OOP language can do.
```vim

    function! myobject#new()
      let obj = {}
      let obj._cats = []
    
      function! obj.add_cat()
        call add(self._cats, '(^.^)')
      endfunction
    
      function! obj.meow()
        for cat in self._cats
          echo cat
        endfor
      endfunction
    
      return obj
    endfunction
    
    " somewhere else
    let x = myobject#new()
    call x.add_cat()
    call x.meow()
```

This might look familiar to some of you. Yes, it’s almost the same as the JavaScript Module Pattern. Unfortunately, closures are not supported, but otherwise all of the usual benefits apply here, mainly controlled visibility into private state and transience!

## static,public,private functions

You can even take this concept further and replicate “static” functions:
```vim

    function! s:object#private_static()
    endfunction

    function! g:object#public_static()
    endfunction

    function! g:object#new()
      let obj = {}

      function! obj.public()
      endfunction

      function! obj._private()
      endfunction

      return obj
    endfunction
```

Yep, same story as JavaScript here – _ variables/functions are “private”.

# Dictionaries

You create a dictionary in Vimscript by using curly braces around a list of key/value pairs. In each pair, the key and value are separated by a colon. For example:

## Creating a dictionary

    let seen = {}   " Haven't seen anything yet

    let daytonum = { 'Sun':0, 'Mon':1, 'Tue':2, 'Wed':3, 'Thu':4, 'Fri':5, 'Sat':6 }
    let diagnosis = {
        \   'Perl'   : 'Tourettes',
        \   'Python' : 'OCD',
        \   'Lisp'   : 'Megalomania',
        \   'PHP'    : 'Idiot-Savant',
        \   'C++'    : 'Savant-Idiot',
        \   'C#'     : 'Sociopathy',
        \   'Java'   : 'Delusional',
        \}

## Access item of a dictionary

### by bracket

    let lang = input("Patient's name? ")
    let Dx = diagnosis[lang]

If the key doesn't exist in the dictionary, an exception is thrown:

    let Dx = diagnosis['Ruby']
    **E716: Key not present in Dictionary: Ruby**

### get() function

Using the get() function. get() takes two arguments: the dictionary itself, and a key to look up in it.

  - If the key exists in the dictionary, the corresponding value is returned;
  - if the key doesn't exist, get() returns zero.
  - Alternately, you can specify a third argument, in which case get() returns that value if the key isn't found:

    let Dx = get(diagnosis, 'Ruby')
    " Returns: 0

    let Dx = get(diagnosis, 'Ruby', 'Schizophrenia')
    " Returns: 'Schizophrenia'

### by dot notation

    let Dx = diagnosis.Lisp                    " Same as: diagnosis['Lisp']
    diagnosis.Perl = 'Multiple Personality'    " Same as: diagnosis['Perl']

This special limited notation makes dictionaries very easy to use as records or structs or object:

    let user = {}

    let user.name    = 'Bram'
    let user.acct    = 123007
    let user.pin_num = '1337'

## Iterator

Vimscript provides functions that allow you to get a list of all the keys in a dictionary, a list of all its values, or a list of all its key/value pairs:

    let keylist   = keys(dict)
    let valuelist = values(dict)
    let pairlist  = items(dict)

    for [k, v] in items(dict)
        let result = process(v)
        echo "Result for " k " is " result
    endfor

## Assignments and identities

    let dict2 = dict1             " dict2 just another name for dict1

    let dict3 = copy(dict1)       " dict3 has a copy of dict1's top-level elements

    let dict4 = deepcopy(dict1)   " dict4 has a copy of dict1 (all the way down)

### compare

    if dictA is dictB
        " They alias the same container, so must have the same keys and values
    elseif dictA == dictB
        " Same keys and values, but maybe in different containers
    else
        " Different keys and/or values, so must be different containers
    endif

## Adding and removing entries

    " Add
    let diagnosis['COBOL'] = 'Dementia'

    " Merge
    call extend(diagnosis, new_diagnoses)
    call extend(diagnosis, {'COBOL':'Dementia', 'Forth':'Dyslexia'})

    " remove a single entry
    let removed_value = remove(dict, "key")
    unlet dict["key"]

## Batch-processing of dictionaries

### function filter()
When removing multiple entries from a dictionary, it is cleaner and more efficient to use filter().
The filter() function works much the same way as for lists, except that in addition to testing each entry's value using v:val, you can also test its key using v:key. For example:

    " Remove any entry whose key starts with C...
    call filter(diagnosis, 'v:key[0] != "C"')

    " Remove any entry whose value doesn't contain 'Savant'...
    call filter(diagnosis, 'v:val =~ "Savant"')

    " Remove any entry whose value is the same as its key...
    call filter(diagnosis, 'v:key != v:val')


### function map

The map() built-in is particularly handy for normalizing the data in a dictionary.
For example, given a dictionary containing the preferred names of users (perhaps indexed by userids), you could ensure that each name was correctly capitalized, like so:

    call map( names, 'toupper(v:val[0]) . tolower(v:val[1:])' )

The call to map() walks through each value, aliases it to v:val, evaluates the expression in the string, and replaces the value with the result of that expression.
In this example, it converts the first character of the name to uppercase, and the remaining characters to lowercase, and then uses that modified string as the new name value.

## Other dictionary-related functions
In addition to filter(), dictionaries can use several other of the same built-in functions and procedures as lists. In almost every case (the notable exception being string()), a list function applied to a dictionary behaves as if the function had been passed a list of the values of the dictionary. Listing 3 shows the most commonly used functions.

    let is_empty = empty(dict)           " True if no entries at all

    let entry_count = len(dict)          " How many entries?

    let occurrences = count(dict, str)   " How many values are equal to str?

    let greatest = max(dict)             " Find largest value of any entry
    let least    = min(dict)             " Find smallest value of any entry

    call map(dict, value_transform_str)  " Transform values by eval'ing string

    echo string(dict)                    " Print dictionary as key/value pairs


## Deploying dictionaries for cleaner code

### Passing optional arguments in a dictionary

    function! CommentBlock(comment, opt)
        " Unpack optional arguments...
        let introducer = get(a:opt, 'intro', '//'                 )
        let box_char   = get(a:opt, 'box',   '*'                  )
        let width      = get(a:opt, 'width', strlen(a:comment) + 2)" Build the comment box and put the comment inside it...
        return introducer . repeat(box_char,width) . "\<CR>"
        \    . introducer . " " . a:comment        . "\<CR>"
        \    . introducer . repeat(box_char,width) . "\<CR>"
    endfunction

