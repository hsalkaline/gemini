# Changelog

## Dev

* Remove built-in TeamCity reporter. If you really want
it, you can adapt 0.7.x reporter to the current version
of `gemini` and publish it in separate package.
* Add experimental [programmatic API](doc/programmatic-api.md).
* Add `--grep` option to `gather` and `test` commands, which
  allows to execute only suites, matching the pattern (@scf2k).

## 0.7.0 - 2014-09-15

* Show meaningful error when capture area origin
  does not fit to full-page screenshot. This change
  can break some of your tests (@scf2k).
* Add experimental CSS-coverage report. Enable
  with `--coverage` CLI flag or `coverage: true` in
  config (@scf2k).
* Add ability to override `gridUrl`,`rootUrl`, `screenshotsDir`,
  `debug` config options by their respective CLI options or
  environment variables (@arikon).
* Add config option `windowSize` to specify default
  size of the browser windows (@scf2k).
* Add action `setWindowSize` to specify browser
  window size during the tests (@scf2k).


## 0.6.5 - 2014-09-12

Was republished as 0.7.0 due to a breaking change.

## 0.6.4 - 2014-08-13

* Add command line completion (@unlok).
* Show an error, when config file has unknown
  options.
* Add `focus(element)` action (@arikon).

## 0.6.3 - 2014-08-05

* Allow to use empty string with `sendKeys`. It can be used
  to focus on an element without changing states.

## 0.6.2 - 2014-08-01

* Reset mouse position for each suite. Previously, cursor
  may stay at the position left from a previous suite and
  some elements was captured with hover effect when there
  shouldn't be any.

## 0.6.1 - 2014-07-30

* Fix incorrect capture region rounding, causing bottom row
  of the element to be cropped sometimes.
  

## 0.6.0 - 2014-07-28

* `:before` and `:after` pseudo-elements outline and shadow are now
  taken into account when calculating capture region(@incorp).

  This change can break some of your tests. Re-gather reference images
  to fix the problem.
* Added `tolerance` config option which can be used to specify maximum
  error rate before images will be treated as unequal.
  Default tolerance to 0. This is stricter then previous versions:
  now every, even slightest difference between reference and current
  images will fail tests.

  Set `tolerance` to 0.001 in `.gemini.yml` to restore 0.5.x behavior.
* Coordinates of capture region are now rounded to capture maximal area.
  Previously, border pixels could be cropped, due to rounding error.
  This change can break some of your tests. Re-gather reference images
  to fix the problem.

* Added ability to change diff highlight color in config file.
* Change diff highlight style 

* Global installations of `gemini` now runs local one, if available.

## 0.5.0 - 2014-07-17

* Browsers are now launched once for each run (previously, they were
  launched once per suite). This greatly reduces total tests run time,
  but can break some of your code, i.e. each `mouseDown` should always
  be closed by `mouseUp`.
  Previously, this was not required if `mouseDown` was used once for
  suite.
  It will show warning if versions of the modules does not match.
* `flat` reporter replaces `tree`. Tree reporter can not work with new
  browser launch model.
* Add `parallelLimit` option that allows to limit number of browsers
 run in parallel.
* Add `suite.after()` which can be used to perform some action after all
  of the states without taking screenshot.
* Unknown errors, returned by Selenium have more detailed report.
* Fix `--version` option.

## 0.4.2 - 2014-06-25

* Fix missing images in html report.

## 0.4.1 - 2014-06-19

* Correctly detect crop region in Firefox

## 0.4.0 - 2014-06-18

* Crop region for screenshots is calculated via client script inside
  browser instead of `gemini`. This allows to issue fewer requests to
  Selenium Server speeding up total tests run time. This feature breaks
  compatibility with old browsers (`IE` < 9).
* New config format, which allows to specify full set of capabilities
  for browsers:

  ```yaml
  browsers:
    phantomjs: phantomjs
    opera12:
        browserName: opera
        version: '12.06'
        platform: 'WINDOWS'
    firefox28:
        browserName: firefox
        version: '28.0'
    firefox27:
        browserName: firefox
        version: '27.0'
  ```
* Correctly capture screenshots of regions out of initial browser
  viewport in  browsers, that can't capture full page (`Opera` and
  `Chrome` at the time of writing).
* `outline-width` of an elements is now also taken into account when 
  calculating crop region.
* Add `debug` options to config file. If set to `true`, `gemini` will
  print debug logs to STDOUT. (@arikon).
* Add `http` section to config file which allows to configure HTTP
  timeout (`http.timeout`) retry count (`http.retries`) and delay
  between retries (`http.retryDelay`). (@arikon).
* Asynchronous errors stacktraces in browser actions (such as not found
  element) will point to users code.
* More HTTP requests to Selenium will run in parallel speeding up
  `gather`/`test` commands (@arikon).
* If `gemini` is run without subcommand, help text will be shown.

## 0.3.4 - 2014-05-28

* Enhanced html report:
  - suites are now collapsible;
  - all but failed suites are collapsed by default;
  - buttons to expand all, collapse all and expand only errors are added;
  - stats of total numbers of tests run, failed, succeeded and skipped
    are shown at the top.

## 0.3.3 - 2014-05-19

* Allow to use multiple reporters in `test` command.
* Throw error when creating multiple suites of the same name within
  the same parent.
* Throw error when creating multiple states of the same name within
  the suite.
* Throw error when creating suite that will be unable to run (
  has states and hasn't url or capture region);
* Check argument types of `SuiteBuilder` methods.
* Check argument types of all actions methods.
* Shorter stacktraces for invalid elements  errors.
* Correctly handle offsets in `mouseMove` actions.
* Fix error when `gridUrl` was required even if there is only
  `phantomjs` browser.

## 0.3.2 - 2014-05-15

* Allow to override `gridUrl` and `rootUrl` settings with cli
  options `--grid-url` and `--root-url`.
* Correctly report error, when wrong argument passed to an action.

## 0.3.1 - 2014-05-13

* Ability to set additional capabilities for all browsers, using
  `capabilities` option in `.gemini.yml`:

  ```yaml
  capabilities:
    option1: value,
    option2: value
  ```
* Non-existent directories, passed to `gather` and `test` commands will
  be filtered out
* If fatal error occurs, `gemini` will always exit with 1 status code
* If test fails or state error occurs, `gemini` will always exit with 2
  status code.
* When `gemini` is unable to launch browser, more clearer error message
  will be displayed.

## 0.3.0 -  2014-04-30

* Elements to take screen shots of and elements to perform action
  on are now defined differently. `setElements` and `setDynmaicElements`
  methods removed.

  New way to define elements for screenshot:

  ```javascript
  suite.setCaptureElements('.selector1', '.selector2', ...)
  ```

  Or using array:

  ```javascript
  suite.setCaptureElements(['.selector1', '.selector2', ...])
  ```


  To get element to perform action on, you can now pass
  selectors directly to the actions:

  ```javascript
  suite.capture('state', function(actions, find) {
      actions.click('.button');
  });
  ```

  To avoid multiple lookups for the same element you can use 
  `find` function which is now passed to the state callback:

  ```javascript
  suite.capture('state', function(actions, find) {
      var button = find('.button');
      actions.mouseMove(button);
             .click(button);
  });
  ```

* Add `suite.before(function(action, find))` which can be used to
  perform some actions before the first state. Context is shared
  between before hook and all of suite's state callbacks.

  You can use `before` to look for element only once for the state:

  ```javascript
  suite.before(function(actions, find) {
      this.button = find('.buttons');
  })
  .capture('hovered', function(actions, find) {
      actions.mouseMove(this.button);
  })
  .capture('pressed', function(actions, find) {
      actions.mouseDown(this.button);
  });
  ```

  Or to perform some actions before first state without taking
  screenshot:

  ```javascript
  suite.before(function(actions, find) {
      actions.click('.button');
  });
  ```

* Add `suite.skip()` method which allows to skip suite in some set of
  browsers:

  - `suite.skip()` - skip in all browsers.
  - `suite.skip('chrome')` - skip in all versions of Chrome.
  - `suite.skip({name: 'chrome', version: '33.0'})` - skip in Chrome 33.0
  - `suite.skip(['chrome', 'opera'])` - skip in Chrome and Opera

* Public API now has constants for special keys to use in `sendKeys` actions
  (i.e. `gemini.CONTROL` for `CTRL` key).

## 0.2.1 - 2014-04-23

* Fix a bug with incorrect reference to the suite in states. Because of
  this bug dynamic elements was not updated properly.

## 0.2.0 - 2014-04-22

* New test suites API.  Plans are replaced by test suites defined by explicit call.

  Old version:

  ```javascript
  module.exports = function(plan) {
      plan.setName('some name')
          .setElements(...)
          .setDynamicElements(...)
          .capture(...)
          
  };
  ```

  New API:

  ```javascript
  var gemini = require('gemini');

  gemini.suite('some name', function(suite) {
      suite.setElements(...)
          .setDynamicElements(...)
          .capture(...)
          
  };

  ```

  Suites also can be nested. In this case, child suite inherits all properties 
  from the parent.

  ```javascript
  gemin.suite('parent', function(suite) {
      gemini.suite('child', function(child) {
          gemini.suite('grandchild', function(grandchild) {

          });
      });
  });
  ```
* `.reload()` method is removed. Use nested suite if you need to reload browser.

* Added action to run any JS code in browser context:

  ```javascript
  actions.executeJS(function(window) {
      window.alert('Hello!');
  });
  ```

* `sendKeys` action can optionally take an receive an element to send keys to.

  ```javascript
  actions.sendKeys(elements.someInput, 'hello');
  ```
* Added ability to specify browser version in `.gemini.yml`

  ```yaml
  browsers:
    - {name: 'phantomjs', version: '1.9'}
  ```
* Tree reporter is now used for `gather` command.

## 0.1.1 - 2014-04-08

* `phantomjs` always starts maximized. This fixes the error, when some shadows didn't fit in crop rectangle.
* Action on dynamic element that does not currently exists causes non-fatal error. Such error will fail only one state, the rest will continue running.
* `gather` command now reports browser name.
* browsers are always closed, even if there was an error.

## 0.1.0 - 2014-03-27

* Initial release
