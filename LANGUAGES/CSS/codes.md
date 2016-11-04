# **CSS**
Some CSS useful codes:

### Align verticaly with pure CSS
```CSS
/* This parent code is just to avoid elements to be blurry due to the element being placed on a “half pixel”. */
.parent-element {
  -webkit-transform-style: preserve-3d;
  -moz-transform-style: preserve-3d;
  transform-style: preserve-3d;
}

.element {
  position: relative;
  top: 50%;
  transform: translateY(-50%);
}
```

OR

```CSS
.element {
  position: relative;
  top: 50%;
  /* perspective(1px) is used to avoid elements to be blurry due to the element being placed on a “half pixel”. */
  transform: perspective(1px) translateY(-50%);
}
```

### References: [#1](http://zerosixthree.se/vertical-align-anything-with-just-3-lines-of-css/)

----------
