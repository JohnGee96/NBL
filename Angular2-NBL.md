# Anuglar2 Syntax N.B. List:

## Advance Features

    Renderer + ElementRef
        * This combination allows cross-platform compatibility (mobile, desktop, and web-worker)
        * [Link](https://netbasal.com/angular-2-explore-the-renderer-service-e43ef673b26c)
    
    Best Practices
        * [Link](https://github.com/escardin/angular2-community-faq/blob/master/best-practices.md#proper-use-of-eventemitters)


## General Architecture

    Modules: largest unit of modularity 
        
        @NgModule takes a metadata with following prop:
            1. Declaration: view classes
            2. Exports
            3. Imports: initialize the module with importing other modules/libraries
            4. Providers: creators of services that this module contributes to. They become globally accessible (i.e. d3).
            5. Bootstrap: main application view. Only root module should set this prop


    Components: mostly used to control view

        @Component takes in a metadata with following prop:
            1. selector: CSS 
            2. templateUrl: html
            3. providers: array of dependecy injection providers that components requires


    Templates: format the view

    Metadata: tells Angular how to process a class (e.g. treat it as a Component/Module)

    Data binding: transfer data via {{var}} and [(ngModel)] (2-way binding) syntax 

    Directives: language semantics that changes DOM rendering (e.g. ngFor/ngIf/ngModel)

    Services: broad term encompassing anything

    Dependency injection: language mechanics that imports the required dependencies

## Component Interactions
  1. @Input decorators (parent -> children)
  2. @Output + EventEmitter (children -> parent)
  3. ngOnChanges(children -> parent)    
  4. @ViewChild (children -> parent: allowing parent TO ACCESS and call child methods)
  5. Local Variable 
        - Local Variable allows a parent component to use data binding to read child properties or invoke child methods (limited and NO ACCESS to child component)

                @Component({
                    selector: 'countdown-parent-lv',
                    template: `
                    <h3>Countdown to Liftoff (via local variable)</h3>
                    <button (click)="timer.start()">Start</button>
                    <button (click)="timer.stop()">Stop</button>
                    <div class="seconds">{{timer.seconds}}</div>
                    <countdown-timer #timer></countdown-timer>
                    `,
                    styleUrls: ['demo.css']
                })
                export class CountdownLocalVarParentComponent { }

        * The parent component cannot data bind to the child's start and stop methods nor to its seconds property.

        * You can place a local variable, #timer, on the tag <countdown-timer> representing the child component. That gives you a reference to the child component and the ability to access any of its properties or methods from within the parent template.

  * [Reference](https://angular.io/guide/component-interaction#parent-listens-for-child-event)

 ## ** Combining Multiple Modules **

    1. Add the modules in the declarations list of @NgModule within app.modules file.
    2. Then the selector for each of the declared modules is free to use globally in templates


## Decorators

    Including @NgModule, @Component, etc...

        They are functions that modify JS classes and other metadata objects
        
        Under the surface, they are essentially a way to
        call high order functions (taking other func as parameters) and they can be used to attach onto functions and properties.

        A decorator is just an expression that will be evaluated and has to return a function.

## Class Declaration

    export class Hero {
        constructor(
            public id: number,
            public name: string) { }
    }

        That brief syntax does a lot:
            1. Declares a constructor parameter and its type.
            2. Declares a public property of the same name.
            3. Initializes that property with the corresponding argument when creating an instance of the class.

## Reference Variable

    Declared by prefixing variable with "#" (i.e. #var)

        A reference variable refers to its attached element, component or directive. It can be accessed anywhere in the entire template.

## Built-in Directives:

NgFor
    
    <div *ngFor="let hero of heroes">{{hero.name}}</div>
        
        Take each hero in the heroes array, store it in the local hero looping variable, and make it available to the templated HTML for each iteration.

        "*" is indicator for Angular-specific syntax

NgIf

    <p *ngIf="heroes.length > 3">There are many heroes!</p>

    <!-- isSpecial is true -->
    <div [class.hidden]="!isSpecial">Show with class</div>
    <div [class.hidden]="isSpecial">Hide with class</div>

    <!-- HeroDetail is in the DOM but hidden -->
    <hero-detail [class.hidden]="isSpecial"></hero-detail>

    <div [style.display]="isSpecial ? 'block' : 'none'">Show with style</div>
    <div [style.display]="isSpecial ? 'none'  : 'block'">Hide with style</div>

** Guard Against Null: **

    Why?
        App may crash and able to render the page if accessing
        a null element. View will disappear. To render view unobstructedly while waiting to receive data, handling
        the null element temporarily should be advised.

        The ngIf directive is often used to guard against null. Show/hide is useless as a guard. Angular will throw an error if a nested expression tries to access a property of null.

    EX:
        <div *ngIf="currentHero">Hello, {{currentHero.name}}</div>
        <div *ngIf="nullHero">Hello, {{nullHero.name}}</div>

    Other approaches:

        <!--No hero, div not displayed, no error -->
        <div *ngIf="nullHero">The null hero's name is {{nullHero.name}}</div>

    Chaining to detect null:

        <p *ngIf="nullHero">The null hero's name is {{nullHero && nullHero.name}} </p>

            These approaches have merit but can be cumbersome, especially if the property path is long. Imagine guarding against a null somewhere in a long property path such as a.b.c.d.
    
    Safe Navigation Operator (?.)

        <p *ngIf="nullHero"> The null hero's name is {{nullHero?.name}} </p>

            The Angular safe navigation operator (?.) is a more fluent and convenient way to guard against nulls in property paths. The expression bails out when it hits the first null value. The display is blank, but the app keeps rolling without errors.

            It works perfectly with long property paths such as a?.b?.c?.d


## Custom Directive:
* Three components: 
    1. Decorator
    2. Attribute Selector
    3. Constructor

            Directive Decorator:
                Declares a class as a directive by adding `@directive`

            Attribute Selector:
                Supports standard css selector syntax.
                Selectors wrapped with `[]` will selector HTML parent element or the
                add on with the given name
                    For example,
                        `[selector]` will select <p selector> or <div selector>, etc...
                        `.a` will selector elements declared as "a" class

            Directive Constructor:
                Angular injects the type `ElementRef` into the class constructor
                of when instantiated. The `ElementRef` is a wrapper for the DOM
                of the selected element.

                Angular can also provides platform independent way of setting element style through `Renderer`.

        Example: 

                import { Renderer, ElementRef    } from '@angular/core';
                .
                .
                .
                class CardHoverDirective {
                constructor(private el: ElementRef,
                            private renderer: Renderer) { (1)
                    renderer.setElementStyle(el.nativeElement, 'backgroundColor', 'gray'); (2)
                }
                }


## Host Binding and Host Listner
- [Reference](https://ng2.codecraft.tv/custom-directives/hostlistener-and-hostbinding/)
- Angular's way of handling DOM event.
- They are types of a directive
- Example:

        import { HostListener } from '@angular/core'
        .
        .
        .
        class CardHoverDirective {
            @HostBinding('class.card-outline-primary')private ishovering: boolean;

            constructor(private el: ElementRef,
                        private renderer: Renderer) {
                // renderer.setElementStyle(el.nativeElement, 'backgroundColor', 'gray');
            }

            constructor(private el: ElementRef,
                        private renderer: Renderer) {
                // renderer.setElementStyle(el.nativeElement, 'backgroundColor', 'gray');
            }

            @HostListener('mouseover') onMouseOver() {
                let part = this.el.nativeElement.querySelector('.card-text');
                this.renderer.setElementStyle(part, 'display', 'block');
                this.ishovering = true;
            }

            @HostListener('mouseout') onMouseOut() {
                let part = this.el.nativeElement.querySelector('.card-text');
                this.renderer.setElementStyle(part, 'display', 'none');
                this.ishovering = false;
                }
            }
        }
    


## Angular Lifecycle Hook
  * ngOnInit()

        Angular offers interfaces for tapping into critical moments in the component lifecycle: at creation, after each change, and at its eventual destruction.

            To Use:
                import { OnInit } from '@angular/core';
                export class AppComponent implements OnInit {
                    ngOnInit(): void {
                    }
                }
  * ngOnChanges() 

     - Respond when Angular (re)sets data-bound input properties. The method receives a SimpleChanges object of current and previous property values.
        
     - Called before ngOnInit() and whenever one or more data-bound input properties change.



## Data Binding:

    Regular binding expressions 

        <hero-detail [hero]="selectedHero"></hero-detail>

            Ihis method require the "Input" module from '@angular/core' and requires that
            the target binding property (hero) to be ab input property.
            Ex:
               @Input() hero: Hero;

            This is needed because by default Angular treats the component's template as private member of the componenet itself. The properties of a component or directive are hidden from binding by default. The @input decorator renders the property public.

            You can tell if @Input is needed by the position of the property name in a binding.

                1. When it appears in the template expression to the right of the equals (=), it belongs to the template's component and does not require the @Input decorator.

                2. When it appears in square brackets ([ ]) to the left of the equals (=), the property belongs to some other component or directive; that property must be adorned with the @Input decorator.

    Two-way binding
        
        <input [(ngModel)]="selectedHero.name" placeholder="name">
    
    Class binding for CSS 
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" 
        [class.selected]="hero === selectedHero">


## Creating Services

    Naming Convention

        Name.service.ts

    Procedure
        1. Create services and add @Injectable decorator
        2. Add the services in Component's provider lists
        3. Declare private var in the Component's constructor parameter (this so injector
           knows where to put the service in the component )

    Example

        import { Injectable } from '@angular/core';
        import { HEROES } from './mock-heroes';
        import { Hero } from './hero';

        @Injectable()
        export class HeroService {
            getHeroes(): Hero[] { 
                return HEROES;
            }
        }

            The @Injectable() decorator tells TypeScript to emit metadata about the service. The metadata specifies that Angular may need to inject other dependencies into this service.

    Importing Service:

        Best Practice 

            NOT to declare:
                heroService = new HeroService(); // don't do this

            TO do:
                1. Add a constructor that also defines a private property.
                    constructor(private heroService: HeroService) { }
                2. Add to the component's providers metadata.
                    providers: [HeroService]
                3. Add setter for the private var 
                    getHeroes(): void {
                        this.heroes = this.heroService.getHeroes()
                    }
                4. Call this setter function to initialize 

## Promises

    Reference: 
        
        https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise

    General Structure

        new Promise( (resolve, reject) => {
            // do asyn actions (http, API calls, etc)
            // you can also resolve or reject the promise as // needed
            // IMPORTANT: all errors are silenced if the 
            // Promise is resolved and error thrown in 
            // any callback here will not be catched.
            // Example:
            resolve(value);
            reject(reason)  
        }).then( 
            // handle value if promise is resolved or value 
            // is returned
            (value) => { //do something }
        ).catch(
            // catch any error
            // skipped if Promise is resolved
            (reason) => { console.log('Error detected ')}
        )
        ** ANYTHING BELOW is usually handled by the caller of
        ** the Promise
        .then( (value)=> {
            // do something of return value from the promise
        },
            (reason) => {
            // called this function when Promise is rejected
            }
        )

    The HeroService returns a list of mock heroes immediately; its getHeroes() signature is synchronous.

        this.heroes = this.heroService.getHeroes();

    To make the service asynchronous, add a Promise onto the service

        A Promise essentially promises to call back when the results are ready. You ask an asynchronous service to do some work and give it a callback function. The service does that work and eventually calls the function with the results or an error

            hero.serive.ts

        getHeroes(): Hero[] { 
            return HEROES;
        }

                BECOMES:

        getHeroes(): Promise<Hero[]> {
            return Promise.resolve(HEROES);
        }

    As a result of the change to HeroService, this.heroes is now set to a Promise rather than an array of heroes.

        getHeroes(): void {
            this.heroes = this.heroService.getHeroes();
        }

                BECOMES:

        getHeroes(): void {
            this.heroService.getHeroes().then(heroes => this.heroes = heroes);
        }

            The arrow function is preferred so "this" is not redefined in the function passed into the "then()" function

## Route

    <base href>

        Sets the base routing route. Preferred to be declared as <base /> in thie index.html for most app.

    Import @angular/router

        import { RouterModule, Routes, ... } from '@angular/router';

    Create and import the RouterModule in the Module file

        @NgModule({
        imports: [
            BrowserModule,
            FormsModule,
            RouterModule.forRoot([
            {
                path: 'heroes',
                component: HeroesComponent
            }
            ])
        ],...

            This creates the '/heroes' path that links to display the "heroes" component

    Add Route Links in the template of the corresponding Component

        template: `
            <h1>{{title}}</h1>
            <a routerLink="/heroes">Heroes</a>
            <router-outlet></router-outlet>
            `
        
            The "<router-outlet>" tag renders the component the routers is connecting

    ** Parameterized Route **

        To configure Route Module to use a parameter in the URL

                 src/app/app.module.ts
            {
                path: 'detail/:id',
                component: HeroDetailComponent
            },

        Change hero-detail.component.ts to receieve "hero" from URL parameter

            visit https://angular.io/tutorial/toh-pt5#revise-the-herodetailcomponent

            ngOnInit() {
            this.route.params
                // (+) converts string 'id' to a number
                .switchMap((params: Params) => this.service.getHero(+params['id']))
                .subscribe((hero: Hero) => this.hero = hero);
            }
        
                Since the parameters are provided as an Observable, you use the switchMap operator to provide them for the id parameter by name and tell the HeroService to fetch the hero with that id.

                ** Observable is a class that is used as the primary object in the router directives **

                    When subscribing to an observable in a component, you almost ALWAYS arrange to unsubscribe when the component is destroyed.

                    There are a few exceptional observables where this is not necessary. The ActivatedRoute observables are among the exceptions.

                    The ActivatedRoute and its observables are insulated from the Router itself. The Router destroys a routed component when it is no longer needed and the injected ActivatedRoute dies with it.

                    Feel free to unsubscribe anyway. It is harmless and never a bad practice.


        To retrieve more information about the Router directives

            see https://angular.io/guide/router#activated-route
        
        
        Sample Routing Module:

            import { NgModule }             from '@angular/core';
            import { RouterModule, Routes } from '@angular/router';
            
            import { DashboardComponent }   from './dashboard.component';
            import { HeroesComponent }      from './heroes.component';
            import { HeroDetailComponent }  from './hero-detail.component';
            
            const routes: Routes = [
            { path: '', redirectTo: '/dashboard', pathMatch: 'full' },
            { path: 'dashboard',  component: DashboardComponent },
            { path: 'detail/:id', component: HeroDetailComponent },
            { path: 'heroes',     component: HeroesComponent }
            ];
            
            @NgModule({
            imports: [ RouterModule.forRoot(routes) ],
            exports: [ RouterModule ]
            })
            export class AppRoutingModule {}

                ** If you have guard services, the Routing Module adds module providers (There are none in this example.)


## Debugging Hint:

    For Injectable modules/libraries, define a private member in the param of class constructor so the Injector knows where to place the value.

        i.e.)
        export class HeroesComponent implements OnInit {
             constructor(
                private router: Router,
                private heroService: HeroService) { }

    When Angular fails to render a particular instants of data binding, it can cause other instants to fail as well even if they are valid.

    The hero id is a number. Route parameters are always strings. So the route parameter value is converted to a number with the JavaScript (+) operator.

    {{selectedHero.name | uppercase}} this CAP the value

    ngOnInit is only called once per component instantiation