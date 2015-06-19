title: Javascript Unit test with Jasmine + Karma
date: 2015-06-18 19:23:58
tags:
---
# Javascript Unit test with Jasmine + Karma


## Start with a simple example

### Jasmine
please refer to its [official site] (http://jasmine.github.io/) if you are not familiar with Jasmine framework.

Suppose we have a javascript function to be tested

```javascript
function validateConfirmPassword(password, confirmPassword) {
    password = $.trim(password);
    confirmPassword = $.trim(confirmPassword);
    return password === confirmPassword;
}
```

The testing code is as following


```javascript
describe('Sample Test', function () {
    it("enter confirm password same as password", function () {
        result = ValidateConfirmPassword("123456", "123456");
        expect(result).toEqual(true);
    });
}
```

### Karma

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

Now that you have your configuration all that is left to do is to start Karma: 
```
karma start
```
You are supposed to see some output in your command prompt and a browser is popup
![karma browser image](C:\Users\chenchao\Desktop\karma.jpg "karma browser"). 
If you want to run tests manually (without auto watching file changes), you can: 
```
karma run
```
But only if you have started the Karma server before.




