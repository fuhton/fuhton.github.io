---
layout: post
title: Mocking Express Request and Response
---

```typescript
# tests.spec.ts

import { Request, Response } from 'express';

const req = {} as Request;
const res = {} as Response;

describe('' => {});
```

This is the easiest way I've found to mock, type, and test Request/Response in TypeScript.