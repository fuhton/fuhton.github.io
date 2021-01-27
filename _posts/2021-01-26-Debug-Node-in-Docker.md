---
layout: post
title: Debug Node in Docker
---

One of our NodeJS apps, written in TypeScript, started to chew up memory in production. The first solution was to toss some more memory at it of course, but that's only gonna work for so long.

So while we were throwing more resources at it (and it kept gobbling everything up), I set out to profile the app and see if I could see anything (which I couldn't).

A few things to know about the app. It's a Node app written with TypeScript. We deploy using a Docker file and run the app locally through a combination of docker-compose (composing our db and some environment variables) and nodemon (for watching the files and rebuilding).

We never modified this setup from when it was a Flow app (another long story) so it has all the bones, it just needed to be reconfigured.

## Can I run node inspect with ts-node?

The answer is no. Not worth the effort. `ts-node` is a great tool, but ultimately leads to more headaches.

Instead of

```typescript
ts-node ./index.ts
```

it'll be the below.

```typescript
node -r ts-node/register ./index.ts
```

Now you'll be able to pipe any command into the nodemon. If you're running nodemon, you can now pass in any node flag.

```bash
$ yarn run nodemon --inspect
```

```json
# package.json
  ...
  "nodemonConfig": {
    "watch": [
      "..."
    ],
    "ext": "ts",
    "exec": "node -r ts-node/register ./index.ts"
  },
  ...
```

## But Docker

Yes, Docker. Running a project locally through Docker/docker-compose always brings some issues and this is the case here too. In order for your debugging tool to use the port docker-compose needs to expose it. 

```yml
...
  ...
    ...
    ports:
      - "9229:9229"
```

Once you expose it, you need to tell the node inspector to use the global 0.0.0.0 address so it can be located by your debugger tool.

That'll end up looking like

```bash
$ node --inspect=0.0.0.0:9229
```
or 
```bash
$ yarn run nodemon --inspect=0.0.0.0:9229
```

Once you know how to get it done it's obvious. But like most things, it's not obvious if you haven't walked the path before.