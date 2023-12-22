---
title: "Svelte Kit Route Guarding With Firebase"
date: 2023-12-22T13:09:18+01:00
categories:
  - frontend
  - software
tags:
  - svelte
  - sveltekit
  - route guarding
keywords:
  - svelte
  - sveltekit
  - route guarding
comments: true
draft: false
# weight: 1
# aliases: ["/first"]
# author: ["Me", "You"] # multiple authors
# showToc: true
# TocOpen: false
# hidemeta: false
# description: "Desc Text."
# disableHLJS: true # to disable highlightjs
# disableShare: false
# hideSummary: false
# searchHidden: true
# ShowReadingTime: true
# ShowBreadCrumbs: true
# ShowPostNavLinks: true
# ShowWordCount: true
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
# editPost:
#     URL: "https://github.com/<path_to_repo>/content"
#     Text: "Suggest Changes" # edit text
#     appendFilePath: true # to append file path to Edit link
---

While looking for documentation on how to deal with protected routing on Svelte Kit using Firebase as a backend, I found disperse resources about it.

I found a pretty nice video on YT explaining the application of server hooks to route guarding from Huntabyte:

{{< youtube K1Tya6ovVOI >}}

So far the best resource I could find. However, I found some caveats that needed to be addressed:

1. Client-side navigation could not be handled correctly
2. Reactiveness on logging out from within any protected page

Both problems can be solved by using a simple +layout.server.ts for all the protected routes:

```ts
import type { LayoutServerLoad } from "./$types";

// Despite the load function is empty, it is required in order to trigger hooks.server.ts always there is a client side navigation to a protected route
export const load = (async () => {
  return {};
}) satisfies LayoutServerLoad;
```

And also using `invalidateAll();` at the end to the sign in and log out methods to force the server hooks to run.

This solution takes for granted using Firebase server side authentication. Of course, you can also use the client side auth alongside.

Here is an example of the route guards logic on the `hooks.server.ts`:

```ts
import { adminAuth } from "$lib/server/admin";
import { redirect, type Handle } from "@sveltejs/kit";
import { sequence } from "@sveltejs/kit/hooks";

const auth: Handle = async ({ event, resolve }) => {
  const sessionCookie = event.cookies.get("__session");

  try {
    const decodedClaims = await adminAuth.verifySessionCookie(sessionCookie!);
    event.locals.userID = decodedClaims.uid;
    console.log("found user id", decodedClaims.uid);
  } catch (e) {
    event.locals.userID = null;
    return resolve(event);
  }

  return resolve(event);
};

const routeGuards: Handle = async ({ event, resolve }) => {
  // Authentication guards
  if (event.url.pathname.startsWith("/dashboard")) {
    if (!event.locals.userID) {
      throw redirect(303, "/login");
    }
  }
  if (event.url.pathname === "/login") {
    if (event.locals.userID) {
      throw redirect(303, "/dashboard");
    }
  }
  // Standard guards
  if (event.url.pathname === "/dashboard") {
    throw redirect(303, "/dashboard/invoices");
  }
  return resolve(event);
};

export const handle = sequence(auth, routeGuards);
```

Hopefully, this enables you to implement a robust and simple route guarding implementation on your Svelte Kit Firebase app.
