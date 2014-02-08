restyle
=======

[![build status](https://secure.travis-ci.org/WebReflection/restyle.png)](http://travis-ci.org/WebReflection/restyle)


### In A Nutshell
`restyle` is a function able to transform the following:

```javascript
// we are in a browser
// defining some style at runtime
var myStyle = (function(){

  // some function helper
  function getSomeNumber(boundary) {
    return Math.floor(Math.random() * (boundary + 1));
  }

  // something we could reuse all over
  function hex(red, green, blue) {
    return '#'.concat(
      ('0' + red.toString(16)).slice(-2),
      ('0' + green.toString(16)).slice(-2),
      ('0' + blue.toString(16)).slice(-2)
    );
  }

  // the fresh new appended style object wrap
  return restyle({
    body: {
      backgroundColor: hex(100, 60, 25),
      padding: {
        top: 50,
        left: '30%'
      }
    },
    '.component > li': {
      width: window.innerWidth,
      height: getSomeNumber(200)
    },
    '.component > .icon-spinner': {
      animation: {
        name: 'spin',
        duration: '4s'
      }
    },
    '@keyframes spin': {
      from: {
        transform: 'rotate(0deg)'
      },
      to: {
        transform: 'rotate(360deg)'
      }
    }
  });
}());
```

into this runtime appended and generated cross browser CSS style:

```css
body {
  background-color: #643c19;
  padding-top: 50px;
  padding-left: 30%;
}

.component > li {
  width: 1251px;
  height: 182px;
}

.component > .icon-spinner {
  -webkit-animation-name: spin;
  -moz-animation-name: spin;
  -ms-animation-name: spin;
  -o-animation-name: spin;
  animation-name: spin;
  -webkit-animation-duration: 4s;
  -moz-animation-duration: 4s;
  -ms-animation-duration: 4s;
  -o-animation-duration: 4s;
  animation-duration: 4s;
}

@-webkit-keyframes spin {
  from {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }

  to {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@-moz-keyframes spin {
  from {
    -moz-transform: rotate(0deg);
    transform: rotate(0deg);
  }

  to {
    -moz-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@-ms-keyframes spin {
  from {
    -ms-transform: rotate(0deg);
    transform: rotate(0deg);
  }

  to {
    -ms-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@-o-keyframes spin {
  from {
    -o-transform: rotate(0deg);
    transform: rotate(0deg);
  }

  to {
    -o-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@keyframes spin {
  from {
    -webkit-transform: rotate(0deg);
    -moz-transform: rotate(0deg);
    -ms-transform: rotate(0deg);
    -o-transform: rotate(0deg);
    transform: rotate(0deg);
  }

  to {
    -webkit-transform: rotate(360deg);
    -moz-transform: rotate(360deg);
    -ms-transform: rotate(360deg);
    -o-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
```

with the ability to drop all those styles at once:

```javascript
myStyle.remove();
```

### Signature
```javascript

restyle(
  Object        // a JSONish object as spec'd
  [, prefixes]  // optional prefixes
                // as node.js module this is by default an empty array
                //  generating prefixes-less CSS for other pre/post processors
                // in browsers this is by default all vendors prefixes
                //  without bothering much that -webkit-background does not even exist
                //  browsers will simply ignore CSS that is meaningless

  [, document]  // browsers only, eventually a different document from another realm
):Object;

```

### Specifications
The first `Object` parameter in `restyle` signature is spec'd as such:
```

selector                any CSS selector
                        {
                          body: {
                            // ... 
                          },
                          'ul.dat > li:first-child': {
                            // ...
                          }
                        }

property                a property name or a group name
                        {
                          div: {
                            // properties
                            width: 256, // will result in "256px"
                            transform: 'rotate(360deg)',
                            background: 'transparent url(image.png) 0 0'
                          }
                        }
                        camelCase will be translated into camel-case
                        (backgroundImage => background-image)

value                   the property value or a group of properties
                        if int, will be set as 'px' value

group                   key/value properties names/values object
                        {
                          div: {
                            // group
                            background: {
                              color: 'transparent',
                              image: 'url(image.png)',
                              position: '0 0'
                            }
                          }
                        }

special                 keyframes, media queries,
                        anything that starts with @
                        {
                          div: {
                            // as before
                          },
                          // special selectors
                          '@keyframes spin': {
                            // cpecialContent
                          }
                        }

specialContent          everything supported by restyle as CSS
                        {
                          // special selectors
                          '@keyframes spin': {
                            // properties => values or groups
                            '0%':   {transform: 'rotate(0deg)'},
                            '100%': {transform: 'rotate(360deg)'}
                          },
                          '@media all and (color)': {
                            'body': {
                              background: randomRainbow()
                            }
                          }
                        }

```

### Reason & Benefits
Here a list of bullets to support `restyle` idea, grouped by usage.

#### As DOM Runtime

  * all values, groups, and even keys, can be generated at runtime after features detection or states
  * all vendor prefixes are placed automatically, no redundant CSS to write or download
  * all changes are confined in a single style element that can be dropped at any time
  * it can be used to style custom components preserving the overall application size
  * it fits in less than 1KB minzipped

#### As node.js module or Preprocessor

  * compared to Sass, Stylus, Less, and others, there's nothing new to learn: it's basically JSON that transpile to CSS
  * can be used upfront other preprocessors such beautifiers, cross platform transformers, etc.
  * CSS can be exported as generic JS modules, with the ability to include, require, and use any sort of utility able to simplify CSS creation, aggregate objects upfront for unified style, anything else you might think would be useful
  * it's simple, fast and straightforward

#### Compatibility

  * `restyle` is compatible with new browsers but also old as _IE6_ . If in doubt, [check the live test](http://webreflection.github.io/restyle/test/)
  * every node.js is able to use `restyle` too as the Travis passing build on top says ;-)

### Examples
It is possible to test them [directly in this page](http://webreflection.github.io/restyle/) but here few examples.
```javascript
// this example code
restyle({
  'html, body': {
    margin: 0,
    padding: 0,
    width: '100%',
    height: '100%',
    overflow: 'hidden',
    textAlign: 'center',
    fontFamily: 'sans-serif'
  },
  section: {
    margin: 'auto',
    marginTop: 20
  }
}, []);
```

It will generate a style with the following content.

```
html, body {
    margin: 0px;
    padding: 0px;
    width: 100%;
    height: 100%;
    overflow: hidden;
    text-align: center;
    font-family: sans-serif;
}

section {
    margin: auto;
    margin-top: 20px;
}
```

Things become more interesting with more complex CSS and prefixed support:
```javascript
restyle({
  'div > button:first-child': {
    transform: 'rotate(30deg)'
  }
}, ['moz', 'webkit']);
```

will result in

```css
div > button:first-child {
  -webkit-transform: rotate(30deg);
  -moz-transform: rotate(30deg);
  transform: rotate(30deg);
}
```

while this little piece of code:

```javascript
restyle({
  'body > div': {
    animation: {
      name: 'spin',
      duration: '4s'
    }
  },
  '@keyframes spin': {
    from: {
      transform: 'rotate(0deg)'
    },
    to: {
      transform: 'rotate(360deg)'
    }
  }
});
```

will produce the following

```css
body > div{
  -webkit-animation-name:spin;
  -moz-animation-name:spin;
  -ms-animation-name:spin;
  -o-animation-name:spin;
  animation-name:spin;
  -webkit-animation-duration:4s;
  -moz-animation-duration:4s;
  -ms-animation-duration:4s;
  -o-animation-duration:4s;
  animation-duration:4s;
}
@-webkit-keyframes spin{
  from{
    -webkit-transform:rotate(0deg);
    transform:rotate(0deg);
  }
  to{
    -webkit-transform:rotate(360deg);
    transform:rotate(360deg);
  }
}
@-moz-keyframes spin{
  from{
    -moz-transform:rotate(0deg);
    transform:rotate(0deg);
  }
  to{
    -moz-transform:rotate(360deg);
    transform:rotate(360deg);
  }
}
@-ms-keyframes spin{
  from{
    -ms-transform:rotate(0deg);
    transform:rotate(0deg);
  }
  to{
    -ms-transform:rotate(360deg);
    transform:rotate(360deg);
  }
}
@-o-keyframes spin{
  from{
    -o-transform:rotate(0deg);
    transform:rotate(0deg);
  }
  to{
    -o-transform:rotate(360deg);
    transform:rotate(360deg);
  }
}
@keyframes spin{
  from{
    -webkit-transform:rotate(0deg);
    -moz-transform:rotate(0deg);
    -ms-transform:rotate(0deg);
    -o-transform:rotate(0deg);
    transform:rotate(0deg);
  }
  to{
    -webkit-transform:rotate(360deg);
    -moz-transform:rotate(360deg);
    -ms-transform:rotate(360deg);
    -o-transform:rotate(360deg);
    transform:rotate(360deg);
  }
}
```

### What Is NOT
Just to be clear what `restyle` is not responsible or capable of, is to make everything magically works as others might be.

As example, `flex-box` is not fixed, neither is any early implementation of some broken CSS property.
However, you can simply combine a common class fix for flex-box and use `restyle` to add more or simply specify other properties.

Last but not least, you are free to fix things by your own deciding very specific CSS accordingly with the browser if done at runtime or simply trusting other pre-processors if done on the server side.

```javascript
var flexValue = '1 200px',
    orderValue = 2;
restyle({
  '.item': {
    boxFlex: flexValue,
    flex: flexValue,
    boxOrdinalGroup: orderValue,
    flexOrder: orderValue,
    order: orderValue
  }
});
```

### TODO
Just realized that in some case it would be useful to repeat the property with different values so that the following
```javascript
var flexBox = [
  '-webkit-box',
  '-moz-box',
  '-ms-flexbox',
  '-webkit-flex',
  'flex'
];
restyle({
  '.wrapper': {
    display: flexBox
  }
});
```
will be usable to produce, as example:
```css
.wrapper {
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
}
```
If you have any other hint/suggestion please let me know, thanks.