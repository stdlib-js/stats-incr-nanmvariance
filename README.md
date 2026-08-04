<!--

@license Apache-2.0

Copyright (c) 2026 The Stdlib Authors.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

-->


<details>
  <summary>
    About stdlib...
  </summary>
  <p>We believe in a future in which the web is a preferred environment for numerical computation. To help realize this future, we've built stdlib. stdlib is a standard library, with an emphasis on numerical and scientific computation, written in JavaScript (and C) for execution in browsers and in Node.js.</p>
  <p>The library is fully decomposable, being architected in such a way that you can swap out and mix and match APIs and functionality to cater to your exact preferences and use cases.</p>
  <p>When you use stdlib, you can be absolutely certain that you are using the most thorough, rigorous, well-written, studied, documented, tested, measured, and high-quality code out there.</p>
  <p>To join us in bringing numerical computing to the web, get started by checking us out on <a href="https://github.com/stdlib-js/stdlib">GitHub</a>, and please consider <a href="https://opencollective.com/stdlib">financially supporting stdlib</a>. We greatly appreciate your continued support!</p>
</details>

# incrnanmvariance

[![NPM version][npm-image]][npm-url] [![Build Status][test-image]][test-url] [![Coverage Status][coverage-image]][coverage-url] <!-- [![dependencies][dependencies-image]][dependencies-url] -->

> Compute a moving [unbiased sample variance][sample-variance] incrementally, ignoring `NaN` values.

<section class="intro">

For a window of size `W`, the [unbiased sample variance][sample-variance] is defined as

<!-- <equation class="equation" label="eq:unbiased_sample_variance" align="center" raw="s^2 = \frac{1}{W-1} \sum_{i=0}^{W-1} ( x_i - \bar{x} )^2" alt="Equation for the unbiased sample variance."> -->

```math
s^2 = \frac{1}{W-1} \sum_{i=0}^{W-1} ( x_i - \bar{x} )^2
```

<!-- <div class="equation" align="center" data-raw-text="s^2 = \frac{1}{W-1} \sum_{i=0}^{W-1} ( x_i - \bar{x} )^2" data-equation="eq:unbiased_sample_variance">
    <img src="https://cdn.jsdelivr.net/gh/stdlib-js/stdlib@49d8cabda84033d55d7b8069f19ee3dd8b8d1496/lib/node_modules/@stdlib/stats/incr/mvariance/docs/img/equation_unbiased_sample_variance.svg" alt="Equation for the unbiased sample variance.">
    <br>
</div> -->

<!-- </equation> -->

</section>

<!-- /.intro -->



<section class="usage">

## Usage

To use in Observable,

```javascript
incrnanmvariance = require( 'https://cdn.jsdelivr.net/gh/stdlib-js/stats-incr-nanmvariance@umd/browser.js' )
```

To vendor stdlib functionality and avoid installing dependency trees for Node.js, you can use the UMD server build:

```javascript
var incrnanmvariance = require( 'path/to/vendor/umd/stats-incr-nanmvariance/index.js' )
```

To include the bundle in a webpage,

```html
<script type="text/javascript" src="https://cdn.jsdelivr.net/gh/stdlib-js/stats-incr-nanmvariance@umd/browser.js"></script>
```

If no recognized module system is present, access bundle contents via the global scope:

```html
<script type="text/javascript">
(function () {
    window.incrnanmvariance;
})();
</script>
```

#### incrnanmvariance( window\[, mean] )

Returns an accumulator `function` which incrementally computes a moving [unbiased sample variance][sample-variance], ignoring `NaN` values. The `window` parameter defines the number of values over which to compute the moving [unbiased sample variance][sample-variance].

```javascript
var accumulator = incrnanmvariance( 3 );
```

If the mean is already known, provide a `mean` argument.

```javascript
var accumulator = incrnanmvariance( 3, 5.0 );
```

#### accumulator( \[x] )

If provided an input value `x`, the accumulator function returns an updated [unbiased sample variance][sample-variance]. If not provided an input value `x`, the accumulator function returns the current [unbiased sample variance][sample-variance]. If provided `NaN`, the accumulator function ignores the value and returns the current [unbiased sample variance][sample-variance] without updating the window.

```javascript
var accumulator = incrnanmvariance( 3 );

var s2 = accumulator();
// returns null

// Fill the window...
s2 = accumulator( 2.0 ); // [2.0]
// returns 0.0

s2 = accumulator( 1.0 ); // [2.0, 1.0]
// returns 0.5

s2 = accumulator( NaN ); // [2.0, 1.0]
// returns 0.5

s2 = accumulator( 3.0 ); // [2.0, 1.0, 3.0]
// returns 1.0

// Window begins sliding...
s2 = accumulator( -7.0 ); // [1.0, 3.0, -7.0]
// returns 28.0

s2 = accumulator( -5.0 ); // [3.0, -7.0, -5.0]
// returns 28.0

s2 = accumulator();
// returns 28.0
```

</section>

<!-- /.usage -->

<section class="notes">

## Notes

-   Input values are **not** type checked. If provided `NaN`, the value is ignored and the accumulator function returns the current [unbiased sample variance][sample-variance] without updating the window. If non-numeric inputs are possible, you are advised to type check and handle accordingly **before** passing the value to the accumulator function.
-   As `W` values are needed to fill the window buffer, the first `W-1` returned values are calculated from smaller sample sizes. Until the window is full, each returned value is calculated from all provided values (excluding `NaN` values).

</section>

<!-- /.notes -->

<section class="examples">

## Examples

<!-- eslint no-undef: "error" -->

```html
<!DOCTYPE html>
<html lang="en">
<body>
<script type="text/javascript" src="https://cdn.jsdelivr.net/gh/stdlib-js/random-base-uniform@umd/browser.js"></script>
<script type="text/javascript" src="https://cdn.jsdelivr.net/gh/stdlib-js/random-base-bernoulli@umd/browser.js"></script>
<script type="text/javascript" src="https://cdn.jsdelivr.net/gh/stdlib-js/stats-incr-nanmvariance@umd/browser.js"></script>
<script type="text/javascript">
(function () {

var accumulator;
var v;
var i;

// Initialize an accumulator:
accumulator = incrnanmvariance( 5 );

// For each simulated datum, update the moving unbiased sample variance...
for ( i = 0; i < 100; i++ ) {
    v = ( bernoulli( 0.8 ) < 1 ) ? NaN : uniform( 0.0, 100.0 );
    accumulator( v );
}
console.log( accumulator() );

})();
</script>
</body>
</html>
```

</section>

<!-- /.examples -->

<!-- Section for related `stdlib` packages. Do not manually edit this section, as it is automatically populated. -->

<section class="related">

</section>

<!-- /.related -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->


<section class="main-repo" >

* * *

## Notice

This package is part of [stdlib][stdlib], a standard library for JavaScript and Node.js, with an emphasis on numerical and scientific computing. The library provides a collection of robust, high performance libraries for mathematics, statistics, streams, utilities, and more.

For more information on the project, filing bug reports and feature requests, and guidance on how to develop [stdlib][stdlib], see the main project [repository][stdlib].

#### Community

[![Chat][chat-image]][chat-url]

---

## License

See [LICENSE][stdlib-license].


## Copyright

Copyright &copy; 2016-2026. The Stdlib [Authors][stdlib-authors].

</section>

<!-- /.stdlib -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="links">

[npm-image]: http://img.shields.io/npm/v/@stdlib/stats-incr-nanmvariance.svg
[npm-url]: https://npmjs.org/package/@stdlib/stats-incr-nanmvariance

[test-image]: https://github.com/stdlib-js/stats-incr-nanmvariance/actions/workflows/test.yml/badge.svg?branch=main
[test-url]: https://github.com/stdlib-js/stats-incr-nanmvariance/actions/workflows/test.yml?query=branch:main

[coverage-image]: https://img.shields.io/codecov/c/github/stdlib-js/stats-incr-nanmvariance/main.svg
[coverage-url]: https://codecov.io/github/stdlib-js/stats-incr-nanmvariance?branch=main

<!--

[dependencies-image]: https://img.shields.io/david/stdlib-js/stats-incr-nanmvariance.svg
[dependencies-url]: https://david-dm.org/stdlib-js/stats-incr-nanmvariance/main

-->

[chat-image]: https://img.shields.io/badge/zulip-join_chat-brightgreen.svg
[chat-url]: https://stdlib.zulipchat.com

[stdlib]: https://github.com/stdlib-js/stdlib

[stdlib-authors]: https://github.com/stdlib-js/stdlib/graphs/contributors

[umd]: https://github.com/umdjs/umd
[es-module]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules

[deno-url]: https://github.com/stdlib-js/stats-incr-nanmvariance/tree/deno
[deno-readme]: https://github.com/stdlib-js/stats-incr-nanmvariance/blob/deno/README.md
[umd-url]: https://github.com/stdlib-js/stats-incr-nanmvariance/tree/umd
[umd-readme]: https://github.com/stdlib-js/stats-incr-nanmvariance/blob/umd/README.md
[esm-url]: https://github.com/stdlib-js/stats-incr-nanmvariance/tree/esm
[esm-readme]: https://github.com/stdlib-js/stats-incr-nanmvariance/blob/esm/README.md
[branches-url]: https://github.com/stdlib-js/stats-incr-nanmvariance/blob/main/branches.md

[stdlib-license]: https://raw.githubusercontent.com/stdlib-js/stats-incr-nanmvariance/main/LICENSE

[sample-variance]: https://en.wikipedia.org/wiki/Variance

<!-- <related-links> -->

<!-- </related-links> -->

</section>

<!-- /.links -->
