# React ePub reader #

An ePub-reader for react powered by EpubJS #react #epubjs #webpack #babel #standardjs

[See demo](http://gerhardsletten.github.io/react-reader/)

![React Reader logo](https://s3-eu-west-1.amazonaws.com/react-reader/react-reader.svg)

## React wrapper for epubjs ##

React reader is an react-wrapper for [epub.js](https://github.com/futurepress/epub.js), using the v.03 branch. We are currently using a [custom branch of epub.js#v3](https://github.com/gerhardsletten/epub.js) to ensure that breaking changes is avoided. Its published on to [@gerhardsletten/epubjs](https://www.npmjs.com/package/@gerhardsletten/epubjs)

## About ##

[epub.js](https://github.com/futurepress/epub.js) is a great library, and this is a wrapper for this library, making it easier to use in a React-app.

```js
import {
  EpubView, // Underlaying epub-canvas (wrapper for epub.js iframe)
  EpubViewStyle, // Styles for EpubView, you can pass it to the instance as a style prop for customize it
  ReactReader, // A simple epub-reader with left/right button and chapter navigation
  ReactReaderStyle // Styles for the epub-reader it you need to customize it
} from 'react-reader'
```

## Usage ##

`npm install react-reader --save`

And in your react-component...

```js
import React, {Component} from 'react'
import {ReactReader} from 'react-reader'

class App extends Component {
  render () {
    return (
      <div style={{position: 'relative', height: '100%'}}> // * Container needs a height..
        <ReactReader 
          url={'/alice.epub'} 
          title={'Alice in wonderland'}
          location={'epubcfi(/6/2[cover]!/6)'}
          locationChanged={(epubcifi) => console.log(epubcifi)}
          getRendition={this.getRendition}
        />
      </div>
    )
  }
  onRenditionSelection = (cfiRange, contents) => {
    console.log('Selection was created', cfiRange, contents)
    contents.mark(cfiRange, {}, (e) => {
      console.log('You clicked the selection')
    })
    contents.highlight(cfiRange)
    contents.window.getSelection().removeAllRanges()
  }
  getRendition = (rendition) => {
    // Get access to core events from epubjs
    rendition.on('selected', this.onRenditionSelection)
    // Add custom styles
    rendition.themes.default({
      '::selection': {
        'background': 'rgba(255,255,0, 0.3)'
      }
    })
}
```

See `demo/App.js` for an example of using the selection api in epubjs.

#### ReactReader props ####

* `url` [string, required] - url to the epub-file, if its on another domain, remember to add cors for the file
* `title` [string] - the title of the book, displayed above the reading-canvas
* `loadingView` [element] - if you want to customize the loadingView
* `showToc` [bool] - wheather to show the toc / toc-nav
* `location` [string, number] - set / update location of the epub
* `locationChange` [func] - a function that recives the current location while user is reading
* `tocChange` [func] - when the the reader has parsed the book you will recive an array of the chapters
* `styles` [object] - override the default styles
* `epubOptions` [object] - pass custom properties to the epub rendition
* `getRendition` [func] - when epubjs has rendered the epub-file you can get access to the epubjs-rendition object here
* `swipeable` [bool, default false] - enable swiping left/right with [react-swipeable](https://github.com/dogfessional/react-swipeable). *Warning* this will disable interacting with epub.js iframe content like selection


*Container needs a height..*
The ReactReader will expand to 100% of width/height, so be sure to set a height on the parent element, either with position it absolute of window, set height or use paddingTop for proporsjonal scaling.

### Optional use the underlaying EpubView ###

This is just the plain epub canvas, you will then need to implement the reader stuff like chapter (toc) navigation and next/prev buttons. Take a look at the implemention in ReactReader.js

```js
import React, {Component} from 'react'
import {EpubView} from 'react-reader'

const epubOptions = {
  disableSrcdoc: true // If you use react-reader within cordova you need to set this because cordova webview will silently disable srcdoc for iframes, see https://issues.apache.org/jira/browse/CB-7379
}

class App extends Component {
  render () {
    return (
      /* The ReactReader will expand to 100% of width/height, so be sure to set a height on the parent element, either with position it absolute of window, set height or use paddingTop for proporsjonal scaling */
      <div style={{position: 'relative', height: '100%'}}>
        <EpubView 
          url={'/alice.epub'} 
          location={'epubcfi(/6/2[cover]!/6)'}
          locationChanged={(epubcifi) => console.log(epubcifi)}
          tocChanged={(toc) => console.log(toc)}
          epubOptions={epubOptions}
        />
      </div>
    )
  }
}
```

#### EpubView props ####

* `url` [string, required] - url to the epub-file, if its on another domain, remember to add cors for the file
* `loadingView` [element] - if you want to customize the loadingView
* `location` [string, number] - set / update location of the epub
* `locationChange` [func] - a function that recives the current location while user is reading
* `tocChange` [func] - when the the reader has parsed the book you will recive an array of the chapters
* `styles` [object] - override the default styles
* `epubOptions` [object] - pass custom properties to the epub rendition
* `getRendition` [func] - when epubjs has rendered the epub-file you can get access to the epubjs-rendition object here

[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat)](https://github.com/feross/standard)
