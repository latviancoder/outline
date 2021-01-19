# Course outline: Build your own mini-Strava

This course is aimed at beginner and intermediate React developers interested in using interactive maps and data visualisations in their applications. Students will build a mini-clone of Strava/Komoot - cycling/running route directory with map and elevation profile. 

TypeScript is going to become an important topic throughout this course. Having autocomplete and static error checking inside the editor is much nicer than cryptic “undefined is not a function” in browser console. For npm libraries we’ll use types almost as documentation at our fingertips. 

To display the map and the route we’ll use Leaflet.js - a library for building mobile-friendly interactive maps. Unlike what’s usually suggested in online tutorials and blogposts we’ll use it directly without relying on readymade integrations like react-leaflet. This will help students better understand what’s actually happening on the framework level and see various React hooks in action.

Because of elevation profile complexity we won’t be able to use some generic charting library and instead will be utilizing D3, a leading library for data visualisation on the web. Using D3 with React is slightly different from the native JS approach because we can declaratively render SVG objects in JSX instead of appending them to DOM. While building the elevation profile students will become familiar with concepts like scales, shapes and axes.

The whole course will be built as if students are in an actual work environment, while applying patterns I’ve learned over 10+ years of working on diverse frontend projects at different companies.

In the process of building the app we are also going to touch following topics:
- Using modern development practices to simplify development process (typescript, prettier)
- Planning for scale, performance and teamwork (container components, directory structure etc)
- Using hooks to bring imperative libraries like Leaflet and D3 into the declarative world of React
- Creating TS typings for libraries which don’t provide them and extending existing ones (type declarations and modules)
- Building custom hooks 
- Using Context to avoid prop-drilling and understanding its tradeoffs
- Using React Devtools Profiler tab to identify and fix rendering performance issues

## Module 1: Setup and general structure
- Setting up development environment using create-react-app
- Using prettier
- Sketching and implementing basic component structure
- Using CSS modules
- Thinking in types
- Fetching data with useEffect
- Container components
- Routing with @reach/router

### Setup development environment 
To bootstrap our application we’ll use create-react-app, which is a common way of creating new React applications. It includes everything you need to build a modern single-page app: development server, linter, test runner, TypeScript support and more.

We’ll also install Prettier for code formatting. Code styling is a very sensitive subject, so we’ll use an opinionated tool to avoid such problems. Prettier is also indispensable when working in teams. 

### Component hierarchy
We’ll use some kind of sketching tool to plan component hierarchy and approximate state management strategy. At this point we want to emphasize the importance of planning before diving headfirst into code.

### Basic component structure
Using our sketch as a reference point we’ll create a basic component structure (App -> Sidebar/Details). To create boilerplate code for components we will use code scaffolding (webstorm live templates/vscode snippets). It’s a good opportunity to explain how important it is to automate mundane codings tasks. We are also going to briefly touch why after v17 we don’t need to import React anymore.

### Basic styling
Short intro to CSS modules. Continuing the topic of automation we will introduce Emmet, which makes writing CSS much faster. We will create basic styling for container, sidebar and details pages. 

When using global CSS selectors name clashing is very common. We will explain how scoped CSS modules solve that problem and demonstrate it in devtools.

### Data source
Before starting to build our api we’ll define data shape first using TypeScript. Thinking about types first is a good planning exercise for students. It makes us realize potential issues earlier and provides autocomplete, which simplifies working with data in our editor. 

Building an actual backend is out of scope of this course, so we’ll use Promises to simulate asynchronous data fetching. We could also add a setTimeout to simulate loading state.

At this point we want to decide which api endpoints we will build and where we will call them. Let’s use the sketch we’ve built to discuss. It’s tempting to just define a single getRoutes endpoint and fetch everything at once, but will this approach scale? If we add a bunch of complex routes we might easily be looking at megabytes of data. A more future-proof solution would be to only fetch what we need, when we need it.

### Fetching the data and displaying route list
Using useState and useEffect hooks to fetch data in function components. We will explain how it’s different from componentDidMount and classes — students should stop thinking in lifecycles and start thinking in effects. useEffect can become quite a footgun for beginner developers because of stale state values and potential to create infinite chains of updates, so we should  emphasize the importance of react-hooks/exhaustive eslint rule.

Should we split our components in “containers” and “presentational”? The concept of container components is still alive and well in 2021. They add some boilerplate but greatly improve testability and make our code more resilient to future changes. We will skip containers mainly because testing is out of scope of this course.

Now that we have the data we can display the route list. We will create a separate component instead of packing everything into Sidebar because having smaller building blocks almost always makes more sense. 

We’ll take activity icons from react-icons library, which is definitely an overkill for such a small app, but highlights nicely how TypeScript can be useful.

### Adding router
Now we’ll need to somehow communicate the selected route to the details page. We could use simple state management on app level instead of routing, but then we’d lose route selection state after every reload and also wouldn’t be able to go forward/back in history. Instead we’ll introduce a simple router (@reach/router).
 
## Module 2: Working with the map
- Using Leaflet.js and OpenStreetMap to display the map
- useRef for instance variables
- useEffect for side-effects
- GPX and GeoJSON formats
- Leaflet Polyline and Marker
- Using geographic data for calculations 
- Custom hooks
- react-dom renderToString

### Displaying map
After all required preparations are done we’ll finally start working on the map. We’ll install leaflet.js directly without relying on react adapters because it makes it easier to understand what’s happening under the hood. For students who don’t feel all that comfortable using hooks it’s also a perfect opportunity to see them in action.

We’ll initialize an empty map first and explain how leaflet itself is tiles-agnostic, then use free OpenStreetMap tiles. We’ll use useRef to save map instance for later use. Refs will be used heavily throughout the course and this will be a good point to explain differences between regular variables and refs in function components, similar to instance variables in classes. 

### GPX Format
Now we can create an example Polyline, which is essentially a collection of connected gps coordinates. What about actual routes, where do we get GPS information from? For that purpose we can use GPX file format. It’s a common format for storing GPS coordinates and can be exported from apps like strava/komoot or tools like Garmin connect. 

We’ll copy an example GPX file to the public folder and extend the route details fetching method, using fetch and async/await.

### Converting to GeoJSON
Now we have an xml string with GPX data, but we can’t really work with it. We need to convert it to JSON first. One way would be to simply use some kind of xml-to-json library, but thankfully there are specialized libraries which can convert GPX to a format called GeoJSON.

Let’s take a look at @tmcw/togeojson library. It does exactly what we want but has one problem - it doesn’t provide TS typings. So we have 2 options here - either completely ignore it or create our own typings. We will do the latter, because GeoJSON is a deeply nested format and typings will make our life so much easier when working with it.

Ideally this conversion step should be done on the backend to minimize performance issues on the frontend, but we’ll simply append conversion step to the fetching function.

### Displaying route on the map
Now that we have nicely formatted data we can finally display the route. Putting polyline initialization into an existing useEffect would be a bad idea because it’s only executed once after initial mount, but we want to execute it whenever the route changes. The beauty of useEffect is that we can have multiple, with different dependency arrays.

Remove route from map using useEffect cleanup function.  

### Loading states and creating custom hooks
While adding loading states to both api calls we’ll be forced to repeat the same stateful logic to our components. At this point we could extract that logic into a custom hook and tackle the problem with stale closures at the same time.

### Add starting point marker
We’ll use leaflet divIcon to create a marker with a custom icon. We could write inline HTML or create a tiny React component and renderToString instead.

### Calculating totals and additional values:
Slope, traveled distance, time, total elevation, total descent.
We’ll use information provided in the GeoJSON file to calculate additional values using different approaches and formulas (like haversine etc). Some of these calculations are pretty straightforward, but for example total elevation/descent can be tricky. 

## Module 3: The elevation profile 
- Creating basic charts with D3
- Making React and D3 work together
- Responsive SVGs
- SVG paths, groups and rectangles
- Using D3 scales and shapes
- Generating axes with D3

### D3 intro
The elevation profile looks like a regular area chart with one caveat - the interactive position indicator. D3 is a data visualisation library which is flexible enough to provide us with such functionality. In fact, elevation profiles on Strava/Komoot websites are also built using D3.

D3 is a huge library and we won’t be covering it in detail. But to build our area chart we’ll still need to become familiar with multiple core D3 concepts: scales, shapes and axes. We’ll also explore how D3 can not only coexist with React but also benefit from its declarative nature. 

### Responsive SVG, viewBox and aspect ratio
D3 uses SVG under the hood so first of all we’ll need to create an svg container. Instead of defining fixed width and height we’ll use viewBox attribute which defines the aspect ratio and makes all lengths and coordinates inside scale accordingly. This will make sure our chart and all elements inside of it (even text) will stay responsive on lower screen sizes.

### Scales and shapes
When starting working with scales there is no immediate visual feedback and students might struggle to understand what exactly they are doing. That’s why before we start we’ll resort to sketching again. We’ll talk about ranges and domains and experiment with dummy data. 

Then we’ll generate an area chart using created scales and svg path element. D3 generates a list of path commands which we can be fed directly into the d attribute. At this point we can also experiment with different shapes and curves.

### X & Y Axes
To make some space for axes on our chart we’ll need to add padding. We should highlight here that the values we are working with aren’t pixels and scale indefinitely. We’ll also introduce g element which can be used to apply transforms to all of its children.

Now we have two options. We can either manually paint axes with React and foreignObject or use D3 helpers like axisBottom/axisLeft to automatically construct them wherever we like. The only issue with the second approach is that we have to give D3 control over certain areas of the DOM and we lose a certain amount of flexibility. In our case though these helpers are totally fine to use.

To give D3 DOM access we’ll use refs and wait till React renders everything to screen, then call axis helpers inside useEffect.

## Module 4: Adding interactive position indicator to elevation profile
- Attaching events to SVG elements
- Using D3 invert and bisect to calculate exact geo coordinates from mouse position 
- Using foreignObject to include HTML in SVG
- Using context to avoid prop drilling

### Attaching events and calculating exact position
To display an interactive marker we’ll first need to attach mousemove event somewhere and a seemingly obvious solution would be the path element itself. However the event would only fire inside of the filled area. Instead we’ll create a transparent rect and attach events to it. 

Next we’ll get the relative pointer X value and from it determine the exact position on the route using a combination of D3 invert and bisect. 

### Lifting state up
Both Map and ElevationProfile components will need to communicate indicator position to each other so we’ll need to lift the state up to the Details component. The Map component will then react to these state changes and adjust marker position on the route accordingly.

### Displaying indicator
Now we can create the indicator itself. We can either do it using svg elements like rect, text and tspan or utilize foreignObject which allows us to include regular html elements. The cool thing is no matter which approach we’ll use we’ll still get autoscaling on smaller resolutions out of the box.

### Using context to avoid prop drilling
We can notice in the Details component that we are repeatedly passing down the same props down the tree, sometimes even a couple levels deep. This is called prop drilling and can be fixed by using context. At this point we could also mention that prop drilling is why context was introduced to React in the first place, not to replace redux.

## Module 5: Adding interactive position indicator to map

- Attaching events to Leaflet Polyline and Map
- Use sphere-knn to do fast nearest-neighbor lookups on a sphere
- Calculating pixel distance between points on the map
- Use React Devtools Profiler to find and fix rendering performance issues

Now we need the opposite of what we did in the previous module - we want moving over the route on the map to display position indicator on the elevation profile. The first solution that comes to mind is to simply attach mousemove event to the leaflet Polyline. That won’t work as expected. Firstly it won’t fire reliably, secondly what we actually need is to show the indicator when we are “within certain pixel distance from the route”.

To achieve that we’ll need to instead attach mousemove event to the whole map, calculate distance between route and mouse position and show indicator if it’s near enough. Essentially our route is a collection of geographic points and we want to check which one of these points is near a given latitude/longitude pair. There are several npm libraries which can help us do that, for example sphere-knn.

We’ll use the library to determine the closest polyline point on mousemove, then calculate pixel distance between the mouse cursor and that point and if the distance is small enough – update indicator position. And by hiding the indicator in case the distance is too long we’ll finally be able to achieve desired functionality.
 
Our application seems to be quite performant, but what about weaker devices? Up until recently I was using 4gb 2013 Macbook Air and modern web was quite a different experience. By using throttling in Chrome performance panel we can simulate weaker CPUs and then use React Devtools Profiler to find performance bottlenecks and fix them.
