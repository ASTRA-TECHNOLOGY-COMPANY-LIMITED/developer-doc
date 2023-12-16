# Angular Overview

# What is Angular?

- Angular is open-source front-end web framework developed by Google.
- It's designed to build dynamic, single-page web applications (SPAs) with the Model-View-Controller (MVC) architectural pattern.

# Key Features

## 1. Structured Framework:

Angular provides a well-defined structure that helps in organizing code efficiently. Its modular approach with components, services, and modules makes code more maintainable and scalable.

## 2. Powerful Templating and Data Binding:

Angular’s two-way data binding simplifies the synchronization between the model and the view, reducing the need for manual DOM manipulation.

## 3. Dependency Injection:

Built-in dependency injection simplifies component management, making the codebase more modular, reusable, and testable.

## 4. TypeScript:

Angular is built with TypeScript, a superset of JavaScript that adds strong typing, improved tooling, and better error detection to JavaScript development.

## 5. Rich Ecosystem and Community Support:

Angular has a vast ecosystem with tools like Angular CLI for project scaffolding, Angular Material for UI components, and NgRx for state management. The community actively contributes libraries, extensions, and resources.

## 6. MVVM (Model-View-ViewModel) Architecture:

Angular follows the MVVM architecture, making it easier to separate concerns, manage application logic, and enhance testability.

## 7. Cross-Platform Development:

With Angular, developers can use the same codebase to build applications for the web, mobile (using frameworks like NativeScript or Ionic), and desktop (using Electron).

## 8. Comprehensive Tooling:

Angular offers a comprehensive set of tools and libraries for testing, debugging, and performance optimization, aiding in faster development cycles.

## 9. Robust Routing and Navigation:

Angular’s router provides powerful features for handling navigation and routing between different views or pages within an application.

## 10. Backed by Google:

Angular is maintained and backed by Google, which ensures continuous updates, improvements, and long-term support.

# Getting Started

To begin with Angular, you need to install Angular CLI: `npm install -g @angular/cli`

Then, follow these steps:

- Create a new Angular project: `ng new --no-standalone project-name`
- Run the development server: `ng serve`

# Project structure

`

- src
  - app
    - components
      - component1
        - component1.component.html
        - component1.component.ts
        - component1.component.css
      - component2
        - component2.component.html
        - component2.component.ts
        - component2.component.css
    - services
      - service1
        - service1.service.ts
      - service2
        - service2.service.ts
    - models
      - model1.ts
      - model2.ts
    - assets
      - images
        - image1.jpg
        - image2.png
      - styles
        - styles.css
    - environments
      - environment.prod.ts
      - environment.ts
    - app.module.ts
    - app-routing.module.ts
    - app.component.ts
- e2e
  - src
    - ...
- node_modules
- angular.json
- package.json
- tsconfig.json
- README.md
  `

## 1. src Folder:

app Folder:

- components Folder: Contains Angular components, each with its HTML, TypeScript, and CSS/SCSS files.
- services Folder: Contains services responsible for handling data, making API calls, and sharing information between components.
- models Folder: Houses data models/interfaces used to define the structure of objects within the application.
- assets Folder: Stores static files like images, fonts, and global stylesheets.
- environments Folder: Holds environment-specific configuration files for development and production environments.
- app.module.ts: The root module of the application where you define dependencies and bootstrap the app.
- app-routing.module.ts: File responsible for managing routing and navigation within the application.
- app.component.ts: The root component that hosts the main application logic and templates.

## 2. e2e Folder (End-to-End Testing):

- Contains files and configurations for end-to-end testing using tools like Protractor.

## 3. node_modules Folder:

- Stores all the project's dependencies installed via npm or yarn.

## 4. Configuration Files:

- angular.json: Configuration file for Angular CLI settings, build configurations, and project-specific settings.
- package.json and package-lock.json or yarn.lock: Files defining project metadata, dependencies, and scripts for npm or yarn.
- tsconfig.json: Configuration file for TypeScript compiler settings.

## 5. README.md File:

- A markdown file containing essential information about the project, its setup, usage, and any other relevant details for developers.

# Angular 17 document website:

https://angular.dev/overview

# CURD project github

## Angular 17: https://github.com/vutran221097/angular17-curd

How to Run:

1. Run backend follow this steps:

- `cd angular17-curd/server`
- `npm i`
- `npm start`

2. Run frontend follow this steps:

- `cd angular17-curd`
- `npm i`
- `npm start`

# Component

## Component file

`ts
import { Component, OnInit } from '@angular/core';
import { Posts } from '../../models/Posts.model';
import { PostsService } from '../../services/posts.service';

@Component({
  selector: 'app-posts-list',
  templateUrl: './posts-list.component.html',
  styleUrls: ['./posts-list.component.css'],
})
export class PostsListComponent implements OnInit {
  posts?: Posts[];
  currentPost?: Posts;
  currentIndex = -1;
  title = '';

  constructor(private postService: PostsService) {}

  ngOnInit(): void {
    this.retrievePosts();
  }

  retrievePosts(): void {
    this.postService.getAll().subscribe(
      (data: any) => {
        this.posts = data;
        console.log(data);
      },
      (error: any) => {
        console.log(error);
      }
    );
  }

  refreshList(): void {
    this.retrievePosts();
    this.currentPost = undefined;
    this.currentIndex = -1;
  }

  setActivePost(post: Posts, index: number): void {
    this.currentPost = post;
    this.currentIndex = index;
  }
}
`

## Component structure
- component.ts
- component.html
- component.css

## Named component
First your will need to check angular.json

`
projects:{
   "prefix": "app"
}
`

If your project have prefix config. You will named component like this: prefix-componentName

# Routing

`ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { PostsListComponent } from './components/posts-list/posts-list.component';
import { PostDetailsComponent } from './components/post-details/post-details.component';
import { AddPostComponent } from './components/add-post/add-post.component';

const routes: Routes = [
  { path: '', redirectTo: 'posts', pathMatch: 'full' },
  { path: 'posts', component: PostsListComponent },
  { path: 'posts/:id', component: PostDetailsComponent },
  { path: 'add', component: AddPostComponent },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
})
export class AppRoutingModule {}
`

# Module

`ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpClientModule } from '@angular/common/http';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { AddPostComponent } from './components/add-post/add-post.component';
import { PostDetailsComponent } from './components/post-details/post-details.component';
import { PostsListComponent } from './components/posts-list/posts-list.component';

@NgModule({
  declarations: [
    AppComponent,
    AddPostComponent,
    PostDetailsComponent,
    PostsListComponent,
  ],
  imports: [BrowserModule, AppRoutingModule, FormsModule, HttpClientModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
`
