## Login & Profile(DAY-8)

## What we learnt today

 the login and profile flow. This is how it works:

1. User types email and password on the login page and clicks Login.
2. The app sends this data to the server.
3. Server sends back a token. We save this token.
4. App moves to the profile page.
5. Profile page asks the server for the user's data.
6. An interceptor automatically attaches the saved token to this request.
7. Server checks the token and sends back the user's data, which we show.

**Auth Service** — one service to talk to the server and handle the token:
- `login()` sends email and password, gets a token back.
- `saveToken()` saves the token in localStorage.
- `getToken()` reads the saved token.
- `logout()` removes the token.

**Login Component** — on submit, calls `login()`. Inside subscribe: save the token first, then navigate to profile. Save happens first because it is instant (just localStorage), so the token is already there by the time the profile page asks for data.

**Profile Service** — just asks the server for the profile. It does not add the token itself.

**Interceptor** — this is the important part. It sits between every request and the server. It checks if a token exists, and if yes, adds it to the request header as `Authorization: Bearer <token>`. This is why the profile service does not need to add the token manually — every request passes through the interceptor automatically.

**Routing** — `/login` and `/profile` paths are mapped to their components. Empty path redirects to login. A `<router-outlet>` in the main template shows whichever page is active.

**Profile Component** — calls `getProfile()` in `ngOnInit()`, saves the result in `user`, shows it with `*ngIf="user"` so it does not try to show data before the server replies.

One thing sir pointed out: right now anyone can open `/profile` directly by typing the URL, even without logging in. A route guard is needed to stop that — but that is not done yet, it's the next step.

---

## What I did today

I built four forms in `resumeflow-practice`.

### 1. Login form

```ts
export class LoginComponent {
  username: string = "";
  email: string = "";

  onSubmit() {
    console.log(this.username, this.email);
  }
}
```

```html
<form (ngSubmit)="onSubmit()">
  <input name="username" [(ngModel)]="username" placeholder="Name" />
  <input name="email" [(ngModel)]="email" placeholder="Email" />
  <button type="submit">Login</button>
</form>
```

Used `[(ngModel)]` for two-way binding on the inputs, and `(ngSubmit)` to call `onSubmit()`, which logs the values to the console.

### 2. Signup form

```ts
export class SignupComponent {
  signupForm = new FormGroup({
    username: new FormControl('', Validators.required),
    email: new FormControl('', [
      Validators.required,
      Validators.email
    ])
  });

  onSubmit() {
    console.log(this.signupForm.value);
  }
}
```

```html
<form [formGroup]="signupForm" (ngSubmit)="onSubmit()">
  <input formControlName="username" placeholder="Name" />
  <input formControlName="email" placeholder="Email" />
  <button type="submit" [disabled]="signupForm.invalid">
    Submit
  </button>
</form>
```

Switched to reactive forms here — `FormGroup` and `FormControl` instead of `ngModel`. Added `Validators.required` and `Validators.email`. Submit button stays disabled with `[disabled]="signupForm.invalid"` until the form is valid.

### 3. College registration form

```ts
export class CollegeRegistrationComponent {
  collegeForm = new FormGroup({
    studentName: new FormControl('', Validators.required),
    email: new FormControl('', [
      Validators.required,
      Validators.email
    ]),
    course: new FormControl('', Validators.required),
    gender: new FormControl('', Validators.required),
    semester: new FormControl('', Validators.required)
  });

  onSubmit() {
    console.log(this.collegeForm.value);
  }
}
```

```html
<form [formGroup]="collegeForm" (ngSubmit)="onSubmit()">
  <input type="text" formControlName="studentName" placeholder="Student Name" />
  <input type="email" formControlName="email" placeholder="Email" />

  <select formControlName="course">
    <option value="">Select Course</option>
    <option value="bca">BCA</option>
    <option value="bba">BBA</option>
    <option value="btech">B.Tech</option>
  </select>

  <div>
    <label>
      <input type="radio" formControlName="gender" value="male" />
      Male
    </label>
    <label>
      <input type="radio" formControlName="gender" value="female" />
      Female
    </label>
  </div>

  <select formControlName="semester">
    <option value="">Select Semester</option>
    <option value="1">1st</option>
    <option value="2">2nd</option>
    <option value="3">3rd</option>
    <option value="4">4th</option>
    <option value="5">5th</option>
    <option value="6">6th</option>
  </select>

  <button type="submit" [disabled]="collegeForm.invalid">
    Register
  </button>
</form>
```

Bigger form with more field types — a `select` dropdown for course, radio buttons for gender, another `select` for semester. All required, all wired through `formControlName`.

### 4. Feedback form

```ts
export class FeedbackFormComponent {
  feedbackForm = new FormGroup({
    name: new FormControl('', Validators.required),
    email: new FormControl('', [
      Validators.required,
      Validators.email
    ]),
    rating: new FormControl('', Validators.required),
    feedback: new FormControl('', Validators.required),
    recommend: new FormControl('', Validators.required)
  });

  onSubmit() {
    console.log(this.feedbackForm.value);
  }
}
```

```html
<section class="form-container">
  <h2>Feedback Form</h2>

  <form [formGroup]="feedbackForm" (ngSubmit)="onSubmit()">
    <div class="form-field">
      <label for="name">Name</label>
      <input type="text" id="name" formControlName="name" placeholder="Enter your name" />
      <small *ngIf="feedbackForm.get('name')?.touched && feedbackForm.get('name')?.invalid">
        Name is required
      </small>
    </div>

    <div class="form-field">
      <label for="email">Email</label>
      <input type="email" id="email" formControlName="email" placeholder="Enter your email" />
      <small *ngIf="feedbackForm.get('email')?.touched && feedbackForm.get('email')?.invalid">
        Enter a valid email
      </small>
    </div>

    <div class="form-field">
      <label>Rating</label>
      <label><input type="radio" formControlName="rating" value="1"> 1</label>
      <label><input type="radio" formControlName="rating" value="2"> 2</label>
      <label><input type="radio" formControlName="rating" value="3"> 3</label>
      <label><input type="radio" formControlName="rating" value="4"> 4</label>
      <label><input type="radio" formControlName="rating" value="5"> 5</label>
      <small *ngIf="feedbackForm.get('rating')?.touched && feedbackForm.get('rating')?.invalid">
        Please select a rating
      </small>
    </div>

    <div class="form-field">
      <label for="feedback">Your Feedback</label>
      <textarea id="feedback" formControlName="feedback" placeholder="Write your feedback"></textarea>
      <small *ngIf="feedbackForm.get('feedback')?.touched && feedbackForm.get('feedback')?.invalid">
        Feedback is required
      </small>
    </div>

    <div class="form-field">
      <label>Would you recommend us?</label>
      <label><input type="radio" formControlName="recommend" value="yes"> Yes</label>
      <label><input type="radio" formControlName="recommend" value="no"> No</label>
      <small *ngIf="feedbackForm.get('recommend')?.touched && feedbackForm.get('recommend')?.invalid">
        Please select an option
      </small>
    </div>

    <button type="submit" [disabled]="feedbackForm.invalid">
      Submit Feedback
    </button>
  </form>
</section>
```

This one goes further than the other three — added per-field error messages using `*ngIf="field.touched && field.invalid"`, so the error only shows after the user has clicked into that field and left it empty or invalid, not immediately on page load. Fields include text, email, radio (rating 1-5), a textarea, and radio again for recommend.

---
