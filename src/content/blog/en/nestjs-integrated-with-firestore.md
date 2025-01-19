---
title: "NestJS Integrated With Firestore"
description: "This is a guide on how to integrate firestore cloud database into NestJS."
pubDate: "Jan 19 2025"
heroImage: "/blog-placeholder-2.jpg"
---

Firebase offers very useful cloud based solutions, one of them being [firestore](https://firebase.google.com/docs/firestore?hl=pt-br).
A NoSQL cloud database used to store and sync data for client and server-side development.

So, if Firestore is that useful, why not using it with one of the most popular server-side Node.js Frameworks, [NestJS](https://nestjs.com/)?
Together both can be really strong to help you build a working application really fast.

## Starting from the beggining

In order to do that, you need first to start your NestJS project. So let's go down to it.

In this example we will be using the NestJS CLI to start our project.
To scaffold the project with the Nest CLI, run the following commands.
I will be using pnpm for this example, but you can use the package manager of your choice.

```shell
$ pnpm i -g @nestjs/cli
$ nest new project-name
$ cd project-name
```

NestJS is a very opinative framework, so if you are not used to its architecture I suggest you to read the [official documentation](https://docs.nestjs.com/first-steps).

Now, we need to install the Firebase Admin Node.js SDK and also the dotenv module.

```shell
$ pnpm i firebase-admin dotenv --save
```

After installing the dependencies, we can access the main file that can be found at `src/main.ts` and setup dotenv.

```js
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";
import * as dotenv from "dotenv";

dotenv.config();

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

Now, we need to create a config file for firebase in the following path: `src/firebase/firebase.config.ts` and inside of it put the following content:

```js
import * as firebase from "firebase-admin";

export function initializeFirebase() {
  const serviceAccount = JSON.parse(process.env.FIREBASE_SERVICE_ACCOUNT);

  firebase.initializeApp({
    credential: firebase.credential.cert(serviceAccount),
    databaseURL: "https://<your-database-url>.firebaseio.com",
  });
}
```

Now we go back to `src/main.ts` and initialize our firebase.

```js
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";
import * as dotenv from "dotenv";
import { initializeFirebase } from "./firebase/firebase.config";

dotenv.config();
initializeFirebase();

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

We are almost done with the setup here, but now we need to go to our [firebase console](https://console.firebase.google.com/) and access your firebase project.

If you still don't have a firebase project you can start one for free by following the [official documentation](https://firebase.google.com/docs/firestore/quickstart).

Now, you will need to get the service account for your project. To do that you will go to the **Configurations of the project** tab, **service accounts**, and in the **Firebase Admin SDK** tab you can click on the **Generate new private key** button. It will download a JSON file with your project service account, this data is very sensitive, so be sure to not share it with anyone.

Now, in the root of your project create a .env file and paste the JSON inside a environment variable called FIREBASE_SERVICE_ACCOUNT in a single line just like this:

```
FIREBASE_SERVICE_ACCOUNT={ "type": "service_account", "project_id": "example-app", "private_key_id": "000000000000000000000000000000000000", "private_key": "-----BEGIN PRIVATE KEY-----your private key-----END PRIVATE KEY-----\n", "client_email": "firebase-adminsdk-xxx@example-app.iam.gserviceaccount.com", "client_id": "000000000000000", "auth_uri": "https://accounts.google.comxxxxxx", "token_uri": "https://xxxx.googleapis.com/token", "auth_provider_x509_cert_url":"https://www.googleapis.com/xxx", "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/xxxxgserviceaccount.com" }
```

And now your NestJS project is integrated with your firestore database. You can follow both the [NestJS documentation](https://docs.nestjs.com/controllers) in order to create your endpoints, and the [firebase admin](https://firebase.google.com/docs/reference/admin/node/firebase-admin.firestore.md#firebase-adminfirestore_module) documentation for firestore.

You can also see the example project I have created for a better demonstration on how to write and read data from firestore with NestJS on my [GitHub](https://github.com/JoaoMadeiraxyz/firebase-integration-nestjs/tree/main).
