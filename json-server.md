# Lesson 14: Add HTTP communication to your app

This tutorial demonstrates how to integrate HTTP and an API into your app.

Up until this point your app has read data from a static array in an Angular service. The next step is to use a JSON server that your app will communicate with over HTTP. The HTTP request will simulate the experience of working with data from a server.

* Estimated time: ~15 minutes

What you'll learn
Your app will use data from a JSON server

## Step 1 - Configure the JSON server
JSON Server is an open source tool used to create mock REST APIs. You'll use it to serve the housing location data that is currently stored in the housing service.

Install json-server from npm by using the following command.

```bash 
npm install -g json-server
```

In the root directory of your project, create a file called `db.json`. This is where you will store the data for the json-server.

Open `db.json` and copy the following code into the file
content_copy
```json
{
{
  "locations": [
    {
      "id": 0,
      "name": "Acme Fresh Start Housing",
      "city": "Chicago",
      "state": "IL",
      "photo": "https://angular.io/assets/images/tutorials/faa/bernard-hermant-CLKGGwIBTaY-unsplash.jpg",
      "availableUnits": 4,
      "wifi": true,
      "laundry": true
    },
    {
      "id": 1,
      "name": "A113 Transitional Housing",
      "city": "Santa Monica",
      "state": "CA",
      "photo": "https://angular.io/assets/images/tutorials/faa/brandon-griggs-wR11KBaB86U-unsplash.jpg",
      "availableUnits": 0,
      "wifi": false,
      "laundry": true
    },
    {
      "id": 2,
      "name": "Warm Beds Housing Support",
      "city": "Juneau",
      "state": "AK",
      "photo": "https://angular.io/assets/images/tutorials/faa/i-do-nothing-but-love-lAyXdl1-Wmc-unsplash.jpg",
      "availableUnits": 1,
      "wifi": false,
      "laundry": false
    },
    {
      "id": 3,
      "name": "Homesteady Housing",
      "city": "Chicago",
      "state": "IL",
      "photo": "https://angular.io/assets/images/tutorials/faa/ian-macdonald-W8z6aiwfi1E-unsplash.jpg",
      "availableUnits": 1,
      "wifi": true,
      "laundry": false
    },
    {
      "id": 4,
      "name": "Happy Homes Group",
      "city": "Gary",
      "state": "IN",
      "photo": "https://angular.io/assets/images/tutorials/faa/krzysztof-hepner-978RAXoXnH4-unsplash.jpg",
      "availableUnits": 1,
      "wifi": true,
      "laundry": false
    },
    {
      "id": 5,
      "name": "Hopeful Apartment Group",
      "city": "Oakland",
      "state": "CA",
      "photo": "https://angular.io/assets/images/tutorials/faa/r-architecture-JvQ0Q5IkeMM-unsplash.jpg",
      "availableUnits": 2,
      "wifi": true,
      "laundry": true
    },
    {
      "id": 6,
      "name": "Seriously Safe Towns",
      "city": "Oakland",
      "state": "CA",
      "photo": "https://angular.io/assets/images/tutorials/faa/phil-hearing-IYfp2Ixe9nM-unsplash.jpg",
      "availableUnits": 5,
      "wifi": true,
      "laundry": true
    },
    {
      "id": 7,
      "name": "Hopeful Housing Solutions",
      "city": "Oakland",
      "state": "CA",
      "photo": "https://angular.io/assets/images/tutorials/faa/r-architecture-GGupkreKwxA-unsplash.jpg",
      "availableUnits": 2,
      "wifi": true,
      "laundry": true
    },
    {
      "id": 8,
      "name": "Seriously Safe Towns",
      "city": "Oakland",
      "state": "CA",
      "photo": "https://angular.io/assets/images/tutorials/faa/saru-robert-9rP3mxf8qWI-unsplash.jpg",
      "availableUnits": 10,
      "wifi": false,
      "laundry": false
    },
    {
      "id": 9,
      "name": "Capital Safe Towns",
      "city": "Portland",
      "state": "OR",
      "photo": "https://angular.io/assets/images/tutorials/faa/webaliser-_TPTXZd9mOo-unsplash.jpg",
      "availableUnits": 6,
      "wifi": true,
      "laundry": true
    }
  ]
}
```

Save this file.

Time to test your configuration. From the command line, at the root of your project run the following commands.

content_copy
```bash
json-server --watch db.json
```

In your web browser, navigate to the http://localhost:3000/locations and confirm that the response includes the data stored in db.json.

If you have any trouble with your configuration, you can find more details in the official documentation.

## Step 2 - Update service to use web server instead of local array
The data source has been configured, the next step is to update your web app to connect to it use the data.

In `src/app/housing.service.ts`, make the following changes:

Update the code to remove housingLocationList property and the array containing the data.

Add a string property called url and set its value to 'http://localhost:3000/locations'

```
url = 'http://localhost:3000/locations';
```

This code will result in errors in the rest of the file because it depends on the housingLocationList property. We're going to update the service methods next.

Update the getAllHousingLocations function to make a call to the web server you configured.

```js
async getAllHousingLocations(): Promise<HousingLocation[]> {
  const data = await fetch(this.url);
  return await data.json() ?? [];
}
```

The code now uses asynchronous code to make a GET request over HTTP.

For this example, the code uses `fetch`. For more advanced use cases consider using `HttpClient` provided by Angular.
Update the getHousingLocationsById function to make a call to the web server you configured.

```js
async getHousingLocationById(id: number): Promise<HousingLocation | undefined> {
  const data = await fetch(`${this.url}/${id}`);
  return await data.json() ?? {};
}
```

Once all the updates are complete, your updated service should match the following code.

Final version of housing.service.ts

```js
import { Injectable } from '@angular/core';
import { HousingLocation } from './housinglocation';

@Injectable({
  providedIn: 'root'
})
export class HousingService {

  url = 'http://localhost:3000/locations';

  async getAllHousingLocations(): Promise<HousingLocation[]> {
    const data = await fetch(this.url);
    return await data.json() ?? [];
  }

  async getHousingLocationById(id: number): Promise<HousingLocation | undefined> {
    const data = await fetch(`${this.url}/${id}`);
    return await data.json() ?? {};
  }

  submitApplication(firstName: string, lastName: string, email: string) {
    console.log(firstName, lastName, email);
  }
}
```

## Step 3 - Update the components to use asynchronous calls to the housing service

The server is now reading data from the HTTP request but the components that rely on the service now have errors because they were programmed to use the synchronous version of the service.

In `src/app/home/home.component.ts`, update the constructor to use the new asynchronous version of the getAllHousingLocations method.

```js
constructor() {
  this.housingService.getAllHousingLocations().then((housingLocationList: HousingLocation[]) => {
    this.housingLocationList = housingLocationList;
    this.filteredLocationList = housingLocationList;
  });
}
```

In `src/app/details/details.component.ts`, update the constructor to use the new asynchronous version of the getHousingLocationById method.

```js
constructor() {
  const housingLocationId = parseInt(this.route.snapshot.params['id'], 10);
  this.housingService.getHousingLocationById(housingLocationId).then(housingLocation => {
    this.housingLocation = housingLocation;
  });
}
```
Save your code.

Open the application in the browser and confirm that it runs without any errors.