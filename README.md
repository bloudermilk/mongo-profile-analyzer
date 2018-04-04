# MongoDB Profile Analyzer

Get to know your MongoDB queries!

```
signup-step-1.profile.json

109	Total

39	users            query: 38	update: 1
17	reservations     query: 12	update: 4	command: 1
15	tickets          query: 12	update: 2	command: 1
11	trips            query: 7	getmore: 4
```

## System Requirements

* MongoDB and related command line tools
* Ruby

## Usage

Start a MongoDB console, taking care to replace `YOUR_DATABASE` with your
desired database.

```bash
$ mongo YOUR_DATABASE
```

Load the following function into the MongoDB console. Its purpose is to enable
and reset the profile collection so that you can create separate profiles
over time. It will also return the count of queries that were in the profile
before it was emptied.

```javascript
function resetProfile() {
  let count = db.system.profile.count()
  db.setProfilingLevel(0)
  db.system.profile.drop()
  db.createCollection("system.profile", { capped: true, size: 400000000 })
  db.setProfilingLevel(2)
  return count
}
```

When you're ready to start recording, call the function you pasted into the
MongoDB console earlier.

```javascript
> resetProfile()
5100
```

When you want to save a profile, run the following back in your *OS console* (i.e.
not Mongo). This will save the analysis of `YOUR_DATABASE` to a file in the
current directory with a filename starting with `YOUR_FILENAME`.

```bash
$ mongoexport --jsonArray --pretty -d YOUR_DATABASE -c system.profile -o YOUR_FILENAME.profile.json
```

To save more profiles, run the previous two commands (`resetProfile()` then
`mongoexport`) each time you'd like to save a new one.

Finally, when you're ready to look at your results, you can run the command
included in this repository from your OS console.

```bash
$ ./analyze-profiles
```

This will look for `*.profile.json` files in the current directory, then print
an analysis of each file to the console.
