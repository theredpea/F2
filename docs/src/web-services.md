% F2 Web Services

<p class="lead">Core to any browser-based application, web services provide access to data sets which enable [Container](container-development.html) and [App](app-development.html) Providers to build customized, multi-vendor solutions. F2 provides the financial industry with this shared specification; now with the addition of a suite of APIs, starting with an F2 Identity service, added utility and collaboration come to the forefront of a powerful web integration framework.</p>

* * * *

## F2 APIs

The following F2-provided web services are available:

* [F2 ID](#f2-id)

Complete documentation and demos are found below. If you have any questions about usage, implementation specifics or find any bugs, browse to the [F2 Google Group](https://groups.google.com/forum/#!forum/OpenF2) or [Issues on GitHub](https://github.com/OpenF2/F2/issues).

### Data Formats

All web services can return data in either `XML` or `JSON` format, with an optional `JSONP` wrapper. The data returned will be the same regardless of the format requested. F2 web services follow the "REST" model for web services. In simple terms, REST is a formal description of the HTTP protocol. Accessing a "REST" web service, therefore is merely a matter of making a standard HTTP request to a defined resource.

Except where otherwise specified, all of the web services described in this document are accessed by an HTTP GET request. HTTP POST requests are reserved for methods that change data on the server, e.g. updating a user's profile. Each request consists of a URI, and a list of parameters. The URI consists of a hostname and base path, a method name, and an optional format specifier. The parameters are appended to the URI on the query string.

Below is a sample request to a web service named `SampleRequest` in `XML` format.

`https://services.openf2.org/1.0/SampleRequest/xml?count=3&echo=example`

The `/xml` format specifier in the above request could be safely omitted, as `XML` is the default format. Other formats supported at the current time are `JSON` ("JavaScript Object Notation"), and `JSONP`. `JSONP` requests all require an additional parameter, named `jsoncallback`. 

The `XML` response format consists of a sequence of elements representing `Type` names and `Property` names. `Type` names are used for the root node, and to identify individual items in a collection. An example `XML` representation of the `SampleResponse` object is shown below:

```xml
<SampleResponse>
    <Echo>example</Echo>
    <SampleList>
        <SampleItem>
            <ItemNumber>1</ItemNumber>
        </SampleItem>
        <SampleItem>
            <ItemNumber>2</ItemNumber>
        </SampleItem>
        <SampleItem>
            <ItemNumber>3</ItemNumber>
        </SampleItem>
    </SampleList>
</SampleResponse>
```

The `JSON` response format does not include type information. A description of the `JSON` format can be found at [json.org](http://www.json.org). It will not be described in this document. A sample `JSON` response is shown below (with white space added for readability):

```javascript
{
    "Echo": "example",
    "SampleList": [
        { "ItemNumber": 1 },
        { "ItemNumber": 2 },
        { "ItemNumber": 3 }
    ]
}
```

The `JSONP` response format is similar, with the addition of a callback method name, `mySampleCallbackName`.

```javascript
mySampleCallbackName({
    "Echo": "example",
    "SampleList": [
        { "ItemNumber": 1 },
        { "ItemNumber": 2 },
        { "ItemNumber": 3 }
    ]
})
```

### Versioning

The suite of F2 Web Services will be versioned as a collection and the endpoint URL will contain the version number. To adhere to industry standards, F2 will be maintained under the Semantic Versioning guidelines as much as possible.

Releases will be numbered with the following format:

`https://services.openf2.org/<major>.<minor>.<patch>/path/to/service`

For more information on SemVer, please visit [SemVer.org](http://semver.org/).

### Forum

Have a question? Ask it on the [F2 Google Group](https://groups.google.com/forum/#!forum/OpenF2).

<OpenF2@googlegroups.com>

### Bug Tracking

To track bugs or issues, F2 is using [Issues on GitHub](https://github.com/OpenF2/F2/issues).

* * * *

## F2 ID

The F2 Identity service allows app developers to use a single identity convention for financial instruments. Knowing about and using a single ID as part of [Context messages](index.html#context) will allow for simpler communication between Container and App Providers. The need for a common identifier is not new. The financial industry has yet to agree upon a universal symbol set, so we've integrated and cross-referenced a majority of ticker symbols. The F2 ID is sourced from a database of identifiers allowing for robust search of tradable instruments, regardless of issue classification scheme.

This cross-referencing database developed by Markit On Demand, called XRef, enables users to execute symbol and company searches across countless numbers of data feeds to find exact matches quickly and intuitively. XRef allows companies the flexibility to use aggregated data sources while accommodating the range of names and symbols users access in a search. Presently, our Intersection System manages 750,000 cross-reference requests per minute during peak market hours. 

XRef allows both name and description based lookups for any underlying instrument. Lookup includes virtually any security identifier including: RIC, CUSIP, ISIN, NSIN, Sicovam, SEDOL, Valoren and WPK. The coverage universe is global fixed income, equity, futures, options and benchmarks and industry classifications.

Instead of reinventing XRef technology itself, F2 is providing access to Markit On Demand's cross-reference database and offering the F2 ID as the universal instrument identifier the financial industry needs.

### Understanding Context

Context is a term used to describe the state of an F2 container and its apps. At the same time, [Context](index.html#context) is also the information passed from [Container-to-App](app-development.html#container-to-app-context) or from [App-to-App](app-development.html#app-to-app-context) or from [App-to-Container](app-development.html#app-to-container-context). In the examples shown within [App Development](app-development.html), two types of context are shown: symbol and trade ticket context. It is important to understand [F2.js](f2js-sdk.html) allows in-browser messaging between third parties using a collection of arbitrary name-value pairs. **For the first time, F2 is enforcing structure to Context messages&mdash;this time in the form of an F2 ID.**

This example below demonstrates a simple Context message where a container broadcasts a [symbol change event](../sdk/classes/F2.Events.html). The Context message below contains a `symbol` property. Given the wide array of symbol identifiers in the financial industry and the inherent problem with using the Street symbol, neither `symbol` nor `AAPL` is very useful programmatically.

```javascript
//define message
var contextMessage = {
	symbol: "AAPL"
}
//broadcast event
F2.Events.emit(F2.Constants.Events.CONTAINER_SYMBOL_CHANGE, contextMessage);
```

In version 1.0.5, F2 is enforcing the name/value pair used as part of a symbol-based context message. Take this example, taking note of the name/value pair `SecurityID` and the integer representing AAPL, `36276`: 

```javascript
//define message
var contextMessage = {
	SecurityID: 36276
}
//broadcast event
F2.Events.emit(F2.Constants.Events.CONTAINER_SYMBOL_CHANGE, contextMessage);
```

**The integer in the `SecurityID` field is the all-new F2 ID**.

### Methods

The linked documentation describes the methods available through the API, their input and output parameters, and the possible errors. A `?` indicates a value is optional (for the request) or may be null (in the response). A `[ ]` indicates that the value is an array containing 0 or more instances of the specified type.

<p><a class="btn btn-primary" href="{{services}}/1.0/Lookup/doc/html" target="_blank">View complete F2 ID documentation</a></p>

### Demo

The purpose of this demonstration is to provide a quick look at the input and output of the API; it is not intended to serve as a reusable component for App Developers.

<form class="form-horizontal form-search" id="demo-F2IDLookupForm" autocomplete="off">
	<div class="control-group">
		<input type="text" class="span2" placeholder="Search term" >
		<span class="help-inline">is</span>
		<select class="span3">
	  		<option value="#">a what?</option>
	  		<option value="isin">an ISIN</option>
	  		<option value="symbol">a Street symbol</option>
	  		<option value="name">a full or partial company name</option>
		</select>
		<button type="submit" class="btn btn-primary" data-loading-text="Searching...">Search</button>
		<div class="help-block hide">Enter a search term _and_ select a query type.</div>
	</div>
  	
</form>

<div id="demo-F2IDLookupResults"></div>

* * * *

## Terms of Use

<span class="label label-warning">EDITOR'S NOTE</span> We need to put SLA and/or terms & conditions here.




