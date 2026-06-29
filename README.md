# Travlr Getaways

This is my CS 465 Full Stack Development final project at Southern New Hampshire University. It is a travel booking site built on the MEAN stack: MongoDB, Express, Angular, and Node.js. The site has a customer-facing public side that uses Express with Handlebars (HBS) templates, and an admin single-page application (SPA) built with Angular for managing trips. All data is stored in MongoDB and accessed through a RESTful API with JSON Web Token (JWT) authentication on the protected endpoints.

## How to run it

1. Make sure MongoDB is running locally on the default port.
2. From the project root: `npm install` then `npm start` to launch the Express server on port 3000.
3. From `app_admin/`: `npm install` then `ng serve` to launch the Angular admin app on port 4200.
4. The customer site is at `http://localhost:3000` and the admin app is at `http://localhost:4200`.

## Reflection Journal

### Architecture

**Compare and contrast the types of frontend development used in this full stack project.**

This project uses three different kinds of frontend code. The first is Express with Handlebars templates. When a customer visits a page, the Express server runs a controller, fetches data from the database through the API, and uses HBS to render an HTML page on the server. The browser receives a complete HTML page. This works well for content-heavy pages that do not change much, but every click means a full page reload.

The second is plain JavaScript running in the browser, which is used in a few places to add small bits of interactivity to the static HBS pages. It is fine for tiny things but does not scale well as the user interface (UI) gets more complex.

The third is the Angular single-page application. The admin side is built this way. Angular ships one HTML shell plus a compiled JavaScript bundle. After that, the browser does all the rendering. Navigating between screens does not reload the page, forms are bound to data with `[(ngModel)]`, and reusable components like `trip-card` and `navbar` keep the code organized. The SPA feels much closer to a desktop application than the HBS side does.

The big tradeoff is that HBS is simpler and faster to ship for read-heavy public pages, while Angular is much richer for screens that involve a lot of user input and interaction.

**Why did the backend use a NoSQL MongoDB database?**

MongoDB stores data as documents, not rows, and those documents look almost exactly like JavaScript objects. Since the rest of the stack (Express, Node.js, Angular) is all JavaScript, the data does not need to be translated between worlds. Mongoose was used as an Object Document Mapper (ODM) on top of MongoDB, which let me define schemas for the trip and user collections without losing the flexibility of a document database. NoSQL was also a good fit because the trip data has nested fields and may change shape over time. A traditional relational database would have forced me to design rigid tables up front.

### Functionality

**How is JSON different from JavaScript and how does JSON tie together the frontend and backend?**

JSON stands for JavaScript Object Notation. It is a text-based data format, not a programming language. JSON looks a lot like a JavaScript object, but it is just data. Any language can read and write JSON, not just JavaScript.

In this project, JSON is the language that the frontend and backend use to talk to each other. The Angular admin app sends JSON in the body of POST and PUT requests, the Express controllers receive that JSON, store it in MongoDB as BSON (binary JSON), and send JSON back as responses. Whether the request comes from the Angular SPA, from the HBS pages, or from a tool like Postman or curl, the format is always JSON. This is why the same `/api/trips` endpoint can serve all of them.

**Provide instances where you refactored code to improve functionality. What are the benefits of reusable UI components?**

There were several refactoring moments in this project. In the earlier modules, the static customer-facing pages were rewritten to use HBS templates so that shared parts like the header, footer, and trip cards could be reused as partials instead of being copy-pasted into every page. Later, the entire admin side was rewritten as an Angular SPA, which replaced the simple static pages with components like `trip-listing`, `trip-card`, `add-trip`, and `edit-trip`. In Module 7, the authentication code was refactored again to use a JWT interceptor that automatically adds the token to outgoing requests, instead of having every service manually attach the header.

The benefits of reusable UI components are concrete. The `trip-card` component is used both on the trip listing page and inside the admin side, so a change to the trip card layout only needs to be made in one place. The same is true for the navbar. If I had built the project without components, I would have to update the same HTML in three or four files every time a small style change came in.

### Testing

**Explain your understanding of methods, endpoints, and security in a full stack application.**

HTTP methods are the verbs that the frontend uses to tell the backend what it wants to do. GET means read data, POST means create new data, PUT means update existing data, and DELETE means remove data. Each endpoint is a URL that the backend listens on and maps to a specific controller function. In this project, `/api/trips` handles getting the list of all trips and creating new trips, while `/api/trips/:tripCode` handles getting and updating a single trip.

Testing was done using Postman and curl. For a normal endpoint like `GET /api/trips`, testing was straightforward: send the request and check the response. Once authentication was added in Module 7, testing got more involved. Every protected endpoint had to be tested in two ways: without the Authorization header (expect 401 Unauthorized) and with a valid JSON Web Token (expect success). This is called negative testing, and it is just as important as positive testing because it confirms that the security layer is actually doing its job.

Security in a full stack application is layered. The JWT proves who the user is. The middleware on the backend checks the token before letting the request reach the controller. The frontend interceptor automatically attaches the token to every outgoing request so the components do not have to think about it. Cross-Origin Resource Sharing (CORS) headers are configured on the Express server so that the Angular app at `http://localhost:4200` is allowed to talk to the Express server at `http://localhost:3000`. Without those CORS headers, the browser would block the requests for security reasons.

### Reflection

**How has this course helped you in reaching your professional goals?**

This course gave me hands-on experience with every layer of a real full stack web application instead of just one slice of it. Before this class, I had touched parts of the stack separately (some HTML and CSS here, some database queries there) but I had not built something that connected all the layers together. The Travlr Getaways project forced me to think about how the same trip data flows from MongoDB through Mongoose and Express, gets shaped into JSON, travels across the network, and ends up as a card in an Angular component. That mental model is exactly what employers want from a full stack developer.

Some specific skills I built or improved in this course:

- Setting up and running a Node.js and Express server
- Designing Mongoose schemas and querying MongoDB
- Building a RESTful API and testing it with Postman and curl
- Refactoring static HTML into HBS templates with partials
- Building an Angular SPA with standalone components, services, and the router
- Implementing JWT-based authentication on both the server and the client
- Using a JWT interceptor to keep the security code clean and reusable
- Reading server logs and browser developer tools to debug a multi-tier system
- Using git branches per module to keep the project history organized

These are skills that come up in almost every full stack job description I have read, and being able to point to a working project where I used all of them makes me a stronger candidate.
