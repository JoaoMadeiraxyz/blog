---
title: "NestJS Integrado Com Firestore"
description: "Este é um guia de como integrar o firestore cloud database com o framework NestJS."
pubDate: "Jan 19 2025"
heroImage: "/blog-placeholder-2.jpg"
---

Firebase oferece soluções baseadas em nuvem muito úteis, sendo uma delas o [Firestore](https://firebase.google.com/docs/firestore?hl=pt-br).
Um banco de dados NoSQL em nuvem usado para armazenar e sincronizar dados para desenvolvimento tanto no lado do cliente quanto no lado do servidor.

Então, se o Firestore é tão útil, por que não usá-lo com um dos frameworks Node.js mais populares para o lado do servidor, o [NestJS](https://nestjs.com/)?
Juntos, ambos podem ser muito poderosos para ajudá-lo a construir uma aplicação funcional de forma rápida.

## Começando do início

Para fazer isso, você primeiro precisa iniciar seu projeto NestJS. Então, vamos lá.

Neste exemplo, vamos usar o NestJS CLI para iniciar nosso projeto.
Para criar o projeto com o Nest CLI, execute os seguintes comandos.
Eu estarei usando o pnpm para este exemplo, mas você pode usar o gerenciador de pacotes de sua escolha.

```shell
$ pnpm i -g @nestjs/cli
$ nest new nome-do-projeto
$ cd nome-do-projeto
```

O NestJS é um framework bem opinativo, então se você não está acostumado com sua arquitetura, sugiro que leia a [documentação oficial](https://docs.nestjs.com/first-steps).

Agora, precisamos instalar o Firebase Admin Node.js SDK e também o módulo dotenv.

```shell
$ pnpm i firebase-admin dotenv --save
```

Após instalar as dependências, podemos acessar o arquivo principal, que pode ser encontrado em `src/main.ts`, e configurar o dotenv.

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

Agora, precisamos criar um arquivo de configuração para o Firebase no seguinte caminho: `src/firebase/firebase.config.ts`, e dentro dele colocar o seguinte conteúdo:

```js
import * as firebase from "firebase-admin";

export function initializeFirebase() {
  const serviceAccount = JSON.parse(process.env.FIREBASE_SERVICE_ACCOUNT);

  firebase.initializeApp({
    credential: firebase.credential.cert(serviceAccount),
    databaseURL: "https://<sua-url-do-banco-de-dados>.firebaseio.com",
  });
}
```

Agora, voltamos para o `src/main.ts` e inicializamos o Firebase.

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

Estamos quase terminando a configuração aqui, mas agora precisamos ir para o [console do Firebase](https://console.firebase.google.com/) e acessar o seu projeto Firebase.

Se você ainda não tem um projeto Firebase, pode começar um gratuitamente seguindo a [documentação oficial](https://firebase.google.com/docs/firestore/quickstart).

Agora, você precisará obter a conta de serviço do seu projeto. Para fazer isso, vá para a **Configuração do projeto**, **Contas de serviço**, e na guia **Firebase Admin SDK** clique no botão **Gerar nova chave privada**. Isso irá baixar um arquivo JSON com a conta de serviço do seu projeto, esses dados são muito sensíveis, então tenha cuidado para não compartilhá-los com ninguém.

Agora, no diretório raiz do seu projeto, crie um arquivo `.env` e cole o JSON dentro de uma variável de ambiente chamada `FIREBASE_SERVICE_ACCOUNT` em uma única linha, assim:

```
FIREBASE_SERVICE_ACCOUNT={ "type": "service_account", "project_id": "example-app", "private_key_id": "000000000000000000000000000000000000", "private_key": "-----BEGIN PRIVATE KEY-----sua chave privada-----END PRIVATE KEY-----\n", "client_email": "firebase-adminsdk-xxx@example-app.iam.gserviceaccount.com", "client_id": "000000000000000", "auth_uri": "https://accounts.google.comxxxxxx", "token_uri": "https://xxxx.googleapis.com/token", "auth_provider_x509_cert_url":"https://www.googleapis.com/xxx", "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/xxxxgserviceaccount.com" }
```

Agora seu projeto NestJS está integrado ao banco de dados Firestore. Você pode seguir tanto a [documentação do NestJS](https://docs.nestjs.com/controllers) para criar seus endpoints, quanto a [documentação do Firebase Admin](https://firebase.google.com/docs/reference/admin/node/firebase-admin.firestore.md#firebase-adminfirestore_module) para trabalhar com o Firestore.

Você também pode ver o exemplo de projeto que criei para uma demonstração mais completa de como escrever e ler dados do Firestore com NestJS no meu [GitHub](https://github.com/JoaoMadeiraxyz/firebase-integration-nestjs/tree/main).
