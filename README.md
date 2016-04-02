Tenuki is a JavaScript implementation of the game of go/baduk/weiqi with full support for HTML rendering out of the box.

<img src="https://raw.githubusercontent.com/aprescott/tenuki.js/master/examples/screenshots/board.png" width="151" height="150">

Features:

  * Ko rule.
  * Pass.
  * Undo.
  * End-game detection: dead stone marking, area/territory scoring.

The go board provides a JavaScript interface to perform various functions, but the UI for individual controls is left to you.

# Installation

* With bower: `bower install tenuki`
* With npm: `npm install tenuki`
* Download the `zip` or `tar.gz` file for a specific version from [the releases page](https://github.com/aprescott/tenuki.js/releases), then use `build/` however you want.

You can also clone this repo and get the `build/` files that way.

# Examples

For live examples, see `examples/`, or view them on GitHub:

* [`example.html`](https://aprescott.github.io/tenuki.js/examples/example.html) — Just the board.
* [`example_with_simple_controls.html`](https://aprescott.github.io/tenuki.js/examples/example_with_simple_controls.html) — Board with an example of simple custom controls and updating game info.

# Simple usage

Create a new `tenuki.Game` instance with a DOM element, then call `setup()`, which displays the board itself and configures click handlers on each intersection:

```html
<link rel="stylesheet" href="build/tenuki.css">
<script src="build/tenuki.js"></script>

<div class="tenuki-board"></div>

<script>
  var boardElement = document.querySelector(".tenuki-board");
  var game = new tenuki.Game(boardElement);
  game.setup();
</script>
```

There are no other dependencies.

For a textured board, add the class `tenuki-board-textured`:

```html
<div class="tenuki-board tenuki-board-textured"></div>
```

<img src="https://raw.githubusercontent.com/aprescott/tenuki.js/master/examples/screenshots/board-textured.png" width="151" height="150">

# Other board sizes

You can pass a second argument to `new tenuki.Game` to specify the board size. If no size is given, the default of 19 is used. All sizes between 1x1 and 19x19 should work. Sizes above 19x19 will error and won't render.

```js
// use a 13x13 board
new tenuki.Game(boardElement, 13);
```

# Browser support

I've tested this on Chrome, Firefox, Safari and Opera.

# Known problems

Because the browser is rendered with pure CSS and no images, there are some pixel rounding issues when the browser's zoom level is not 100%. This can create positioning/alignment problems, for instance, at 110%, because widths and lines on the board are not consistent with each other.

# Usage outside of a browser

The full browser environment is not required in order to use the representation of the game in JavaScript. For example, if you have a node app, you can simply create a new game without passing an element:

```js
var Game = require("tenuki").Game;
game = new Game();
// game.boardSize = 13;
game.setup();
```

From there, the JavaScript interface is the same as in a browser console:

```js
game.intersectionAt(0, 0).value;
// 'empty'
game.currentPlayer;
// 'black'
game.isOver();
// false
game.playAt(0, 0);
// true
game.intersectionAt(0, 0).value;
// 'black'
```

# Game play functions

The following functions are available on a `Game` object, and can be used to control the gameplay.

Note that all functions which take two integer coordinates (`y` and `x`) are measured from the top of the board and left of the board. So `y = 0` is the top-most row, and `x = 0` is the left-most row. On a 19x19 board, the top left star point (4-4) is thus at `y = 3` and `x = 3`.

* `pass()`: passes for the current player.
* `playAt(y, x)`: attempts to play a stone at `(y, x)` for the current player. If the move is illegal (because of ko, suicide, etc.), then nothing will happen. Returns `true` if the move is successful, otherwise `false`.
* `isOver()`: returns `true` if the most recent 2 moves were passes, indicating the game is over, otherwise `false`.
* `toggleDeadAt(y, x)`: sets the group of stones at `(y, x)` to be dead as part of marking territory. Only useful if `isOver()` is `true`.
* `territoryScore()` and `areaScore()`: return an object containing score information, e.g., `{ black: 150, white: 130 }`. Only useful if `isOver()` is `true`, since proper scoring requires dead stone marking at the end of the game.
* `undo()`: undo the most recent move.

# Post-render callbacks

There is a configurable callback, `postRender`, which is fired each time the board is rendered, e.g., after every move.

This is useful if you want to update some other state:

```js
var game = new tenuki.Game(boardElement);
game.setup();

game.callbacks.postRender = function(game) {
  if (game.currentMove().pass) {
    console.log(game.currentMove().color + " passed");
  } else {
    console.log(game.currentMove().color + " played " + game.currentMove().y + "," + game.currentMove().x);
  }
};
```

# Running tests

Open [`test.html`](https://aprescott.github.io/tenuki.js/test.html) in your browser. If the tests all pass, you'll see "PASS" shown with black stones.

# Developing

You'll need [`browserify`](http://browserify.org/). You can install it with [`npm`](https://www.npmjs.com/) by running `npm install -g browserify`.

To make changes, update individual files in `lib/` and `css/`. Then, run `./build.sh` to generate `build/js/tenuki.js` and `build/css/tenuki.css`, suitable for use in a browser.

To test changes, use `test.html` and the files in `examples/` to check that things still work.