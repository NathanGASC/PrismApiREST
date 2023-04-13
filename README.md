# PrismApiREST
PrismApiREST is a module which work as a middleware for Express. You need to know how Prisma work first before using this module. This module will use the PrismaClient generated by Prisma to work. PrismApiREST is here to turn a prisma file into a prisma rest api. For each defined model in your prisma file, you will have a route GET, POST, PUT, DELETE to manipulate it.

## How to use
To use PrismApiREST, you will need a express server and a prisma file like below. If you execute this server, you will be able to retreive users at `http://localhost:3000/user`. Of course we have no data in user.
```typescript
import express from "express"
import { PrismApiREST } from "prismapirest"
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

const app = express()
app.use(new PrismApiREST().rest({
    "prisma": {
        "client": prisma
    },
    "api":{
        "composer":{
            "user":{
                "posts": true
            },
            "post":{
                "author": true
            }
        },
        "validation":{
            
        },
        "pagination":{

        }
    }
}))

const port = 3000
app.listen(port,()=>{
    console.log(`Server listen on port ${3000}`)
})
```

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  posts     Post[]
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
}
```