# MEAN Stack Development Best Practices

A collection of development best practices for rapidly developing MEAN (MongoDB, Express, Angular, Node) applications

## Front End Project Structure Conventions

The front end of your structure should group code by view with the exception of common modules or services. A normal module should be structured like:

````
-[module]
    -[controllers]
        -moduleController.js
    -[services]
        -moduleService.js
    -[directives]
        -moduleDirective.js
    -[filters]
        -filterName.js
    -[partials]
        -modulePartialName.html
    -[specs]
        -moduleTestName.spec.js
    -[data]
        -data.json
    -index.js
````

Using Gulp/Browserify, you can define the dependencies of your module and bind the controllers/services etc in the index.js file and have browserify crawl the application and generate a dependency tree as well as spit out a single concatenated JS file. An index.js file should look like this:

````
    'use strict';
    require('modules/common/services/commonService');
    require('modules/common/modules/commonModule');
    require('modules/common/services/commonService2');
    require('../config').registerModule('my-module', ['commonService', 'commonModule', 'commonService2']);
    require('./config/myModule.config');
    require('./controllers/myModuleController');
````

Based on this structure, the application tree should look something like:

````
[www]
    -[css]
    -[img]
    -[fonts]
    -[js]
        -[config]
        -[libs]
        -[mocks]
        -[modules]
            -[common]
                -[modules]
                    -[commonModule1]
                        -[config]
                            -commonModule1.config.js
                        -[controllers]
                            -commonModule.js
                        -[directives]
                            -directiveName.js
                        -[filters]
                            -filterName.js
                        -[partials]
                            -templateName.js
                        -[specs]
                            -testName.js
                        -[data]
                            -data.json
                        -index.js
                -[services]
                -[utilities]
            -[modules]
                -[view1]
                    ...standard module structure...
                index.js <--bootstrap the app, set up all configs in the .run, .config, or .value blocks 
                config.js <--define app name, dependencies, any application properties you want to include with all modules
                config-test.js <-- configure the tests, require mocks, etc
                modules.js <--contains a manifest of all modules to be included in the app
        index.js <--requires ./modules
    index.html <--your app entry point
````

## Build Process Conventions

### The Standard Project Stack

Gulp, Browserify, Karma/Jasmine Tests, Angular for FE MVC

### Generating Project Scaffolding using Yeoman

To run the InRhythm MEAN Project scaffolding generator:

`npm install -g generator-irmeanstack`

This will generate a project scaffold using best practices set out for rapid development

## CSS Conventions

### Use Icon Fonts Where Possible

Your /img folder or the equivalent should not be a massive list of assets. While there are some assets that must be delivered as .png or .jpg, many common icons can be compacted for better performance. Reduce the number of network requests by using SVG and creating an icon font containing all of the icons you'll need. A great tool for setting up icon fonts is http://icomoon.com which will let you manage the creation of the font as well as the addition or deletion of assets.

### If You Can't Use Icon Fonts, Use Spritemaps

If for any reason you are unable to create icon fonts with assets provided by the design team, then concatenate all assets into a single file and generate a set of css classes that reference the position of each asset. There are tools like https://draeton.github.io/stitches/ that make spritemap creation very easy.

### `z-index` Scale

Don't sprinkle your z-index declarations freely about your Sass. Place them all into a single file where you can manage them centrally. This will help avoid easily fixable element layer bugs and standardize z-indexes used across your development team. If possible, settle on an accepted z-index range (i.e. 1-10) which can be set to sass vars then you can set up a number of classes for different layer z-index values to use throughout the application.

### Color Scales and Type Scales

Centralize your color and type (font size, font weight, font family) variables in one central declaration that can be leveraged throughout the application.

### Class Name Syntax

Lower case and with dashes between words like `my-class`.

### Abstract Components

When writing css (and JS code for that matter), think about what common elements can be abstracted to form re-usable components. Instead of `.homepage-login-button`, create an abstracted `.button` class which can be modified with context classes.

### Styling the Styles

Style blocks should have exactly one new line between them. When separating selectors by a comma, each selector should have its own line. All css should be indented by 4 spaces. Most modern IDEs have a setting for this.

### Style Specificity

Don't get too crazy with the specificity of your selectors as this has a performance penalty. Try to simplify your selectors as much as possible. For example:

````
BAD

ul.user-list li span a:hover { 
    color: red; 
}
````

versus

````
GOOD

.user-list a:hover { 
    color: red; 
}

````

### Mobile First

Responsive styling should follow a mobile first approach. Media queries are defined in a common sass file and should only use the named media queries.

### No Inline Styles

There should be no inline styles in the HTML files. Ever. All styles need to be defined in a .scss file.

### Spaces & Indentation

All HTML/JS/CSS/SCSS code should be indented using spaces, with 4 spaces per indent. Most IDEs can handle "smart indents", so the indent settings should be set accordingly.

**`!important"`**

The "!important" tag should be avoided. Use CSS cascading rules for defining styles. 

http://coding.smashingmagazine.com/2010/11/02/the-important-css-declaration-how-and-when-to-use-it/

### Naming Convention

Styles should contain a common prefix defined by your project tech lead i.e. "ir-" to prevent class name collisions. Dashes should be used between words in class names.


## JS Conventions

### File Naming Conventions

File names should match the name of the controller/directive/service/etc and should be in camelCase

### Using jsHint

All code must pass standard JSHint tests. This will be established as part of the build to various environments.
The JSHint profile will be run as part of the gulp builds.
And as a requirement, a JSHint plugin must be activated in your IDE:
IntelliJ IDEA / Webstorm: This functionality is built in. Under settings this is in "JavaScript > Code Quality Tools > JSHint", then click the checkbox on top to enable

#### Adding Predefs

To add additional global predefs, this will need to be added in two places:
`[project root]/.jshintrc`: Add the additional name to the "predef" array

To add JS file specific predefs, this can be added as a comment on the top of the page (comma separated):

````
/*global yourPredefName, yourSecondPredefName */
define([...], function(){
    ...
});
````

### Documenting JS Code

It is required to add JSDoc documentation for all JS files. More information available at:
http://usejsdoc.org/

#### ngDoc

In addition to JSDoc for standard modules, ngDoc should be utilized to describe Angular modules. More information available at:

https://github.com/angular/angular.js/wiki/Writing-AngularJS-Documentation

https://github.com/idanush/ngdocs/wiki/API-Docs-Syntax

Example documentation:

````
/**
 * @ngdoc directive
 * @name AppModuleName.directive:loremIpsum
 * @restrict A
 * @description
 * "Lorem ipsum dolor sit amet, consectetur adipisicing elit
 *
 * @example
 * <example>
 * <file name="lorem.html">
 * <div>implementation example</div>
 * </file>
 * </example>
 */
 App.directive("loremIpsum", ['scope', function($scope){
     return {
         restrict: 'A',
         link: function(scope, element, attrs){
             // do stuff
         }
     };
}]);

````

### JS Coding Conventions

We will adhere to a subset of the AirBnb Javascript coding conventions (excluding their spacing standard among other things) which can be found here: https://github.com/airbnb/javascript

Fork of AirBnb JS conventions to come in a later version.

### Using Angular

#### Minimize jQuery 

Angular has built-in functionality similar to some of the tools that come with jQuery. Use these before introducing any jQuery functionality. Our goal is to move away from jQuery completely, if possible. See AngularJS Docs: angular.element.
A good reference on how to implement various functionality without jQuery is AngularUI for Twitter Bootstrap.

#### Bind Once

When developing in Angular 1.3, be sure to use bind-once when whenever possible. This improves performance by minimizing the number of digest cycles.

#### Dependency Injection

In order for uglification to not obfuscate angular dependencies, the following inline method for injection should be followed:

````
myApp.controller('nameOfController', ['$scope', '$timeout', 'nameOfService', function($scope, $timeout, nameOfService){
   ...your code here...
}]);
````

This also applies in directives:

````
myApp.directive('nameOfDirective', function() {
    return {
        restrict: 'A',
        controller: ['$scope', function($scope) {
            // content
        }]
    };
});
````

#### Controllers

 - There should be no DOM manipulation in any Angular controllers. All DOM manipulation should be kept in directives.
 - Communication between modules should happen using events. You can fire events from `$rootScope` if the two controllers are at the same level in the hierarchy.
 - When using the event broadcast and emit system, remember that `$broadcast` fires the event down the hierarchy of controllers and modules (i.e. from a controller to a directive called within that controllers scope) an `$emit` fires events up the hierarchy. Events broadcasted from `$rootScope` are available to any module if you inject `$rootScope` into as a dependency.
 - Don't name your controller with a "Ctrl" or "Controller" suffix.
 - Controllers should be very light. Data transforms and manipulation should happen in the service.
 
#### Services

 - Angular services should be used for any functionality that requires a RESTful call. This would better aid creation of mock factories or services in unit testing.
 - Services should contain all data transforms that need to be done on data returning from a web service. You should use the `transformResponse` property of `$http` for this.
 
#### IE8 Compatibility :-(

Because IE8 needs to be supported, there are various guidelines to follow:

https://docs.angularjs.org/guide/ie
- *Directives:*
  - When using the element method (E), a createElement must be added into a global property configuration file
  - In some cases, the element method will break even if a createElement is added. Switching to the attribute method (A) and adding "data-" prefix to your attribute will fix the issue.
- *$q:*
  - When using the "catch" and "finally" methods in a $q promise object, this will break in IE8 unless you wrap the methods in brackets:
  
  
````
function myDeferredObj(){
    var d = $q.defer();
    // do something with resolving
    return d.promise;
}
 
var d = myDeferredObj();
 
d.then(function(){
    //do something
})
['catch'](function(){
    //catch something
})
['finally'](function(){
    //finally do something
});
````

### Code Testing and Coverage

All AngularJS code is tested using KarmaJS with Jasmine. Testing will occur when running the `gulp test` task which can be configured in your gulpfile to run for every build.
Karma and supporting plugins will need to be installed on your machine.

#### 1. Install npm

If you do not have the Node Package Manager set up on your machine, you can follow these steps to get started:
*Windows* 
Install the Node package manager (http://nodejs.org/)

**OSX**
1. Install Homebrew (http://brew.sh/) and Node by running the following in terminal:
  - `ruby -e "$(curl -fsSkL https://raw.github.com/Homebrew/homebrew/go/install)"`
  - `brew doctor` *(Note: brew doctor will give a list of recommendations to make sure your system is configured properly. This may include installing the latest XCode, Command Line for XCode, and removing various files/folders)*
2. Once Homebrew is installed, run the following commands in terminal to install Node Package Manager:
  - `brew install node`
  - `curl https://npmjs.org/install.sh | sh`
  - `export NODE_PATH="/usr/local/lib/node"`
  - `export PATH="/usr/local/share/npm/bin:$PATH"`
  
**OSX Alternative Installation**
Install https://github.com/creationix/nvm into your bash profile. Node Version Manager (NVM) allows you to have different versions of node installed and not have to install brew or have superuser access to add npm modules.
Installation (from terminal):
`curl https://raw.github.com/creationix/nvm/v0.4.0/install.sh | sh`

#### 2. Install Karma

Once you have the Node Package Manager installed, open either your Terminal or Command Prompt and run the following:
Navigate to the root folder of your installation
Run the command `npm install`
This should execute and install all of the dependencies defined in the package.json file.

#### 3. Creating AngularJS Tests

All AngularJS modules (directives, controllers, etc) need to have a corresponding spec file. All spec files are in the folder /js/app/ng/spec (in the corresponding content folder in /etc/designs), and subfolders in the /spec folder should match all of the subfolders in the /ng folder. The name of the tests should match the name of the module being tested, along with the suffix "spec" at the end of the file name. For example, the spec file for "/ng/controllers/accordion.js" should be created as "/ng/spec/controllers/accordion.spec.js".

#####Jasmine-jQuery

In addition to the standard angular-mocks syntax, the Jasmine-jQuery library has been added in order to write easier tests using jQuery-friendly methods.

More information: https://github.com/velesin/jasmine-jquery

#####Tutorials

Here are some good tutorials to get started on writing tests for AngularJS:

http://www.yearofmoo.com/2013/01/full-spectrum-testing-with-angularjs-and-karma.html

#####Example

When defining your test, you will need to structure in the following way:

````
'use strict';

(function(){
    require('./../yourModule');

    var $scope, $controller, yourController, yourMockFactory;

    describe('Your Controller: your controller:', function(){
        beforeEach(function(){
            configTest.bootstrapModule("yourController", null, ['yourMockFactory']);
        });

        function createController(){
            yourController = $controller('yourController', {
                '$scope' : $scope,
                yourFactory : yourMockFactory
            });
        }

        beforeEach(inject(function(_$rootScope_, _$controller_, _yourMockFactory_) {
            $scope = _$rootScope_;
            $controller = _$controller_;
            yourMockFactory = _yourMockFactory_;

            createController();
        }));

        it('should do something cool', function() {
            var whatToDo = 'your test code goes here';

            expect($scope.testVar).not.toBe(null);
        });

    });
})();
````

#### 4. Mocking Services/Factories

A large amount of functionality involves testing service calls and how our AngularJS code handles them. Testing our mock data can be achieved through mock factories.
Mock JSON data responses should be placed in a /data folder within the module being tested

##### Mocking Services/Factories with a promise object response

Here is an example test to set up a mock service and how to test the response. Let's start with a sample factory and controller:

````
App.factory('sampleFactory', ['$q', '$timeout', function($q, $timeout){ 
    return { 
        getItems: function(){ 
            return { 
                var d = $q.defer();
                $timeout(function(){
                    d.resolve({ rickAstley: "Never Gonna Give You Up" });
                }, 1000); 
 
                return d.promise;
            } 
        } 
    };
});
 
App.controller('sampleController', function ($scope, sampleFactory) {
    sampleFactory.getItems().then(function(data){ 
        $scope.returnedData = data; 
    });
});
````

Now let's create our test with mock factory:

````
var $scope, $controller;
var sampleController;
var sampleFactoryData, sampleMockFactory;
 
describe("sampleController test", function(){
    beforeEach(module('your-module');
 
    beforeEach(inject(function($injector){
        $scope = $injector.get('$rootScope');
        $controller = $injector.get('$controller');
 
        // Set the path to where the JSON file lives
        jasmine.getFixtures().fixturesPath = 'base/quicklane/clientlibs_quicklane/js/test/mock/';
 
        // Fetch the JSON data and store locally
        sampleFactoryData = JSON.parse(readFixtures("sampleFactory.json"); // { rickAstley: "Never Gonna Let You Down" }
 
        // Set up the mock factory
        sampleMockFactory = {
            getItems: function(){
                var d = $q.defer();
                d.resolve(sampleFactoryData);
                return d.promise;
            }
        };
 
        // Add spy to the mock factory method
        spyOn(sampleMockFactory, "getItems").andCallThrough();
 
        // Initialize the controller with the mock factory in place
        sampleController = $controller('sampleController', {
            $scope: $scope,
            sampleFactory: sampleMockFactory
        });
 
        // Make sure to initialize the scope, otherwise this won't work
        $scope.$digest();
    }));
 
    it("should call the service and return the mock data", function(){
        expect(sampleMockFactory.getItems).toHaveBeenCalled();
        expect($scope.returnedData.rickAstley).toBe("Never Gonna Let You Down");
    });
});
````

##### Mocking $http responses

Because a service utilizing $http responds in either a "success" or "error", our tests will need to use $httpBackend to for capturing and watching for the $http calls. In addition to watching for expected GETS/POSTS, $httpBackend can also alert our tests if any unexpected calls are made as well.

Here is an example service using $http:

````
App.service('sampleService', ['$http', function($http){
	this.serviceMethod = function(formData){
		return $http({
			method: 'GET',
			params: formData,
			url: '/path/to/service'
		});
	};
}]);
 
App.controller('sampleController', ['$scope', 'sampleService', function($scope, sampleService){
	sampleService.sampleMethod({ name: 'Rick Astley' })
		.success(function(response){
			$scope.something = response;
		});
}]);
````

A few things note when utilizing $httpBackend in unit tests:
 - Use $injector to get $httpBackend initialized in your test
 - Wrap the controller creation into a method that gets called AFTER the expectGET/expectPOST/etc is called
 - For an expectGET, you may need to apply the query string params that are getting passed into the $http service
 - Use $httpBackend.flush() after the controller is created
 - Set an "afterEach" to call "$httpBackend.verifyNoOutstandingExpectation()" and "$httpBackend.verifyNoOutstandingRequest()". This will check if any expects or unexpected calls are made
  
Here is an example of setting up a test with $httpBackend:

````
var $scope, $controller, $httpBackend;
var sampleController, sampleData;

var sampleService = '/path/to/service.do';

describe('Sample controller:', function(){	
	function createSampleController() {
		sampleController = $controller('sampleController', {
 			$scope: $scope
		});
 	}
 
	beforeEach(module('your-module'));
	
	beforeEach(inject(function($injector) {
 		$httpBackend = $injector.get('$httpBackend');
 		$scope = $injector.get('$rootScope');
 		$controller = $injector.get('$controller');
 
		jasmine.getFixtures().fixturesPath = 'base/path/to/js/test/mock/';
		sampleData = JSON.parse(readFixtures("sampleData.json"));

}));
 
it("should call the sample service", function(){
 	$httpBackend.expectGET(sampleService + '?name=Rick').respond(sampleData);
 	createSampleController();
 	$httpBackend.flush(); 	
});
 
afterEach(function() {
 	$httpBackend.verifyNoOutstandingExpectation();
 	$httpBackend.verifyNoOutstandingRequest();
});
````

#### 5. Mocking `$window`

If $window is used to reference global variables, location, etc, that information and functionality can be mocked in our unit tests. To mock the data:

````
App.controller('yourController', ['$scope', '$window', function($scope, $window){
    var _data = $window.windowVar;
 
    // rest of the controller...
}]);
 
describe('Test Name:', function() {    
    beforeEach(module('your-module', function($provide){
        $provide.value('$window', {
            windowVar: { ... mock data ... }
        });
    }));
 
    // rest of the test...
});
````

