# Angulars
Angular courses
ANG.js
--------------------------------------
--------------------------------------
angular.module('app')
	.controller('todosCtrl',['$scope','$tosdoservice',function($scope,mytodos){
		mytodos.getTodos().then(function(todos){
			$scope.todos = todos;
		});
		
		$scope.addNewTodo = function(){
			var text = $scope.newTodo;
			
			$scope.todos.push({
			text:text,
			isDone:false
			});
			
			$scope.newTodo = '';
		}
	}]);
	
angular.module('app')
		.factory('todosservice',['$http',function($http){
			return{
				getTodos: function(){
					return $http.get('api file')
						.then(response => response.data)
				}
			}
		]});
		
		or return $timeout(() =>
		[ Json],1500);
		
var app= angular.module('app',['ngRoute']);

angular.module('app')
	.config(function($routeProvider, $locationProvider){
		$locationProvider.hashPrefix('!');
		$routeProvider.when('/todos',{
			controller: 'todosCtrl',
			template:`
							<div>
					<!-- <pre>{{ todos | json }}</pre> -->
					<form ng-submit="addNewTodo()">
						Add new task
						<input id="newTodo" type="text" ng-model="newTodo">
					</form>
					<p>
						<input placeholder="Search ..." type="text" ng-model="search"> //ng-model is the actual binding
					</p>
					<ul>
						<li ng-repeat="item in todos | filter:search" ng-class="{done : item.isDone}">
							<label>
								<input type="checkbox" ng-model="item.isDone">
								<span>{{ item.text | uppercase | mr }}</span>
								<span>{{ item.date | date }}</span>
							</label>
						</li>
					</ul>
				</div>
			`
	})
	
	.when('/',{
		redirectTo:'/todos'
		})
		
	.when('/books',{
		template: `Books Template`
	});
	//we could have use templateUrl: `location of html` instead of pasting the template here:
	For example
	---------------
	angular.module('app')
    .config(['$routeProvider', '$locationProvider', function ($routeProvider, $locationProvider) {
        $locationProvider.hashPrefix('!');
        $routeProvider.when('/todos', {
            controller: 'todosCtrl',
            templateUrl: `/views/todos.html`
        }).when('/', {
            redirectTo: '/todos'
        }).when('/books', {
            controller: 'BooksCtrl',
            templateUrl: '/views/books.html'
        }).when('/grid', {
            controller: 'GridCtrl',
            templateUrl: '/views/grid.html'
        });
    }]);
	
})

angular.module('app')
.filter('mr',function mrFilter(){
	return function transformer(input){
		return 'mr.' + input;
	}
});

<script src = "..."></script>
<link rel="stylesheet" href ="...">

<div ng-view>
	
</div>
----
S3--Minification
----
npm --> build.sh:
-------
echo 'Building my app ...'
echo 'Cleaning dist'
rm -rf $DIST/*

echo 'Concatenate app.js'
cat src/app/app.js > dist/app.min.js
cat src/app/config.js >> dist/app.min.js
cat src/app/mr.filter.js >> dist/app.min.js
cat src/app/todos/todos.controller.js >> dist/app.min.js
cat src/app/todos/todos.service.js >> dist/app.min.js

echo 'Copying assets ...'
cp -r src/{lib,style.css,api,index.html,views} dist

echo 'Compressing Javascripts'
uglifyjs dist/app.min.js --output dist/app.min.js --compress --mangle

----
S4--Directives
----
Directives:
 - User controls to defined new elements `restrict: 'E'`
 - Decorators to extend existing elements `restrict: 'A'`

Isolate scope by passing `scope: true`

Replace content with element by passing `replace: true`

angular.module('app')
	.directive('NameofDir', function(){
		return {
			replace:true //deprecated
			restrict: 'A',
			transclude: true, //will work with the parent scope and not the child scope
			link: function (scope,el){
				el.append('<button>Share</button>');
			scope: {
                title: '@', //from parent to child
                data: '=', //from child to parent
                onClick: '&', // 2-ways communication
            },
			});
		}
	}

Directives
-------------------------------------------------
------------------------------------------------
angular.module('app').directive('required', function () {
    return {
        restrict: 'A',
        link: function (scope, el, attrs) {

            el.prev().html(function (i, html) {
                return html + '<sup class="red">*</sup>'
            })

        }
    }
}).directive('book', ['todosService', 'bus', '$rootScope', 'userSettings', function (todosService, bus, $rootScope, userSettings) {
    return {
        transclude: true,
        restrict: 'E',
        template: `
<div class="book">

    <h4 ng-click="color='red'" style="color: {{color}}">{{ item.id }}.{{ item.name || 'No Name' }}</h4>

    <small>Views: {{ item.views }}</small>
    <small>Price: {{ item.price | number }} {{currency }}</small>

   <!-- <button ng-click="addToBasket({item: item, views: item.views})" class="btn btn-default"><i class="fa fa-check"></i></button> -->
    <button ng-click="triggerEvent('bookAdded', item)" class="btn btn-default"><i class="fa fa-check"></i></button>

    before
    <div ng-transclude></div>
    after

</div>
        `,
        scope: {
            // item: '@', // scope.item = 'books[0]'
            item: '=bookData', // scope.item = { id, name, view }

            addToBasket: '&onAdd'

        },
        link: function (scope, el, attrs) {

            scope.$on('scope-event', function () {
                console.log('receiving a message from another book')
            })

            console.log(todosService)

            console.log(scope.item)

            // scope.id = attrs.id;
            // scope.name = attrs.name;
            // scope.views = attrs.views;

            scope.triggerEvent = function (event, data) {
                scope.$emit('scope-event');
                bus.emit(event, data);
            }

        }
    }
}]).directive('outOfStock', function () {
    return {
        restrict: 'A',
        link: function (scope, el) {

            el.find('.book').css('border', '1px solid red');

            el.click(function () {
                alert('Out of stock item');
                return false;
            });
        }
    }
})
    .directive('shareable', function () {
        return {
            restrict: 'A',
            link: function (scope, el) {
                el.append('<button>Share</button>');
            }
        }
    })

    .directive('bcol', ['mrFilter', function (mrFilter) {
        return {
            replace: true,
            restrict: 'E',
            template: '<div class="col-md-3">{{ text }}</div>',
            link: function (scope, el) {
                scope.text = mrFilter(new Date().getFullYear());
            }
        }
    }])
    .directive('basket', function () {
        return {
            restrict: 'E',
            scope: true,
            link: function (scope) {
                scope.$on('scope-event', function () {
                    console.log('listening from basket');
                })
            }
        }
    })
    
  Books Ctrl and view
  ----------------------------------------------------------
  -----------------------------------------------------------
  Books controller:
----------------
angular.module('app')
    .factory('bus', function () {

        var global = new Bus();
        return global;

    })
    .controller('BooksCtrl', ['$scope', '$rootScope', 'bus', function ($scope, $rootScope, bus) {

        // $rootScope.currency = 'USD';

        $scope.booksAdded = [];

        $scope.status = 'solid';
        $scope.color = 'blue';

        $scope.books = [
            { id: 1, views: 100, name: 'Introduction to Angular', price: 11 },
            { id: 2, views: 200, name: 'Introduction to C#', price: 22 },
            { id: 3, views: 300, name: 'Introduction to Java', price: 33 },
        ]

        $scope.otherBook = { id: 4, views: 100, name: 'Angular 6' };

        $scope.onBookAdded = function (item, views) {

            console.log(item, views);

            $scope.booksAdded.push({ name: item.name, views: item.views });

            // console.log('a new message from book directive with: on book added ');
        }

        $scope.$on('scope-event', function () {
            console.log('event from directive');
        })

        bus.subscribe('bookAdded', function (item) {
            console.log('book added');

            $scope.booksAdded.push({ name: item.name, views: item.views });
        })

    }]);
	
	books html:
	------------
	<hr>

<div class="row">
    <!-- <div class="col-md-3">A</div> -->
    <!-- <div class="col-md-3">b</div> -->
    <!-- <div class="col-md-3">c</div> -->
    <!-- <div class="col-md-3">d</div> -->
    <bcol></bcol>
    <bcol></bcol>
    <bcol></bcol>
    <bcol></bcol>
</div>

<hr>

<div class="app">
    <div class="row">
        <div class="col-md-3">

            <basket></basket>

            <div ng-repeat="v in booksAdded">{{ v.name }}</div>

        </div>
        <div class="col-md-9">

            <div class="market">

                <book book-data="book" ng-repeat="book in books" on-add="onBookAdded(item, views)"></book>

                <book book-data="books[0]">
                    extra html here
                </book>

                <div class="horor">
                    <book shareable book-data="books[2]"></book>
                </div>
            </div>

        </div>
    </div>
</div>

ANG6
--------------------------------------------------------------
----------------------------------------------------------------
app
	app.component.css
	app.component.html
	app.component.spec.ts
	app.component.ts
	app.module.ts
	data.service.ts
index.html
main.ts
test.ts
tsconfig.app.json
tsconfig.spec.json
tslint.json

app.component.html
---------------------------
<!--The content below is only a placeholder and can be replaced.-->
<div style="text-align:center">
  <h1>
    Welcome to {{ title }}!
  </h1>
  <img width="300" alt="Angular Logo" src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNTAgMjUwIj4KICAgIDxwYXRoIGZpbGw9IiNERDAwMzEiIGQ9Ik0xMjUgMzBMMzEuOSA2My4ybDE0LjIgMTIzLjFMMTI1IDIzMGw3OC45LTQzLjcgMTQuMi0xMjMuMXoiIC8+CiAgICA8cGF0aCBmaWxsPSIjQzMwMDJGIiBkPSJNMTI1IDMwdjIyLjItLjFWMjMwbDc4LjktNDMuNyAxNC4yLTEyMy4xTDEyNSAzMHoiIC8+CiAgICA8cGF0aCAgZmlsbD0iI0ZGRkZGRiIgZD0iTTEyNSA1Mi4xTDY2LjggMTgyLjZoMjEuN2wxMS43LTI5LjJoNDkuNGwxMS43IDI5LjJIMTgzTDEyNSA1Mi4xem0xNyA4My4zaC0zNGwxNy00MC45IDE3IDQwLjl6IiAvPgogIDwvc3ZnPg==">
</div>
<h2>Here are some links to help you start: </h2>
<ul>
  <li>
    <h2><a target="_blank" rel="noopener" href="https://angular.io/tutorial">Tour of Heroes</a></h2>
  </li>
  <li>
    <h2><a target="_blank" rel="noopener" href="https://github.com/angular/angular-cli/wiki">CLI Documentation</a></h2>
  </li>
  <li>
    <h2><a target="_blank" rel="noopener" href="https://blog.angular.io/">Angular blog</a></h2>
  </li>
</ul>

app.component.spec.ts
-------------------------------
import { TestBed, async } from '@angular/core/testing';
import { AppComponent } from './app.component';
describe('AppComponent', () => {
  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [
        AppComponent
      ],
    }).compileComponents();
  }));
  it('should create the app', async(() => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.debugElement.componentInstance;
    expect(app).toBeTruthy();
  }));
  it(`should have as title 'app'`, async(() => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.debugElement.componentInstance;
    expect(app.title).toEqual('app');
  }));
  it('should render title in a h1 tag', async(() => {
    const fixture = TestBed.createComponent(AppComponent);
    fixture.detectChanges();
    const compiled = fixture.debugElement.nativeElement;
    expect(compiled.querySelector('h1').textContent).toContain('Welcome to ng6!');
  }));
});

app.component.ts
-------------------
import { Component } from '@angular/core';
import { DataService } from './data.service';
import { debounceTime, switchMap, distinctUntilChanged } from 'rxjs/operators';
import { Subject } from 'rxjs';

@Component({
  selector: 'app-root',
  template: `
    <div class="header">
      <a href="/"><img src="/assets/logo.png" /></a>
      <div class="menu">
        <a href="/">Home</a>
        <a href="/">Customers</a>
        <a href="/">Providers</a>
        <a href="/">Contact</a>
      <div>
    </div>

    <div class="body">

      <input type="text" [(ngModel)]="myText">

       <input type="text" (keyup)="click$.next($event.target.value)">

       <ul>
         <li *ngFor="let row of items$ | async">{{ row }}</li>
       </ul>

       <div [ngSwitch]="page">
         <div *ngSwitchCase="'home'">Home Page</div>
         <div *ngSwitchCase="'about'">About Page</div>
         <div *ngSwitchDefault> is {{ myText.length }}</div>
       </div>

    </div>

    <div class="footer">
      Copyright OMT {{ year }}
    </div>
  `,
})

// myDirective
export class AppComponent {

  myText = 'hellodsdf';

  public items$;
  public name = 'OMT FINANCE';
  year = new Date().getFullYear();

  click$ = new Subject<string>();

  constructor(public data: DataService) {
  }

  // tslint:disable-next-line:use-life-cycle-interface
  ngOnInit() {

    this.items$ = this.click$
      .pipe(
        debounceTime(1000),
        distinctUntilChanged(),
        switchMap(q => this.data.search(q))
      );

  }

  onButtonClicked() {
    console.log('button clicked');
  }

  onKeyup(value) {
    console.log(value);

    this.myText = value;

    // document.getElementById('pr').innerHTML = value;
    // document.getElementById('input2').value = value;

  }


  search(value) {

    this.data.search(value).pipe(debounceTime(1000)).subscribe(data => {
      console.log(data);
      this.items = data;
    });
  }

  getLowerName() {
    return this.name.toLowerCase();
  }

}

app.module.ts
----------------
import { BrowserModule } from '@angular/platform-browser'; // language level
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';

import { DataService } from './data.service';

import { AppComponent } from './app.component';

@NgModule({
  imports: [BrowserModule, FormsModule], // angular level
  declarations: [AppComponent],
  providers: [DataService],
  bootstrap: [
    AppComponent
  ]
})
export class AppModule { }

data.service.ts
-------------------
import { Injectable } from '@angular/core';
import { Observable, from, pipe } from 'rxjs';
import { delay, debounceTime } from 'rxjs/operators';

@Injectable()
export class DataService {

  public search(q: string) {

    return from([
      // mocking web api
      ['ahmad', 'maroun', 'gabi', 'micheal'].filter(x => x.includes(q))
    ]).pipe(delay(800));

  }

}

index.html
-------------
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Ng6</title>
  <base href="/">

  <style>
  [ng-reflect-model] {
    border: 1px solid red;
  }
  </style>

  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
</body>
</html>

main.ts
-----------
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';
import { environment } from './environments/environment';

if (environment.production) {
  enableProdMode();
}

// console.log(AppModule)
// console.log(platformBrowserDynamic);

platformBrowserDynamic().bootstrapModule(AppModule)
  .catch(err => console.log(err));

test.ts
--------
// This file is required by karma.conf.js and loads recursively all the .spec and framework files

import 'zone.js/dist/zone-testing';
import { getTestBed } from '@angular/core/testing';
import {
  BrowserDynamicTestingModule,
  platformBrowserDynamicTesting
} from '@angular/platform-browser-dynamic/testing';

declare const require: any;

// First, initialize the Angular testing environment.
getTestBed().initTestEnvironment(
  BrowserDynamicTestingModule,
  platformBrowserDynamicTesting()
);
// Then we find all the tests.
const context = require.context('./', true, /\.spec\.ts$/);
// And load the modules.
context.keys().map(context);

tsconfig.app.json
--------------------
{
  "extends": "../tsconfig.json",
  "compilerOptions": {
    "outDir": "../out-tsc/app",
    "module": "es2015",
    "types": []
  },
  "exclude": [
    "src/test.ts",
    "**/*.spec.ts"
  ]
}

tsconfig.spec.json
--------------------
{
  "extends": "../tsconfig.json",
  "compilerOptions": {
    "outDir": "../out-tsc/spec",
    "module": "commonjs",
    "types": [
      "jasmine",
      "node"
    ]
  },
  "files": [
    "test.ts",
    "polyfills.ts"
  ],
  "include": [
    "**/*.spec.ts",
    "**/*.d.ts"
  ]
}

tslint.json
---------------
{
    "extends": "../tslint.json",
    "rules": {
        "directive-selector": [
            true,
            "attribute",
            "app",
            "camelCase"
        ],
        "component-selector": [
            true,
            "element",
            "app",
            "kebab-case"
        ]
    }
}
