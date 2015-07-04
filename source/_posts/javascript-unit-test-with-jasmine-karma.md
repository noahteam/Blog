title: Simple example of Javascript unit test by Jasmine + Karma
author: chen chao
tags: [javascript, unit test, jasmine, karma]
---

#About

Jasmine is an excellent framework for Javascript unit test. However, there are many test runners avaiable. This post guides to setup karma test runner environment. 

# Example

## Jasmine
please refer to its [official site] (http://jasmine.github.io/) if you are not familiar with Jasmine framework.

Suppose we have a javascript function to be tested in file 'example.src.js'

```javascript
function validateConfirmPassword(password, confirmPassword) {
    password = $.trim(password);
    confirmPassword = $.trim(confirmPassword);
    return password === confirmPassword;
}
```
The testing code 'example.spec.js' is as following 

```javascript
describe('Sample Test', function () {
    it("enter confirm password same as password", function () {
        result = ValidateConfirmPassword("123456", "123456");
        expect(result).toEqual(true);
    });
});
```

## Karma

Karma is a test runner which has similiar functions provided by resharper. It is running on [node.js](https://nodejs.org/) and available as a node module via [NPM] (https://www.npmjs.com/).

Here is a [guide](http://karma-runner.github.io/0.8/intro/installation.html) to install Karma. You are required to type following commands : 
```
npm install karma
```
In order for karma to start, we need to do create configuration file for karma. To create configuration file, simply typing 
```
karma init
```
This will ask you many questions and if you answered them all correct you will be allowed to use Karma. For more information on the configuration options see [Configuration File Overview](https://github.com/karma-runner/karma). 
```
$>karma init

Which testing framework do you want to use ?
Press tab to list possible options. Enter to move to the next question.
> jasmine

Do you want to use Require.js ?
This will add Require.js plugin.
Press tab to list possible options. Enter to move to the next question.
> no

Do you want to capture any browsers automatically ?
Press tab to list possible options. Enter empty string to move to the next question.
> Chrome
>

What is the location of your source and test files ?
You can use glob patterns, eg. "js/*.js" or "test/**/*Spec.js".
Enter empty string to move to the next question.
> lib/jquery-1.11.3.js
> *.src.js
> *.spec.js
>

Should any of the files included by the previous patterns be excluded ?
You can use glob patterns, eg. "**/*.swp".
Enter empty string to move to the next question.
>

Do you want Karma to watch all the files and run the tests on change ?
Press tab to list possible options.
> yes


Config file generated at "karma.conf.js".

```
Here is configuration file generated

```
// Karma configuration
// Generated on Fri Jul 03 2015 11:13:31 GMT+0800 (Malay Peninsula Standard Time)

module.exports = function(config) {
  config.set({

    // base path that will be used to resolve all patterns (eg. files, exclude)
    basePath: '',


    // frameworks to use
    // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
    frameworks: ['jasmine'],


    // list of files / patterns to load in the browser
    files: [
        'lib/jquery-1.11.3.js',
        '*.src.js',
        '*.spec.js'
    ],


    // list of files to exclude
    exclude: [
    ],


    // preprocess matching files before serving them to the browser
    // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor
    preprocessors: {
    },


    // test results reporter to use
    // possible values: 'dots', 'progress'
    // available reporters: https://npmjs.org/browse/keyword/karma-reporter
    reporters: ['progress'],


    // web server port
    port: 9876,


    // enable / disable colors in the output (reporters and logs)
    colors: true,


    // level of logging
    // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
    logLevel: config.LOG_INFO,


    // enable / disable watching file and executing tests whenever any file changes
    autoWatch: true,


    // start these browsers
    // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
    browsers: ['Chrome'],


    // Continuous Integration mode
    // if true, Karma captures browsers, runs the tests and exits
    singleRun: false
  });
};

```


Now that you have your configuration all that is left to do is to start Karma: 
```
karma start
```
You are supposed to see some output in your command prompt and a browser is popup
![karma browser image](https://goo.gl/photos/HMUUuxbKHAhRhiJt7 "karma browser"). 

While after karma browser started, it will load all the files specified in configuration. And it will detect jasmine test cases, automatically execute test cases, and shows out the test result in console.
```
$>karma start
INFO [karma]: Karma v0.12.36 server started at http://localhost:9876/
INFO [launcher]: Starting browser Chrome
WARN [web-server]: 404: /favicon.ico
INFO [Chrome 40.0.2214 (Windows 7 0.0.0)]: Connected on socket yGdW4usRG36DmTiLMLKG with id 42325643
Chrome 40.0.2214 (Windows 7 0.0.0): Executed 1 of 1 SUCCESS (0.006 secs / 0.002 secs)
```

## Debug
If you see any error in console output, you could try to click [Debug] button in top right corner. A new tab will be opened. By viewing the source code, you could see any .js file you missed to included. And you also could using [F12] to debug in this seperated browser.







