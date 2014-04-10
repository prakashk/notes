Title: Emacs tips I learned today  
Date: Wed Feb  6 14:20:07 2013  
Tags: Emacs

* Using counters in keyboard macros

`<F3>` - start a macro, or insert counter value when defining a macro  
`<F4>` - end a macro, or call the last defined macro  

`<C-x C-k C-i>` - insert counter value (can be used outside a macro definition too)  
`<C-x C-k C-a>` - increments current counter value by the prefix argument  
`<c-x C-k C-f>` - sets the format of the counter value inserted ("%02d")

* Ediff

`!` - recomputes the diff.
