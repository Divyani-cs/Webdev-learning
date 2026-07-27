
## 1. What is an API?

Think of a restaurant.

- **You (the client)** sit at the table and order food.
- **The waiter (the API)** takes your order to the kitchen.
- **The kitchen (the server)** cooks the food and gives it to the waiter.
- The waiter brings the food back to you.

You never go into the kitchen yourself. The waiter (API) is the messenger between you and the kitchen. That's exactly what an API does between an app and a server.

## 2. Client and Server

- The **client** always speaks first. It sends a request.
- The **server** replies with a response.
- Rule: **one request = one response.** You ask once, you get one answer back.

## 3. What's Inside a Request and a Response?

**A request has:**
- **Method** — what you want to do (like GET or POST)
- **URL** — where you are sending the request
- **Headers** — extra info (like what type of data you're sending)
- **Body** — the actual data you're sending (not always needed)

**A response has:**
- **Status** — a number that tells you what happened (success, error, etc.)
- **Headers** — extra info about the response
- **Body** — the actual data you get back

⚠️ **Always read the status code first.** The body can say "success" even when something went wrong, so don't trust the body alone.

## 4. JSON — The Common Language

- **JSON** is the format almost all APIs use to send data.
- `response.json()` → turns the response into data you can use.
- `JSON.stringify()` → turns your data into text to send.

## 5. The Five HTTP Methods

| Method | What it does |
|--------|--------------|
| GET    | Read data |
| POST   | Create new data |
| PUT    | Replace data completely |
| PATCH  | Update part of the data |
| DELETE | Remove data |

## 6. GET vs POST

- **GET**: data goes in the **URL** (like `?search=cat`)
- **POST**: data goes in the **body**
- POST is **not safe to repeat** — if you click submit twice, you might create two orders instead of one. GET is safe to repeat, it just reads data.

## 7. Status Code Families

Status codes are grouped by their first digit:

- **1xx** — Just letting you know, still processing
- **2xx** — Success ✅
- **3xx** — Redirect, go somewhere else
- **4xx** — You made a mistake ❌
- **5xx** — The server made a mistake 💥

**Common ones to remember:**
- `200` OK — it worked
- `201` Created — new thing was made
- `204` No Content — worked, but nothing to send back
- `400` Bad Request — you sent something wrong
- `401` Unauthorized — you're not logged in / no key
- `403` Forbidden — you're logged in but not allowed
- `404` Not Found — doesn't exist
- `429` Too Many Requests — slow down
- `500` Server Error — server broke, not your fault

## 8. REST URL Design

Simple rule: **URLs are nouns, methods are verbs.**

Good example:
```
GET    /users        → get all users
GET    /users/5      → get user 5
POST   /users        → create a user
PUT    /users/5      → replace user 5
DELETE /users/5      → delete user 5
```

Don't do this: `/getUser` or `/deleteUser5` — the method already tells you the action, the URL should just name the "thing."

## 9. Using fetch()

You can use `.then()`:
```js
fetch("https://api.example.com/users")
  .then(res => res.json())
  .then(data => console.log(data));
```

Or the cleaner `async/await`:
```js
async function getUsers() {
  const res = await fetch("https://api.example.com/users");
  const data = await res.json();
  console.log(data);
}
```

**Why await twice?**
1. First `await` waits for the server to respond.
2. Second `await` waits for the body to be turned into usable data (JSON).

## 10. Sending Data with fetch()

```js
fetch("https://api.example.com/users", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ name: "Alex" })
});
```

## 11. Error Handling

⚠️ Important: `fetch()` does **NOT** throw an error for 404 or 500. It only fails on a real network problem (like no internet).

So you must check it yourself:
```js
const res = await fetch(url);
if (!res.ok) {
  console.log("Something went wrong:", res.status);
}
```

## 12. Auth Basics

- **API keys** and **Bearer tokens** prove who you are to the server.
- Keep secret keys in a `.env` file.
- **Never** put keys in:
  - your frontend code
  - git / GitHub

Anyone can see frontend code and git history, so secrets don't belong there.

## 13. The Server Side (Express Example)

```js
app.get("/users", (req, res) => {
  res.status(200).json({ users: [] });
});

app.post("/users", (req, res) => {
  res.status(201).json({ message: "User created" });
});
```

## 14. Live Demo

We tested all of this live using the **PokeAPI** in the browser console — sending real GET requests and reading the JSON responses.

---

## 🎯 Key Takeaways

1. **Read the status code first** — the body can lie.
2. `fetch()` only throws on a **network failure** — always check `res.ok` yourself.
3. **URLs are nouns, methods are verbs.**
4. **Secret keys never go in frontend code or git.**
