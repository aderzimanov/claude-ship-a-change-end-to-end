# Notes

**Plan.** The plan was to add `PUT /users/:id`: check the id exists (404 if not), validate that
`name` and `email` are both present (400 if not), then update the user through a new
`store.updateUser(id, { name, email })` helper, mirroring the existing `getUserById`/`createUser`
conventions in `db/store.js` and the 404/400 response shapes already used by `GET /:id` and
`POST /`. I approved the plan as written — the only thing I added on top of it was explicitly
confirming, before building, that the not-found check has to run *before* the validation check,
since the test suite's 404 case sends a fully valid body and its 400 case targets an id that
already exists.

**Model.** Sonnet 5. The change is small and the patterns to follow were already established in
the file (one more route, one more store function), so a fast, cheap model was the right fit —
no need for a heavier reasoning model on a task this contained.

**Commits.** Two commits: one for the working endpoint (`db/store.js` + `routes/users.js`
together, since the store helper only exists to support the route — splitting them further would
have been artificial), and a second for this `NOTES.md`. Keeping the feature and the write-up
separate makes the diff for the actual behavior change easy to review on its own.

**Review.** I ran a self-review before pushing. It found no correctness bugs — the not-found and
validation paths, and the 200 response body, all match what the tests expect. It flagged three
duplication/efficiency nitpicks: the 404 check and the validation check each repeat logic already
inlined in `GET /:id` and `POST /`, and `updateUser` re-looks-up the user internally after the
route already looked it up once. I left these as-is: the file is three small routes long, it
already duplicates these same checks between the existing handlers, and factoring out a shared
helper for a two-line check would be over-engineering relative to the size of this codebase.
