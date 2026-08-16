## Route Guards (DAY-9)

## What is a Route Guard?

A route guard is a check that runs before a route opens. It decides if the user is allowed to see that page or not.

If the check passes, the page opens. If it fails, the guard stops the page and sends the user somewhere else, usually the login page.

In simple words: a route guard is like a gate in front of a page. It asks "is this user allowed here?" before showing anything.

**Why we need it:** Without a guard, anyone can open a private page (like `/profile`) just by typing the URL, even without logging in. The guard checks if a token exists first, and blocks the page if it does not.

## Making a Guard

A guard is a service that implements `CanActivate`. We can create it using the CLI:

```
ng generate guard auth
```

(choose CanActivate when it asks)

```typescript
// auth.guard.ts
import { Injectable } from "@angular/core";
import { CanActivate, Router } from "@angular/router";
import { AuthService } from "./auth.service";

@Injectable({ providedIn: "root" })
export class AuthGuard implements CanActivate {
  constructor(private auth: AuthService, private router: Router) {}

  canActivate(): boolean {
    if (this.auth.getToken()) {
      return true; // token exists, allow the page
    }
    this.router.navigate(["/login"]);
    return false; // no token, block and redirect
  }
}
```

`canActivate()` returns `true` to allow the route, `false` to block it. We check if a token exists — if yes, allow the page. If not, send the user to login and block the page.

## Adding the Guard to a Route

We attach the guard to any route we want to protect:

```typescript
// app-routing.module.ts
import { AuthGuard } from "./auth.guard";

const routes: Routes = [
  { path: "login", component: LoginComponent },
  {
    path: "profile",
    component: ProfileComponent,
    canActivate: [AuthGuard], // protected
  },
  { path: "", redirectTo: "login", pathMatch: "full" },
];
```

Now before `/profile` opens, Angular runs `AuthGuard` first. Logged in → page shows. Not logged in → redirected to login. The same guard can be added to multiple routes.

## The Opposite Guard: NoAuthGuard

There is also a reverse case. If a user is already logged in, they should not see the login or signup page again. Typing `/login` while already logged in should send them to profile instead.

`NoAuthGuard` does this — it allows the page only when there is **no** token.

```typescript
// no-auth.guard.ts
import { Injectable } from "@angular/core";
import { CanActivate, Router } from "@angular/router";
import { AuthService } from "./auth.service";

@Injectable({ providedIn: "root" })
export class NoAuthGuard implements CanActivate {
  constructor(private auth: AuthService, private router: Router) {}

  canActivate(): boolean {
    if (!this.auth.getToken()) {
      return true; // no token, allow login/signup page
    }
    this.router.navigate(["/profile"]);
    return false; // already logged in, redirect to profile
  }
}
```

It is the mirror image of `AuthGuard`:
- `AuthGuard` → allows the page when token **exists**
- `NoAuthGuard` → allows the page when token **does not exist**

Add it to login and signup routes:

```typescript
const routes: Routes = [
  { path: "login", component: LoginComponent, canActivate: [NoAuthGuard] },
  { path: "signup", component: SignupComponent, canActivate: [NoAuthGuard] },
  { path: "profile", component: ProfileComponent, canActivate: [AuthGuard] },
  { path: "", redirectTo: "login", pathMatch: "full" },
];
```

Together, these two guards make sure:
- A logged-out user cannot reach private pages
- A logged-in user does not see the login form again

## The Full Auth Flow

1. Login checks the user and gives back a token, which we save.
2. The interceptor attaches that token to every request, so the server knows who is asking.
3. The guard protects the pages, so only a logged-in user can open them.

```
User visits a route
        |
        v
  Does route have
  a guard attached?
    /         \
  No           Yes
   |             |
   v             v
Page opens   Guard runs canActivate()
                  |
          Token check happens
            /            \
      Token exists    No token
       (AuthGuard)   (AuthGuard)
           |               |
           v               v
      Allow (true)   Redirect to login
      Page opens      Block (false)
```

## Types of Guards

| Guard | Runs when | Used for |
|---|---|---|
| CanActivate | before entering a route | block a page if not logged in |
| CanDeactivate | before leaving a route | warn about unsaved changes |
| CanActivateChild | before entering child routes | protect a group of pages at once |
| Resolve | before a route loads | fetch data so the page opens ready |

`CanDeactivate` is useful when a user tries to leave a half-filled form — it can ask "You have unsaved changes, leave anyway?"

## Key Points

- A route guard is a gate that runs before a route and decides if the user may enter.
- `CanActivate` is the most common guard: return `true` to allow, `false` to block.
- `AuthGuard` checks for a token; if missing, it redirects to login and blocks the page.
- Attach it using `canActivate: [AuthGuard]` on the route.
- `NoAuthGuard` is the reverse — it keeps logged-in users away from login/signup pages, sending them to profile instead.
- Full auth story: login gets the token → interceptor sends it → guard protects the pages.
 
  
