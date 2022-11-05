# realtime-todo-task-management-app-nestjs-and-angular
*Please Remember that this is a hobby project and done in the freetime*

## Requirements
1. Docker

## How to run the Project & additional Docker Commands
1. Run all services: `docker-compose up`

Additional Docker Commands:
1. Rebuild all Images (also run e.g. if you have a new dependency): `docker-compose build --no-cache`
2. Run services with rebuild: `docker-compose up -d --build api frontend postgres postgres_admin`
3. Remove all images: `docker rmi -f $(docker images -a -q)`
4. Remove all containers: `docker rm -vf $(docker ps -a -q)`

## Setup steps in detail

###
TODO: Health check with NestJS Terminus  
TODO: Health check with Terminus for Database

### Docker & Docker-Compose (Database)
1. Create a "docker-compose.yml" file at the top level of the project
2. Add a "postgres" database service, so that NestJS can connect to it

### [Video 1] NestJS Backend API
1. Install the NestJS cli globally `npm install -g @nestjs/cli`
2. Create the NestJS backend api `nest new todo-api --skip-git`
3. Make sure that all dependencies are installed: `cd todo-api` and `npm install`
4. Run the project and try to connect to the api via Postman `npm run start:dev`
5. Generate all needed modules
   1. Auth Module: `nest generate module auth`
   2. Todo Module: `nest generate module todo`
   3. All Modules should now be generated and also be imported into the main module
6. Generate the User Resource (inside the User Module)
   1. Create the plain User Resource (Module, Controller, Service, Entity and DTO (Domain Transfer Object)) with  
      `cd /src/user` and then `nest generate resource user`
   2. Move the generated Classes to new folders, for example the service into a "services" folder in the user module
7. Set a global prefix to your NestJS api, so that everything is served under the base path "/api"
   1. Open the main.ts and add `app.setGlobalPrefix('api');` accordingly
8. Add Typeorm and the connection to the database
   1. Add access to environment variables via `npm i --save @nestjs/config`
   2. Add the `ConfigModule.forRoot({isGlobal: true})` to the imports of our "app.module"
   3. Add the Typeorm Packages and the Postgres ("pg") packages via `npm install --save @nestjs/typeorm typeorm pg`
   4. Add the Typeorm Module with the configurations ``
9. Create a "Dockerfile" so that the api can be started via docker-compose as a service

### [Video 2] Angular Frontend
1. Install the Angular CLI globally `npm install -g @angular/cli`
2. Create the Angular Frontend with `ng new angular-frontend`
3. Make sure to install all deps: `cd angular-frontend` and `npm install`
4. Set Up our main modules
   1. Create Public Module (here we handle Register and Login of the users): `ng generate module public`
   2. Create Private Module (handles Stuff after login, jwt protected): `ng generate module private`
3. Create dir `/models` and dir `/services`
4. Create a Service to get a value from the todo-api: `ng generate service test` and modify the return from the backend as Json
5. Create a proxy.conf to proxy all requests when running `ng serve` for `/api` against  
   our in container todo-api under `http://api:3000` & add it to our angular.json
6. Add a Dockerfile & then add the service to our docker-compose file
7. Fix the start script in the angular package.json to run inside a container to `"start": "ng serve --host=0.0.0.0 --port 4200"`  
   Explanation: https://stackoverflow.com/questions/46778868/ng-serve-not-working-in-docker-container
   Other solution could be to use for ex. nginx

### [Video 3] NestJS User Api (Register and Login)
To be able to later build our Realtime Todo Api, we always need to know who the user is and what roles/rights does he has.
For this we provide Endpoints for Login and Register.
The Login Endpoint will return a JWT, which will then be attached to every request against the backend and it should always be validated.

1. Read the Docs about Authentication: https://docs.nestjs.com/security/authentication
2. Install the dependencies via: `npm install --save @nestjs/passport passport passport-jwt passport-local bcrypt @nestjs/jwt`
3. Create the Jwt Strategy under path `/auth/strategies/jwt.strategy.ts` (read more in the docs at 1.)
4. Create the Auth Service under path `/auth/services/auth.service.ts`
5. Create a Guard under path `/auth/guards/jwt.guard.ts`
6. Update imports/providers/... for the AuthModule (make sure to export the authService, so we can use it in other modules)
7. Make sure to add the environment variable for the JWT_SECRET to the docker-compose file
8. Import the AuthModule into the UserModule
9. Create the User Entity & the User Interface
10. Install the package class-validator to check our dto values `npm i --save class-validator class-transformer`
11. Make sure to enable the class validation in main.js by adding: `app.useGlobalPipes(new ValidationPipe());`
12. Create the dtos for login and register
13. Create the login and register Endpoints and Service Functions
14. Run the Api with docker and test it with Postman

