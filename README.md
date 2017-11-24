# COJ_project
# Week1
***
## Document Introduction

### e2e / karma.conf.js / protractor.cong.js 
```
For testing
```

### node_modules
```
Made by npm, then, npm will install libraries by package.json file
```

### gitignore 
```
Marked documents don't need to be uploaed eg. dependencies/node_module
```

### .angular-cli.json
* apps -> root -> src : the startpoint of our app
* outDir -> "dist" : files need to be uploaded (we'll change in the future)
* style -> style.css : global stylesheets (eg. bootstrap)

## src
### Template(VIEW)
```
Composing HTML templates with Angularized markup 
```
### Component(Model)
```
Classes to manage or support the templates.
Interacting with the VIEW through an API of properites and methods. 
```
- selector : How to show the page in index.html <app-root>

```ts
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
```

### Service
```
Adding application logic. Almost anything can be a service. (Logic Service / Data Service etc.)
```

### Module
- declarations : Component
- imports : Module
- providers : Service
- bootstrap : Enter
```
Boxing components and services. (收納箱)
```
```ts
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
```
***

# Components need to be Created
## Client (Data will connect with a fake)

```
╔  Router: app.routes.ts 
║      ↓
║      ↓       ╔ Problem-list
╟ Conponents ══╟ Problem-detail
║              ╟ New Problem
║              ╚ Navbar
║                   ↓ (without Navbar)
╚  Data Service ←  ↙          
```
- Made a dir. components in src/app/
- Used ag-cli automatically create four files 
- and add ProblemListComponent in app.module.ts 

```md
$mkdir components
$ng g c problem-list
```
***
## Add Bootstrap
- install bootstrap & jQuery package from npm 
- Since we don't use all bootstrap module, suggest to use from npm
```
npm install bootstrap --save
npm install jquery --save
```
- Add both jQuery nd Bootstrap in the script and stylesheet from ".angular-cli.json"
```js
      "styles": [
        "styles.css",
        "../node_modules/bootstrap/dist/css/bootstrap.min.css"
      ],
      "scripts": [
        "../node_modules/jquery/dist/jquery.js",
        "../node_modules/bootstrap/dist/js/bootstrap.js"
      ],
```


***
## Problem List Component

### In problem-list.component.ts
```
Use tag <app-problem-list> made by Angular in "app.component.html.ts" 
could show the content from "problem-list.component.html"  
```
```
index.html(<app-root>) <- 
app.component.html.ts(<app-problem-list>) <-
problem-list.component.html.ts
```

```ts
 selector: 'app-problem-list',
  templateUrl: './problem-list.component.html',
```
### Add modeles for gaining Problems
- Opne a model file under the app
```
$ mkdir models
$ touch models/problem.model.ts
```
- Export a problem model
```ts
export class Problem{
    id: number;
    name: string;
    desc: string;
    diff: string;
}
```
### Create a mock data 
- In problem-list component, we need to import the model and create a variable with mock datas

```ts
const PROBLEMS: Problem[] = [
  {
    id: 1,
    name: "Two Sum",
    desc: `Given an array of integers...`,
    diff: "easy"
  },
  /// ...
];
```
### Model

- In model, we will provide problems to VIEW which only can access content inside ProblemListComponent.

- ngOnInit() : initization

- getProblems(): give model PROBLEMS outside to the problems inside Component

- :void : give the "type" of callback value to the method.

```ts
export class ProblemListComponent implements OnInit {
  problems = []; // give a list
  
  constructor() { }

  ngOnInit() {
    this.getProblems();
  }

  getProblems(): void{
    this.problems = PROBLEMS;
  }

}
```
## View
### (problem-list.component.html)
- Build up a Structure with bootstrap
```ts

```
- "*ngFor": looping data from database and print it out
- Let each value in problems array save in a variable problem
```ts
*ngFor="let problem of problems"
```
- Gain the data from problems model and show on HTML markup (data binding)
```ts
{{problem.diff}}
```
- Also need a binding for class in span element, since we need to add-in a styling tag based on difficulty.
```ts
<span class="{{'pull-left label difficulty diff-' + problem.diff.toLowerCase()}}">
```

## Add in CSS Stylesheet
- problem-list.component.css
```css
.difficulty {
  min-width: 65px;
  margin-right: 10px;
}
....
```
***
# Seperate the Data and Model/View
- Create a single reusable data service and inject it into the components that need it.

## Move mock data to seperate file
- to mock-problems.ts and export it
```ts
import { Problem } from "./models/problem.model";
export const PROBLEMS: Problem[] = [
 //....
]
```
## Seperate the getting problem logic
```
$cd src/app
$mkdir services
$cd services
$ng g s data
```

### Provide the servie in app.module
```ts
@NgModule({
  providers: [
    DataService
  ],
})
```

### Add service in DataService
- Import problem model and mock problems
```ts
import { Injectable } from '@angular/core';
import { Problem } from '../models/problem.model';
import { PROBLEMS } from '../mock-problems';
```
- Add Method to help get singal problem with id and whole problems
```ts
  getProblems():Problem[]{
    return PROBLEMS;
  }

  getProblem(id: number): Problem{
    return PROBLEMS.find((problem) => problem.id === id );
  }
```

## Inject the service into problem-list component
```ts
import {DataService} from '../../services/data.service';

    problems: Problem[];
    constructor(private dataService: DataService) { }

    getProblems(): void{
    this.problems = this.dataService.getProblems();
```

*** 
## Problem Detail Component
```
ng g c problem-detail
```
*** 
### Single page app Routing
- Client side routing is the same as server side routing, 
- but it's ran in the browser
### Add app.routes.ts
```
touch app/app.routes.ts
```
- Import angular/router && Components

```ts
import { Routes, RouterModule } from '@angular/router';
import { ProblemListComponent } from './components/problem-list/problem-list.component';
import { ProblemDetailComponent } from './components/problem-detail/problem-detail.component';
```
### Settle a router and export for Root
```ts
const router: Routes =[
    {
        path: "",
        redirectTo: 'problems',
        pathMatch: 'full'
    },
    {
        path: 'problems',
        component: ProblemListComponent
    },
    {
        path: 'problems/:id',
        component: ProblemDetailComponent
    },
    {
        path: '**',
        redirectTo: 'problems'
    }

];

export const routing = RouterModule.forRoot(router);
```

### Import in app.module
```
  imports: [
    BrowserModule,
    routing
  ],
```

### Change app.c.html for showing router page
```ts
<router-outlet></router-outlet>
```


### add a router link in an anchor tag (problem-list.component.html)
```ts
 <a class="list-group-item" *ngFor="let problem of problems"
      [routerLink]=['/problems',problem.id]>
```
***
### Add Pipe for SUMMARY
- Add summary.pipe.ts
```
$ng -g -p summary
```
- Import Pipe, PipeTransform and Give a logic to SUMMARY
```ts
import { Pipe, PipeTransform } from '@angular/core';
@Pipe({
    name: "summary"
})

export class SummaryPipe implements PipeTransform {
    transform(value: string, limit?: number){
        if (!value)
            return null;
        return value.substr(0,70) + '...';
    }
}
```

- Add SummaryPipe into Module
```ts
@NgModule({
  declarations: [
    AppComponent,
    ProblemListComponent,
    ProblemDetailComponent,
    SummaryPipe
  ],
```

- Used Summary in html markup
```html
<div class="list-group-item description "> {{problem.desc | summary}}</div>
```

*** 
## Problem Detail Component
- Import Probelm from model
```ts
import { Problem } from '../../models/problem.model';

export class ProblemDetailComponent implements OnInit {
  problem : Problem[];
}
```
- Import Data from DataService and Gain data from ActivedRoute
```ts
import { DataService } from '../../services/data.service';
import { ActivatedRoute, Params} from '@angular/router';

  constructor(private dataService: DataService, private route: ActivatedRoute)  { }

```

- Used ngOnInit to get the id from Route and change the parameter from String into Number

```ts
  ngOnInit() {
    this.route.params.subscribe(params => {
      this.problem = this.dataService.getProblem(+params['id']); // Change String into Number
    })
  }
```

- Add HTML markup (*ngIf to show existed problem)
```html
<div class="container" *ngIf = "problem">
```

## NEW-Problem Component
- Add new-problem component
```
$ ng g c new-problem
```

- Import Form module
```ts
  imports: [
    BrowserModule,
    routing,
    FormsModule
  ],
```

- HTML markup for New Problem Form 
- [()]: "Banana in the Box" for TWO-WAY data binding
* []: Property Binding (One-Way)
* (): Event Binding (One-Way)

- Input Name <label> + <input>
```html
<div>
  <form #formRef = "ngForm">
    <div class="form-group">
      <label for="problemName">Problem Name</label>
      <input name="problemName" id="problemName" 
      class="form-control" type="text" required 
      placeholder="Please Enter Problem Name" 
      [(ngModel)]="newProblem.name">
    </div>
    <div></div>
    .
    .
    .
  </form>
</div>
```

- Select Difficulities <\select> + <\option>
```html
<div class="form-group">
        <label for="problemDiff">Difficulty</label>
        <select name="diff" id="diff" class="form-control"  
        [(ngModel)]="newProblem.diff">
          <option *ngFor = "let diff of diffs" [value] = "diff">
            {{diff}}
          </option>
        </select>
      </div>
```

- Type Area <\textarea><\textarea>
```html
      <div class="form-group">
          <label for="problemDesc">Problem Description</label>
          <textarea name="problemDesc" id="problemDesc" 
          class="form-control" required 
          placeholder="Please Enter Problem Description" 
          [(ngModel)]="newProblem.desc" rows="3">
          </textarea>
      </div>
```

- Submit Button (with Click EVENT)
```html
<div class="row">
    <div class="col-md-12">
     <button type="submit" class="btn btn-info pull-roght"
    (click) = "addProblem()">
        Add Problem
    </button>
    </div>
</div>
```

### Modle and Data in New-Problem

- Import Problem Model into new-problem.component and Add a string array of difficulities

```ts
import { Problem } from '../../models/problem.model';

```
- Give a const variable of default Problem
```ts
const DEFAULT_PROBLEM: Problem = Object.freeze({
  id:0,
  name:'',
  desc:'',
  diff:'easy'
});
```



- Assign Value to Prpblem and then send the data to DataService for processing (by addProblenm())
- Import DataService
- Connent to Service in constructor
- Chagne new Problem into Default Problem after each added

```ts
newProblem: Problem = Object.assign({}, DEFAULT_PROBLEM);
  diffs: string[] = ['easy', 'medium', 'hard', 'super'];
  constructor(private dataService: DataService) { }
```
```ts
  addProblem(){
    this.dataService.addProblem(this.newProblem);
    this.newProblem = Object.assign({}, DEFAULT_PROBLEM);
  }
```

- dataService modify: Since we couldn't change the array of PROBLEMS, we saved it to another variable for adding new Problems

```ts
problems: Problem[] = PROBLEMS;
```
- Also, change the original PROBLEM to this.problems which we setted last step
```ts
  getProblems():Problem[]{
    return this.problems;
  }
```

- Add a method of "addProblem()"
- Give a new problem an id and push this object into problems array
```ts
  addProblem(problem: Problem): void{
    problem.id = this.problems.length + 1;
    this.problems.push(problem);
  }
```

## Navbar Component
- create files for navbar
```
ng g c navbar
```
- Put navbar in app.component.html
```html
<app-navbar></app-navbar>
<router-outlet></router-outlet>
```
- Copy navbar codes from bootstrap
```html
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container"> 
      <div class="navbar-header">
        <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-2" aria-expanded="false">
          <span class="sr-only">Toggle navigation</span>
......
```

## Add footer
- install font-awsome
```
npm install --save font-awesome
```
- Add font-awesome stylesheet in ".angular-cli.json" 
```ts
"../node_modules/font-awesome/css/font-awesome.css"
```

- Add html
```html
<div class="container">
  <div class="social-icons">
   <ul class="list-inline">
     <li><a href="mailto:weichien711@gmail.com?Subject=Visiter from your website" target="_blank"  ><i class="fa fa-envelope"></i></a></li>
     <li><a href="https://github.com/WeiChienHsu" target="_blank"><i class="fa fa-github" ></i></a></li>
     <li><a href="https://www.linkedin.com/in/weichien-hsu/" target="_blank"><i class="fa fa-linkedin"></i></a></li>
   </ul>
 </div> <!-- /.social-icons -->
</div>  
```