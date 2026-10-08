# Week 3 record

Your name: Khawaja Abdul Moiz
Date: Octuber, 8, 2026

Fill this in as you work, rather than at the end. Where you are unsure, write
that you are unsure and say why. That is worth more than a confident sentence you
cannot support.

---

## 1. The finding I chose

State which of the three the tool reported.

- File: sql.yaml
- Line: 29
- What the tool said about it:
 This SQL statement is built by joining pieces of text together. If any of those pieces came from outside the application, the database will read it as part of the command rather than as a value.

## 2. What an assistant told me

Say which assistant you asked, and what it said in a sentence or two.

- Assistant used: 
Copilot assistant in VS Code.

- Its explanation, in your own words: 
The scanner flagged SQL text assembled by concatenating strings. If user-provided search text is included this way, the database may treat parts of it as SQL syntax instead of as a search value.

- One thing it asserted that I had not verified at that point: 
That the directory search value comes from the request and reaches the SQL query. I later checked that code path and tested searches in the running application.

## 3. What the code shows

Answer all four. If you cannot answer one, say so.

**Where does the data come from?**

The search text comes from the `q` query parameter in the request to `/directory`. The handler reads it from `req.query.q`.

**What happens to it on the way?**

The handler passes that text to `searchDirectory`. The function adds it between `%` wildcard characters by concatenating it into a SQL string.

**Where does it become dangerous?**

It becomes potentially dangerous when the completed string is passed to `db.prepare(sql)`. Because the search text is part of the SQL text rather than a bound parameter, specially formed input could be interpreted as SQL syntax.

**What stands in the way?**

The `/directory` route requires the user to be signed in. That limits who can reach the search, but it does not make the query safe from a signed-in user's input.

## 4. What the running application shows

Record both. A single result on its own proves nothing.

**Ordinary case**

- What I entered: 
`Keane`
- What came back:
The page showed one colleague: Bob Keane.

**The case I was testing for**

- What I entered: 
`' OR 1=1 -- ` (including a space after the two hyphens)
- What came back: 
The page showed all three colleagues: Alice Nolan, Bob Keane, and Morgan Doyle.
- How this differs from the ordinary case: 
The ordinary search returned only Bob Keane. The crafted search returned everyone, even though the text does not match their names or departments, showing that the input changed how the SQL query was interpreted.

## 5. My answer

Delete the two that do not apply.

**Real**

**Why, in two or three sentences.** 
Write for somebody who has not seen any of this. The directory search puts a user's search text directly into the SQL command. In the running app, a crafted search returned all three colleagues instead of only matching results, confirming that the input can change the query's behavior.

**What would change my mind.** 
If new information would alter this answer, say what. If a repeat test against this same version and seeded data did not reproduce the result, or if the running code were shown to use bound parameters instead of concatenating the search text, I would revisit this conclusion.

**How far this answer reaches.** 
What you established applies to a particular page,
a particular set of data and this version of the application. Say what you have
shown, and be careful not to claim more.

I tested the signed-in directory search in the local Atrium app with its three seeded colleagues. This shows the behavior of that page and version; it does not establish what happens on other pages, with other data, or in a different deployed version.

## 6. Back to the assistant

The thing you noted in section 2, that you had not verified at the time.

- Did I check it?
Yes. I checked the handler and tried a crafted search in the directory.
- Was it right? 
Yes. The search value reaches the SQL query, and my crafted input made the page show all three colleagues instead of only matching results.

---

## Optional, if you had time

The other two findings matched the same rule. Why are they not the same situation?
Two sentences.
