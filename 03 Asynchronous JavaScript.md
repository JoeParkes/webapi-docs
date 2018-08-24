

# Asynchronous Code

This week’s lab tasks will focus on how JavaScript is used to asynchronously get and set data on remote servers, often referred to as AJAX calls.

Note that AJAX technically uses XML as the data format, but we will be using JSON which is easier to parse, and becoming the defacto standard for much data on the web. The principles of asynchronous communication and callback functions are effectively identical for XML and JSON though.

## Contents

This chapter covers a wide number of topics associated with running async code. The first 4 should be considered **essential** and the rest are topics that, whilst not essential to writing NodeJS code, will greatly improve your knowledge of how asyncronous code works and, should you choose to implement these, will lead to much cleaner code.

1. Async callbacks *
2. JSON data *
3. Modules and callbacks *
4. Nested callbacks *
5. Generators
6. Promises
7. Async functions
8. Screen scraping

Any section above marked with an asterix `*` should be considered essential knowledge.

## 1 Async Callbacks

In this first exercise you will be studying the `currency.js` script to learn about two important concepts.

1. How to pass parameters to your program as runtime parameters.
2. How to run code in multiple threads using callbacks.

### 1.1 Runtime Parameters

When a JavaScript program is invoked from the console, the entire invocation string is available through the `process.argv` array, each word being stored in a different array index. This means that index 0 always contains the string `node`.

Study the `currency.js` script carefully. When we run this script we need to pass the currency we want as a _runtime parameter_ like this: `node currency GBP`.

1. If the script is invoked correctly there should be 3 indexes in the `process.argv` array. Index 0 contains the string `node`, index 1 contains the string `currency` and index 2 contains the string `GBP`.
2. If the array is shorter than 3 indexes we throw an error
3. Finally we take the third index and convert it to upper case, storing the resulting string in an immutable variable (constant).

### 1.2 Callbacks

NodeJS is a single-threaded event loop that processes queued events. This means that if you were to execute a long-running task within a single thread then the process would block. To solve this problem, NodeJS  relies on callbacks, which are functions that run after a long-running process has finished. Instead of waiting for the task to finish, the event loop moves on to the next piece of code. When the long-running task has finished, the callback is added to the event loop and run.

Because callbacks are such a fundamental part of NodeJS you need to spend time to make sure you fully understand how they work.

1. The script uses a third-party package called `request`. To install this, make sure your terminal in pointing to the script directory and install it with the `npm` command (Node Package Manager) like this: `npm install request`.
2. Try running the program with a single currency code `node currency GBP`.
3. Because we want to throw exceptions if something unexpected happens, the code needs to be enclosed in a try-catch block.
4. Next the URL is created. This string is known as a [template literal](https://goo.gl/3vznuR) and is enclosed using backticks instead of quotes. This allows variables to be embedded.
5. When the `request.get()` method is called it takes two parameters The url to call and an anonymous function with three parameters, `err`, `res` and `body`. Note the use of the [ECMA arrow function](https://goo.gl/4pRqUs). This function **callback** will be run once the API call has completed, the API call running in its own thread.
6. If the API request fails, the first parameter, `err` will be non-null and will contain an Error object. At this point we simply throw an exception and exit.
7. The `res` parameter contains the entire response sent back from the server, we don't need this in this example.
8. The `body` parameter contains the data returned from the API, this is what we will be using. it is returned as a _string_ so we use `JSON.parse()` to turn it into a JavaScript object.
9. Finally we extract the data we need from the JavaScript object and send it to the console for display. the `JSON.stringify()` function does the opposite of `JSON.parse` in that it turns a JavaScript object into a JSON string. The second parameter can be used to filter the results. The third parameter specifies the indentation to use when formatting.

### 1.3 Test Your Knowledge

Lets improve the currency exchange tool. You will need to refer to the API [documentation](http://fixer.io) as you work through the tasks.

Its often helpful to see the complete response body. This allows you to see precisely what data was returned. Add a line to print out the entire JSON object. This needs to be immediately after the body was parsed as a JavaScript object.
```
console.log(JSON.stringify(json, null, 2))
```

1. At the moment, all the exchange rates are based on the € (EUR) however the API allows this to be changed by adding a second key to the querystring. Read through the documentation and modify the API call to use £ (GBP) as the base currency.
2. Modify the program to take two parameters: the first should be the base currency and the second the currency to convert to.
3. Use the [Number.prototype.toFixed()](https://goo.gl/DU4hvd) to truncate the number to 2 decimal places.
4. Use the Chrome POSTMan plugin to make an API call to convert £ (GBP) to $ (USD). Take a moment to make sense of the structure of the JSON data.
5. Modify the output of the script to display the currency conversion in a sensible format: e.g. `1 GBP = 1.33 USD`.
6. Finally, modify your program so that it throws an error if it doesn't recognise one of the currency codes.

## 2 JSON Data

Most RESTful APIs return their data as a string in JSON format. This format allows primitives, objects and arrays to be converted into a string, passed between systems as text and then converted to the correct JavaScript object at the receiving end.

In this exercise you will learn how to extract information from complex _JSON_ data.

1. Run the script by entering `node addressFinder 'coventry'`, the address you are looking for needs to be enclosed in single quotes. Notice the result (lots of data).
2. Open the `addressFinder.js` file and notice that the script requires at least three parameters. The user will need to enter an address to look up.
3. The third parameter (index 2) contains the address to find.
4. The API call is made, passing the correct parameters and when it is complete the callback code is executed.
5. The `body` parameter string is parsed into a JavaScript Object.
6. This is then converted back into a formatted JSON string and printed to the console.

### 2.1 Test Your Knowledge

In this exercise you will be extracting data from the JSON object and displaying it in the console.

1. Try using a non-sensical address. What data is sent if Google can't resolve the address? Add an if statement to check for this and throw an exception if it is found.
2. If a match is found, the JSON data will contain the longitude and latitude of the location. Extract this data and display it in a human format: `lon: xxx, lat: xxx`.
3. The `address_components` array contains objects describing the full address. Write code to loop through this array and extract the `long_name properties`, printing them to the console.
4. The `bounds` object contains the geo data defining the top-right and bottom-left of a box that contains the location. Write code to calculate the width and height of the box in degrees.

## 3 Modules and Callbacks

Lets recap a little about JavaScript functions. Functions are _first-class objects_ of the type _Object_. This means they can be used just like other objects. You have already seen them stored in other variables.

In the previous examples you passed a function as an argument to another function. By passing a function argument we can execute it when we wish, for example after a network operation to retrieve data. In this context, the function is called a **callback function**.

In this topic you will learn how to create your own functions that take a callback function argument and how to store these in CommonJS modules, importing them where needed. 

Locate the `directions/` directory then open and run the `index.js` script. You will be prompted to enter a start and finish address, the script will return the driving distance between them. Test the exception handling by using both valid and invalid data.

1. The `directions` module is imported.
2. The `getDistance()` function it contains is called:
  1. This takes two string parameters
  2. The third parameter is a callback function
3. The callback function takes two arguments:
  1. The first should always be an error object, this will be `null` if no error occurred.
  2. The second argument is the data returned.
4. Exceptions are handled _inside_ the callback function.
5. The final line in the script executes _before the callback function_
6. The callback function executes once the data has been retrieved, _without blocking the thread_.

Open the `directions.js` file and study it carefully.

1. The `request` module is imported.
2. The `getDistance()` function is exported.
3. The third argument is the _callback function_ which has two arguments, the error and the data. This is the recommended callback argument pattern sequence.
4. The `getDistance()` function makes an aynchronous call to the `request.get()` function.
  - by isolating the API call in its own private function we won't need to duplicate this code when we add more functionality (the DRY principle).
5. Its third parameter is a _callback function_.
6. In the callback function we check for a non-null first parameter which would indicate an error has occurred.
  - If there has been an error we call our callback function and pass an Error object as its first parameter.
  - If no error has occurred we return null for the first parameter and the data as the second one.

### 3.1 Test Your Knowledge

1. When the script runs, the url used in the API call is printed to the console. Copy this into [Chrome Postman](https://goo.gl/Twyycv) to see the entire API response body.
2. Write a second function in your module called `getDuration()` which should print out how long the journey takes (in minutes).
3. Write a third function called directions which returns an array of directions (HINT: `html_instructions`).

## 4 Nested Callbacks

Because the code to be run after a callback is run needs to be _inside_ the callback code it is very challenging to build a script that contains several long-running tasks you get into a situation where you nest callbacks inside callbacks (inside callbacks) which makes the code very difficult to write, debug and read and means its very difficult to split into separate functions, a situation commonly known as **Callback Hell**.

Open the file `nestedCallbacks.js` which asks for a _base_ currency code then prints out all the exchange rates against other currencies. Notice that there are four functions defined, three of which include a callback. Our script is designed to capture user input using `stdin` (needing a callback), identify whether a currency code is valid (requiring a second callback) and then getting the currency conversion rates for the specified currency (requiring a third callback).

1. Notice that the `checkValidCurrencyCode()` function is called by the callback for the `getInput()` function and the `getData()` function is called by the callback for the `checkValidCurrencyCode()` function.
2. Each callback takes two parameters as normal. The first contains the error (if any) and this needs to be handled in each callback.
3. The data from the first callback is needed when calling the third function so needs to be stored in an immutable variable (constant).
4. The fourth, and final, function does not have a callback.

Callbacks are the simplest possible mechanism for asynchronous code in JavaScript. Unfortunately, raw callbacks sacrifice the control flow, exception handling, and function semantics familiar from synchronous code.

### 4.1 Test Your Knowledge

The callbacks are already nested 3 deep. To test your knowledge of deeply nested callbacks you are going to create a script that has 6 levels of nested callbacks!

1. modify the script to ask for the currency to convert to and display only the one conversion rate.
2. instead of printing the exchange rate, ask for the amount to be converted and them return the equivalent in the chosen currency
3. use the [OpenExchangeRates](https://openexchangerates.org/api/currencies.json) API to display the full name of the chosen currency.

Even though the script is still simple you are probably already getting in a tangle! Imagine a more complex script with conditions, it would quickly get out of hand and become practically impossible to debug.

Thankfully there are a number of advance features in NodeJS that are designed to flatten out these callbacks and to treat asynchronous code in a more _synchronous_ manner. These care called _Generators_, _Promises_ and _Async Functions_ and are described below. Even though you don't technically _need_ to know these, its worth learning them to keep your code manageable.

## 5 Generators

Until now we have made certain assumptions about NodeJS functions. One of these is that once a function starts running it will always run to completion before any other code runs. A **Generator** is a different kind of function that can be _paused_ at any time and _resumed_ later.

In concurrent programming there are two types of concurrency, _cooperative_, which allows the process to determine when the interruption happens, and _preemptive_, which allows the process to be interrupted by another process. A Generator is an example of _cooperative concurrency_ and use the `yield` keyword to trigger the interruption. To resume execution requires external control.

The cool feature of Generators is that messages can be passed to and from it.

Start by opening the `generators.js` file.

1. The `function *main()` function declares a _function generator_ which behaves much like a standard function.
2. At the end of the script we use this to instantiate an _iterator_ object we are calling `it`. This instantiates the iterator object but doesn't execute any of its contents.
3. To start iterating over the _generator function_ we call its `.next()` property, this runs the generator function up to the first `yield` keyword.
4. The `yield` function pauses the _generator function_ and passes control to the `getInput()` function, passing the parameter as normal.
5. At the end of the `getInput()` function the `.next()` function is called on the `it` _iterator object_ which passes control back to the _generator function_ which runs until it encounters the next `yield` keyword...
6. if an error occurs, the error object is passed to the _iterator object_'s `.throw()` function (see the `checkValidCurrencyCode()` function to see this in action).
7. Errors passed in this way are caught by the `catch` block in the _generator function_.

Simply by looking at the _function generator_ you can see how it has completely eliminated the nested callbacks, making the code much easier to read (and debug).

### 5.1 Test Your Knowledge

The sample script `generators.js` has the same functionality as the previous script `nestedCallbacks.js` and your challenge is to implement the same changes as the previous challenge (repeated below). The good news is that you have already solved a lot of the coding challenges and so you can focus on how to implement it using generators.

1. modify the script to ask for the currency to convert to and display only the one conversion rate.
2. instead of printing the exchange rate, ask for the amount to be converted and them return the equivalent in the chosen currency
3. use the [OpenExchangeRates](https://openexchangerates.org/api/currencies.json) API to display the full name of the chosen currency

## 6 Promises

_A promise is an object that proxies for the return value thrown by a function that has to do some asynchronous processing (Kris Kowal)._

A promise represents the result of an asynchronous operation. As such it can be in one of three possible states:

1. pending - the initial state of a promise.
2. fulfilled - the asynchronous operation was successful.
3. rejected - the asynchronous operation failed.

### 6.1 Creating a Promise

Promises are created using the `new` keyword. This function is called immediately with two arguments. The first argument resolves the promise and the second one rejects it. Once the appropriate argument is called the promise state changes.
```javascript
const getData = url => new Promise( (resolve, reject) => {
  request(url, (err, res, body) => {
    if (err) reject(new Error('invalid API call'))
    resolve(body)
  })
})
```
This example creates a `Promise` that wraps a standard callback used to handle an API call. Notice that there are two possible cases handled here.

1. If the API call throws an error we set the promise state to _rejected_.
2. If the API call succeeds we set the promise state to _fulfilled_.

As you can see it it simple to wrap any async callbacks in promises but how are these called?

### 6.2 Consuming a Promise

To use promises we need a mechanism that gets triggered as soon as a promise changes state. A promise includes a `then()` method which gets called if the state changes to _fulfilled_ and a `catch()` method that gets called if the state changes to _rejected_. 
```javascript
const aPromise = getData('http://api.fixer.io/latest?base=GBP')

aPromise.then( data => console.log(data))

aPromise.catch( err => console.error(`error: ${err.message}`) )
```
In this example we create a _new Promise_ and store it in a variable. It get executed _immediately_. The second line calls its `then()` method which will get executed if the promise state becomes _fulfilled_ (the API call is successful). The parameter will be assigned the value passed when the `resolve()` function is called in the promise, in this case it will contain the JSON data returned by the API call.

If the state of the promise changes to _rejected_, the `catch()` method is called. The parameter will be set to the value passed to the `reject()` function inside the promise. In this example it will contain an `Error` object.

This code can be written in a more concise way by _chaining_ the promise methods.
```javascript
getData('http://api.fixer.io/latest?base=GBP')
  .then( data => console.log(data))
  .catch( err => console.error(`error: ${err.message}`))
```
Because the Promise is executed immediately we don't need to store it in a variable. The `.then()` and `.catch()` methods are simply chained onto the promise. This form is much more compact and allows us to chain multiple promises together to solve more complex tasks.

### 6.3 Chaining Promises

The real power of promises comes from their ability to be _chained_. This allows the results from a promise to be passed to another promise. All you need to do is pass another promise to the `next()` method.
```javascript
const getData = url => new Promise( (resolve, reject) => {
  request(url, (err, res, body) => {
    if (err) reject(new Error('invalid API call'))
    resolve(body)
  })
})

const printObject = data => new Promise( resolve => {
  const indent = 2
  data = JSON.parse(data)
  const str = JSON.stringify(data, null, indent)
  console.log(str)
  resolve()
})

const exit = () => new Promise( () => {
  process.exit()
})

getData('http://api.fixer.io/latest?base=GBP')
  .then( data => printObject(data))
  .then( () => exit())
  .catch(err => console.error(`error: ${err.message}`))
  .then( () => exit())
```
Notice that we pass the `printObject` promise to the `then()` method. The data passed back from the `getData` promise is passed to the `printObject` promise.

Because we can chain `then()` and `catch()` methods in any order we can add additional steps after the error has been handled. In the example above we want to exit the script whether or not an error has occurred.

Despite the code in the `printObject` promise being _synchronous_ it is better to wrap this in a promise object to allow the steps to be chained. 

If a promise only takes a single parameter and this matches the data passed back when the previous promise _fulfills_ there is a more concise way to write this.

```javascript
getData('http://api.fixer.io/latest?base=GBP')
  .then(printObject)
  .then(exit)
  .catch(err => console.error(`error: ${err.message}`))
  .then(exit)
```


There are some situations where you can't simply pass the output from one promise to the input of the next one. Sometimes you need to store data for use further down the promise chain. This can be achieved by storing the data in the `this` object.
```javascript
getData('http://api.fixer.io/latest?base=GBP')
  .then( data => this.jsonData = data)
  .then( () => printObject(this.jsonData))
  .then(exit)
  .catch(err => console.error(`error: ${err.message}`))
  .then(exit)
```
In the example above we store the data returned from the `getData` promise in the `this` object. This is then used when we call the `printObject` promise.

### 6.4 Test Your Knowledge

Run the `promises.js` script, its functionality should be familiar to the `currency.js` script you worked with in chapter 3.

Open the `promises.js` script and study the code carefully. Notice that it defines 5 promises and chains them together. You are going to extend the functionality by defining some additional promises and adding them to the promise chain.

1. modify the script to ask for the currency to convert to and display only the one conversion rate.
2. instead of printing the exchange rate, ask for the amount to be converted and them return the equivalent in the chosen currency
3. use the [OpenExchangeRates](https://openexchangerates.org/api/currencies.json) API to display the full name of the chosen currency

### 6.5 Executing Code Concurrently

In the async examples we have seen so far, each async function needs to complete before the next async call is run. The diagram below shows how this looks.
```
         1      2      3        
      ───⬤─────⬤─────⬤
```
The program flow is.

1. The first async call `getData` is executed.
2. Once this has completed, `printObject` is executed.
3. Only when this has completed will the `exit` step execute.

There are many situations where two steps can run at the _same time_. This would be impossible to build using standard callbacks but this can be written using promises.

The first stage is to create an array of promises. Typically this is done by looping through an array of data and using this to return an array of promises.
```javascript
const dataArray = ['USD', 'EUR']
const promiseArray = []
dataArray.forEach( curr => {
	promiseArray.push(new Promise( (resolve, reject) => {
		const url = `http://api.fixer.io/latest?base=GBP&symbols=${curr}`
		request.get(url, (err, res, body) => {
			if (err) reject(new Error(`could not get conversion rate for ${curr}`))
			resolve(body)
		})
	}))
})
```
In the example above we loop through the `dataArray`, creating a new promise object that we push onto our `promiseArray`.

 Once we have an array of promises there are two possible scenarios.

1. We want _all_ the promises in the array to be fulfilled before continuing the promise chain.
2. We want _one_ of the promises to be fulfilled but we don't care which one.

#### 6.5.1 Promises All

In the first scenario we want _all_ the promises to be fulfilled before continuing and for this we use the `Promises.all()` method.

```javascript
Promise.all(itemPromises)
  .then( results => results.forEach( item => console.log(item)))
  .catch( err => console.log(`error: ${err.message}`))
```
When the `Promise.all()` method fulfills it returns an array of results. In the example above we loop through these and print each to the terminal.

#### 6.5.2 Promises Race

The alternative is that once one of the promises in the array has fulfilled we want to take its returned value and continue the promise chain. In this scenario we use `Promise.race()`.
```javascript
Promise.race(promiseArray)
	.then( result => console.log(result))
	.catch( err => console.log(`error: ${err.message}`))
```
As you can see, only a single value is returned by `Promise.race()`. In the example above you won't be able to predict which conversion rate will be returned but you will only get the one. A good application of this would be if you can get your data from multiple APIs but you don't know which ones are working.

## 7 Async Functions

In the previous sections we have covered the use of _generators_ which allow the use of synchronous-style code to handle async code but the syntax is far from intuitive.

We then looked at the use of _promises_ which allows you to wrap async code as a series of promises which can be chained together and implements exception handling. The price we pay for this is non-intuitive syntax which can become over complex. Async functions combine the benefits of promises with a clean synchronous-style syntax, avoiding the complex syntax used in promise chains. They are designed to simplify the behaviour of using promises in a synchronous manner.

Whenever we execute a function there is some implicit behaviour we expect. One behaviour is that, once invoked, a function will run until it gets to the end. Async functions break this behaviour, they can pause at any point and resume at a later point on the script. This enables us to write _asynchronous_ code that looks and feels _synchronous_, it can even use standard `try-catch` execption handling.

1. We can chain promises together in a cleaner way with full exception handling.
2. We can substitute a _promise_ with an _async function_ without needing to change any other part of the script.

### 7.1 Simplifying Promise Chains

Here is a simple example.

```javascript
const getData = url => new Promise( (resolve, reject) => {
  request(url, (err, res, body) => {
    if (err) reject(new Error('invalid API call'))
    resolve(body)
  })
})

const printObject = data => new Promise( resolve => {
  console.log(JSON.stringify(JSON.parse(data), null, 2))
  resolve()
})

async function main() {
  try {
    const data = await getData('http://api.fixer.io/latest?base=GBP')
    await printObject(data)
    process.exit()
  } catch (err) {
    console.log(`error: ${err.message}`)
    process.exit()
  }
}
main()
```
Async functions are declared using the `async` keyword in the function declaration, all errors are handled using the standard `try-catch` block. Because the main block of code needs to be in an _async function_, this has to be explicitly executed at the end of the script.

The `getData()` function returns a _promise_. it is called using the `await` keyword, this pauses the execution of the `main()` function until `getData()` is either _fulfilled_ or _rejected_. If it is _fulfilled_, the data returned is stored in the `data` variable and control moves to the next line, if it is _rejected_ code execution jumps to the `catch()` block.

### 7.2 Simplified Promises

Async functions are implicitly wrapped in a `Promise.resolve()` and any uncaught errors are wrapped in a `Promise.reject()`. This means that an _async function_ can be substituted for a _promise_. let's look at a simple example.
```javascript
const printObjectPromise = data => new Promise( (resolve) => {
  const indent = 2
  data = JSON.parse(data)
  const str = JSON.stringify(data, null, indent)
  console.log(str)
  resolve()
})

const printObjectAsync = async data => {
  const indent = 2
  data = JSON.parse(data)
  const str = JSON.stringify(data, null, indent)
  console.log(str)
}
```
both `printObjectPromise` and `printObjectAsync` behave in exactly the same manner. They both return a `Promise.resolve()` and so can be used in either a _promise chain_ or an _async function_.

### 7.3 Test Your Knowledge

Run the `asyncFunctions.js` script. Note that it works in the same way as the previous ones. Open the script and study it carefully.

1. modify the script to ask for the currency to convert to and display only the one conversion rate.
2. instead of printing the exchange rate, ask for the amount to be converted and them return the equivalent in the chosen currency
3. use the [OpenExchangeRates](https://openexchangerates.org/api/currencies.json) API to display the full name of the chosen currency
4. rewrite the `printObject` promise as an _async function_.
5. rewrite another promise as an _async function_.

## 8 Screen Scraping

In the previous tasks we have been working with data that is available via a RESTful API but what do you do if the information you need is only found in human-readable format in an HTML webpage?

In this task you will learn how to extract data from HTML web pages, a technique known as **Screen Scraping**. This is a much harder that using an existing API because:

- the html won't have semantic information
- if the website author changes the page your script will need to be rewritten.

Despite these issues sometimes this approach is the only way to get the information you need.


Open the `quotes/index.js` file and notice that it imports a custom `quotes` module, the `./` indicates that it is in the current directory. Because the parsing code can get quite complex it is best practice to place this in a custom module.

There is only one function in this module, called `getQuotes()` which takes two parameters, the author name plus a callback. The callback follows best practice by passing an error as the first parameter followed by the data.

Now open the `quotes/quotes.js` module. The screen-scraping functionality is in a private function which is referenced by the exported function, this makes it easier to update if the web page layout changes.

If an error occurs the callback is called with an Error as the first parameter. If no error occurs, the callback takes a `null` first parameter with the data as a second parameter. This pattern is consistent with the built-in JavaScript functions that take a callback.

Run the `index.js` script and try searching for a valid person (such as _Asimov_), copy the URL into the chrome web browser.

Open the _Developer Tools_ and choose the _Elements_ tab. As you hover over the DOM elements in the _Elements_ tab you will see the content highlighted in the browser window.

Use this to expand the DOM until you can highlight the first quote in the list.

- Notice that all the quotes are in a `<dl>` (definition list) tag.
- Each quote is in an `<a>` (anchor) tag a `<dt>` (definition term) tag.

In the `scraper()` function:

1. The supplied parameters are used to create a **unique url**. It is absolutely vital that:
  - each resource have a unique URL.
  - the URL for each resource can be calculated based on the supplied parameters.
2. The url is logged to the console so that it can be pasted into a browser to check for validity.
3. The `request` module is used to grab the web page html which is available i the `body` parameter of the callback.
4. The `cheerio` module loads the page DOM into a constant which can be parsed using JQuery syntax.
5. We then check the DOM for particular elements.
  - If there is a `<p>` tag containing the text `No quotations found` we know the search has returned no quotations so an error is returned.
  - The number of quotes is extracted from the DOM and stored as a property of the data object.
  - An empty `quotes[]` array is added to the `data` object.
  - [JQuery.each()](http://api.jquery.com/jquery.each/) is used to loop through each of the tags of interest.
  - Each quote is then pushed onto the `quotes[]` array.
6. Once all the data has been extracted from the DOM and added to the `data` object this is passed to the callback function.

### 4.1 Test Your Knowledge

The best way to learn about screen scraping is to have a go. In this task you will be writing a script to search for books based on ISBN numbers and returning useful data.

You will be using the Amazon website, start by searching for a specific ISBN such as `1449336361`, this will give you a URL to parse.

https://www.amazon.co.uk/JS-Next-ECMAScript-6-Aaron-Frost/dp/1449336361/ref=sr_1_1?ie=UTF8&qid=1475398158&sr=8-1&keywords=1449336361

The next step is to remove the unnecessary parts of the URL until you are left with something you can work with. This is a process of trial and error but you need to be able to construct this URL using only the ISBN number.

https://www.amazon.co.uk/dp/1449336361

Have a go at writing a `books` screen scraper and try to return:

1. Title
2. Authors
3. Description
4. Price
5. Rating

# Extension Activity

By now you whould have decided on the theme for your API.

1. Identify any existing APIs you can integrate into your assignment. Write a NodeJS script to extract and display appropriate data.
2. Identify websites that contain useful data and Write a NodeJS screen scraper to extract and display useful data (lists of items and details on specific items).

---

# Asynchronous JavaScript

Outcomes

- Understand and use command line options.
- Understand and use callbacks to produce asynchronous code.
- Understand the JSON data format and know how to convert between it and JavaScript objects.
- Understand Screen Scraping

## IO is Expensive

Waiting for IO to complete is big waste of resources
Three solutions:
synchronous
processes		Apache
threads			Node

## NodeJS Threading Model

NodeJS runs in a single thread
JavaScript supports lambda / callbacks
Callbacks run in their own threads
After callback thread is destroyed





Using Request.

Main methods correspond to HTTP verbs:
```
request.get(url, callback)
request.put(url, data, callback)
request.post(url, data, callback)
request.del(url, callback)
```
Be careful, because callbacks are asynchronous

## Callbacks

A callback (higher-order) function

Passed around like a variable

a function that is passed to another function as a parameter

the callback function is called (or executed) inside the other Function.

When we pass a callback function as an argument to another function, we are only passing the function definition.

The containing function has the callback function in its parameter as a function definition

The function is not executed in the parameter.

It can execute the callback anytime.

Callbacks are important!

NodeJS runs in a single threaded event loop.

If a long-running operation occurs, the process stops "blocks" until the event has finished.

To prevent blocking operations any long running activities are run in callbacks.

The callback is a function that should be run after the operation is complete.

While it is processing, control is passed back to the main event loop.

Simple GET request with callback:
```javascript
'use strict'
const request = require('request')
request.get( 'http://api.fixer.io/latest?symbols=GBP', (err, res, body) => {
  if (err) {
  console.log('could not complete request')
  }
  console.log(body)
})
```

## Data Exchange Formats

RESTful APIs send data across the Internet

Needs to be transmitted as text (ASCII/UniCode)

Needs to communicate both the data and its structure.

- Variables
- Objects
- Arrays

Common data exchange formats

- XML		(Extensible Markup Language)
- JSON	(JavaScript Object Notation)
- YAML	(Yet Another Markup Language)
- CSV		(Comma-Separated Values)

XML Example
```xml
<address>
  <org>Coventry University</org>
  <street>4 Gulson Road</street>
  <city>Coventry</city>
  <country>United Kingdom</country>
  <postcode>CV1 5FB</postcode>
</address>
```
JSON Example
```json
address {
  "org": "Coventry University",
  "street": "4 Gulson Road",
  "city": "Coventry",
  "country": "United Kingdom",
  "postcode": "CV1 5FB",
}
```
YAML Example
```yaml
address:
  org: "Coventry University"
  street: "4 Gulson Road"
  city: "Coventry"
  country: "United Kingdom"
  postcode: "CV1 5FB"
```
CSV Example
```csv
"org", "street", "city", "country", "postcode" 
"Coventry University", "4 Gulson Road", "Coventry", "United Kingdom", "CV1 5FB"
```
Why do we prefer the JSON format?

- Text-based
- Position independent
- Lightweight
- Interoperable with JavaScript Objects

Converting to and from JSON
```javascript
const jsObj = {
firstname: 'John',
lastname: "Doe"
}
const jsonStr = JSON.stringify(jsObj)
const jsonStr2 = JSON.stringify(jsObj, null, 2)
const newObj = JSON.parse(jsonStr)
```

## Screen Scraping

Sometimes called Data Scraping

Extracting data from a human-readable web page

Why use screen scraping?

Some data not available through an API

Usually a last resort

Sometimes companies scrape their own websites!

There are some challenges:

- Complex process
- Needs deconstructable URLs
- Success depends on the DOM not changing
- Most search results are paginated

Deconstructable URLs.

To access search results:

- Search term needs to be inserted into URL

To access resources:

- Product ID needs to be inserted into URL.

Here are some examples:

Amazon Book Search URL (javascript)
```
https://www.amazon.co.uk/s/ref=nb_sb_noss_2?url=search-alias%3Dstripbooks&field-keywords=javascript
https://www.amazon.co.uk/s/?url=search-alias%3Dstripbooks&field-keywords=javascript
```
Guardian Bookstore
```
http://bookshop.theguardian.com/catalogsearch/result/?q=javascript&order=relevance&dir=desc
http://bookshop.theguardian.com/catalogsearch/result/?q=javascript
```
BBC iPlayer search for history
```
http://www.bbc.co.uk/iplayer/search?q=history
```

Accessing resources.

Amazon Books
```
https://www.amazon.co.uk/JavaScript-Definitive-Guide-Guides/dp/0596805527/ref=sr_1_2?s=books&ie=UTF8&qid=1476384737&sr=1-2&keywords=javascript
https://www.amazon.co.uk/dp/0596805527

http://bookshop.theguardian.com/javascript-patterns.html
But the ISBN is 0596806752

http://www.bbc.co.uk/iplayer/episode/b019c88d/the-grammar-school-a-secret-history-episode-2
http://www.bbc.co.uk/iplayer/episode/b019c88d
```

Screen scraping techniques

- Browse to web page using Google Chrome
- Open the Developer tools (elements tab)
- Expand DOM structure and see what content it controls
- Uniquely identify the data
- Extract data using JQuery patterns

Module for screen scraper.

Process is messy

Needs updating when page structure changes

Need to isolate in its own module

Keep public interface simple

Public interface:

- Pass a search string and get a JavaScript array in return
- Pass a resource identifier and get a JavaScript resource back