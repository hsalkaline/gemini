# Programmatic API (Experimental)

You can use `gemini` programmatically in your scripts or build tools plugins.
To do so you'll need to require `gemini/api` module.

First step is to create `gemini` instance with a config:

```javascript
var Gemini = require('gemini/api');

var gemini = new Gemini({
    projectRoot: '/path/to/project',
    gridUrl: 'http://example.com/grid'
    ...
});
```

* `new Gemini(filePath)` will load config from YAML file at given paths;
* `new Gemini(filePath, overrides)` will load config from YAML file and override its
properties with values, specified in `overrides`;
* `new Gemini(options)` will create config from specified options (see config file reference).
Options **must** have `projectRoot` setting specified.

## Accessing the config options

You can get values of an options via `gemini.config` property:

```javascript
var Gemini = require('gemini/api'),
    gemini = new Gemini('/path/to/config');

console.log(gemini.config.rootUrl);

```

## Reading the tests

* `gemini.readTests(paths)` – read all of the tests from specified paths into one single
meta-suite. `paths` is an array of files or directories paths containing `gemini` tests.
If not specified, will look for tests in `$projectRoot/gemini` directory.
Returns promise which resolves to single `Suite` object. All top level suites will 
be the children of this root suite.

Here is the example that prints all top level suite names:

```javascript
var Gemini = require('gemini/api'),
    gemini = new Gemini('/path/to/config');

gemini.readTests()
    .done(function(root) {
        root.chidlren.forEach(function(suite) {
            console.log(suite.name);
        });
    });
```

### Suite

Suite objects have the following properties:

* `id` – unique numeric id of the suite. Automatically generated when loading
  suites.
* `name` – the name of the suite;
* `children` – array of subsuites of the current suite.
* `state` – array of the `State` objects, defined in suite.

### State

Suite objects have the following properties:

* `name` – the name of the state;

Methods:

* `shouldSkip(browser)` – returns true if this state should be skipped for browser.
Browser is specified in WebDriver capabilities format:

```javascript
state.shouldSkip({browserName: 'firefox', version: '25.0'});
```

## Gathering reference screenshots

Use `gemini.gather(paths, options)` method.

`paths` are the array of file paths or directories to run the suites from.

Options:
* `reporters` – array of reporter to use. Each element can be either string
(to use corresponding built-in reporter) or reporter function (to use custom 
reporter).
* `grep` - regular expression to filter suites to run. By default, all tests
will be executed. If this option is set, only suites with name matching the
pattern will be executed.

Returns promise, that resolve to a stats object with following keys:

* `total` - total number of tests executed.
* `skipped` - number of skipped tests.
* `errored` - number of errored tests.

Rejects promise, if critical error occurred.

## Running tests

Use `gemini.test(paths, options)` method.

`paths` are the array of file paths or directories to run the tests from.

Options:
* `reporters` – array of reporter to use. Each element can be either string
(to use corresponding built-in reporter) or reporter function (to use custom 
reporter).
* `tempDir` - directory to save temporary images (current states) to. By default,
new temp directory will be created.
* `grep` - regular expression to filter suites to run. By default, all tests
will be executed. If this option is set, only suites with name matching the
pattern will be executed.

Returns promise, that resolve to a stats object with following keys:

* `total` - total number of tests executed.
* `skipped` - number of skipped tests.
* `errored` - number of errored tests.
* `passed` - number of passed tests.
* `failed` - number of failed tests.

Rejects promise, if critical error occurred.

## Utilites

* `gemini.getScreenshotPath(suite, stateName, browserId)` – returns path to the reference screenshot
of the specified state for specified browser.
* `buildDiff(referencePath, currentPath, diffPath)` – creates a diff image between `referencePath` and
`currentPath` and stores the result at `diffPath`.
* `getBrowserCapabilites(browserId)` – returns WebDriver capabilities for specified `browserId`.
* `browserIds` – list of all browser ids to use for tests.

